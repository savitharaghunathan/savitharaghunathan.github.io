---
layout: post
title: "What I Learned Building an AI Rule Generator for Konveyor"
date: 2026-06-05 12:00:00
description: >
    I built a pipeline that generates Konveyor analyzer rules from a migration guide. This post is one rule walking through it, from a Spring Boot section to a Kantra pass, and the design that fell out of watching it fail.
tags: AI, Konveyor, Agentic Development
categories: AI, Agentic Development
---

[Konveyor](https://www.konveyor.io/) is a modernization platform for migrating legacy apps. The analysis runs on rules that explain what's being flagged, what changed, and what needs attention.

I've written rules in the pre-AI era for some applications, and it took me many hours of testing and debugging. I have firsthand experience of how painful it is. So this project meant a whole lot to me: I wanted the Konveyor-specific craft of writing rules to live in the pipeline, so you don't have to be a rules SME to create them. You still need a migration guide though.

The code lives in [konveyor/ai-rule-gen](https://github.com/konveyor/ai-rule-gen). I first tried a skills + MCP server path, and briefly had two ways of generating rules. MCP made it too easy to pull extra tool output into the same conversation I was trying to keep small, so I dropped it. What I kept is skills plus sub-agents, and an orchestrator that only decides what runs next.

## One rule, from the guide to a passing test

The canonical input is the [Spring Boot 4.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide). One section looks like this:

> Spring Boot’s `@MockBean` and `@SpyBean` support has been removed in this release, in favor of `@MockitoBean` and `@MockitoSpyBean` support.
>
> If your tests are using `@MockBean` and `@SpyBean` as fields in test classes, you can consider a direct replacement.

That is a real migration. People have this annotation in their tests. A Konveyor rule should find it.

The rule-writer agent does not emit YAML. It emits a pattern object that has to match a JSON schema, because `cmd/construct` is the thing that actually builds the rule file. For `@MockBean` that contract looks like this:

```json
{
  "source_pattern": "@MockBean",
  "target_pattern": "@MockitoBean",
  "source_fqn": "org.springframework.boot.test.mock.mockito.MockBean",
  "location_type": "ANNOTATION",
  "rationale": "@MockBean removed; use @MockitoBean from Spring Framework instead",
  "complexity": "medium",
  "category": "mandatory",
  "concern": "testing",
  "provider_type": "java",
  "documentation_url": "https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide#mockbean-and-spybean-removal"
}
```

`cmd/construct` turns that into rule YAML. The agent never chooses the `ruleID`, the effort number, or the label format. Construct maps `complexity: medium` to `effort: 5`, stamps the source/target labels, and writes a sequential ID. Konveyor rules flag the old API; `target_pattern` lands in the description and message, not in the `when` clause. This is the rule the pipeline actually produced, now in [`evals/spring-boot3-to-spring-boot4/rules/testing.yaml`](https://github.com/konveyor/ai-rule-gen/blob/main/evals/spring-boot3-to-spring-boot4/rules/testing.yaml):

```yaml
- ruleID: spring-boot3-to-spring-boot4-00860
  description: '@MockBean removed; use @MockitoBean from Spring Framework instead'
  category: mandatory
  effort: 5
  labels:
    - konveyor.io/source=spring-boot3
    - konveyor.io/source=spring-boot
    - konveyor.io/target=spring-boot4
    - konveyor.io/target=spring-boot
    - konveyor.io/generated-by=ai-rule-gen
  when:
    java.referenced:
      pattern: org.springframework.boot.test.mock.mockito.MockBean
      location: ANNOTATION
```

Then a test-generator agent has to produce tests that will make Kantra fire this rule. The first version it wrote was, essentially:

```java
import org.springframework.boot.test.mock.mockito.MockBean;
```

That looks plausible if you are an LLM. The FQN is right there. Kantra still reported **0 incidents**: expected the rule to match, unmatched.

The reason is a Kantra / JDTLS quirk I had to learn the hard way, and it is now a row in the validator's [Java fix table](https://github.com/konveyor/ai-rule-gen/blob/main/agents/rule-validator/references/languages/java/fix-strategies.md). A `java.referenced` rule with `location: ANNOTATION` does not fire on an import. The annotation has to be applied to a class, field, or method. Import-only test data is a silent miss.

The validator agent is not allowed to investigate. It gets the failing rule ID, the `.test.yaml` filename, and the Kantra error, then looks up the condition type in that table. For `java.referenced` + `ANNOTATION` the row says: apply the annotation, don't just import it. So it rewrites the test to:

```java
import org.springframework.boot.test.mock.mockito.MockBean;

public class MockBeanTest {
    @MockBean
    private GreetingService greetingService;
}
```

Re-run Kantra on that one test file. Incident found. Rule passes.

That loop is the whole pipeline in miniature: guide section → pattern JSON → constructed YAML → bad test data → a specific Kantra miss → a lookup fix → a passing rule. I did not want the validator to reason about whether the rule was wrong. The rule is authoritative. If the test doesn't trigger it, the test is wrong.

The exception is when the test is already correct and Kantra still can't match it. A real version string like `6.4.0.Final` is what projects actually ship. If we rewrote it to `6.4.0` to force a green result, we'd be testing a version that doesn't exist on Maven Central. Those get classified as an engine limitation and left alone.

## Why one agent couldn't hold all of this

The first major challenge was keeping the context light. When you have one big agent trying to do everything, that is read a migration guide, understand API changes, write rule YAML, generate test code, debug Kantra failures, the context window fills up fast. The quality of output degrades as the conversation grows.

Sub-agents fixed that for me. Each one starts fresh with only what it needs. The rule writer gets guide sections and extraction instructions. The test generator gets the rule YAML and a test scaffold. The validator gets failing rules and a lookup table of fix strategies. Nobody carries another agent's leftovers. I would have used sub-agents even if I never ran them in parallel. Isolation was the point

I also kept the orchestrator thin on purpose. It doesn't read the files the sub-agents will read. It doesn't hold migration guide content. It passes file paths, collects structured outputs, and kicks off the next step. Every time I tried to make it "smarter" like taking a peek at the guide, rewrite a pattern, second-guess a fix, it picked up more context and started drifting.

## Agents inventing their own JSON

Initially, every sub-agent invented its own format. The rule writer returned patterns one way, the test generator expected them another way, and things broke at the boundaries. You'd get three stages down before noticing that `location_type` was missing, or that `dependency_name` used a colon instead of a dot.

So I added contracts: JSON schemas for what each agent gets and what it has to return. Before a sub-agent runs, the orchestrator checks inputs against the schema with `cmd/contract-validate`. After it returns, it checks outputs. Fail validation and the step fails right there, with a clear error, instead of passing garbage into construct or scaffold.

The rule-writer contract is small on purpose. It says the agent must return `patterns_count`, and may return `language`, `output_file`, `suspected_kantra_limitations`. If it returns a blob of YAML instead, the pipeline stops, which helps me to see which boundary failed.

## The boring parts are Go

I also split tasks that need exact answers from tasks that need reasoning. For the exact ones I wrote small Go CLI tools and called them from the pipeline like parsing a migration guide into sections, building rule YAML from patterns, scaffolding test directories, validating XML, running Kantra tests. None of that needs an LLM. It needs the same transformation every time.

I ended up with 13+ Go tools for the deterministic work. `cmd/construct` is the one that turns the `@MockBean` JSON above into YAML. `cmd/merge-patterns` is the one that combines parallel extracts. `cmd/sanitize` exists because generated XML comments would occasionally be malformed and take down a whole test group. The agents only do what LLMs are good at, like reading a migration guide and figuring out what changed, writing compilable test code that hits a specific pattern, and diagnosing why a test failed.

Every time I moved something from "let the LLM decide" to "let the deterministic tool run it," reliability went up. The model doesn't need to guess a rule ID format or which directory a test file belongs in. Eg, the ruleid `spring-boot3-to-spring-boot4-00860` is constructed, not authored.

## Splitting a long guide

For bigger migration guides I split the work. The guide gets divided into balanced chunks by top-level heading groups, and I spawn up to 1to N (where 5 is the max value for N) rule-writer agents in parallel, each on its own chunk. Test generation works the same way where groups get batched across parallel test-generator agents.

Each agent is independent. Chunk 2 doesn't wait on chunk 1. They write to separate files, and `cmd/merge-patterns` combines and deduplicates after everyone finishes. No shared state, no race conditions. Isolation still matters more than the parallelism.

## Where it landed

The Spring Boot 3→4 eval checked into the repo is [94 rules](https://github.com/konveyor/ai-rule-gen/tree/main/evals/spring-boot3-to-spring-boot4). A May snapshot scored 84% effective coverage. Japicmp ground truth for that migration has 999 API-level changes, so a guide-driven pipeline is not a full API diff. I review for gaps instead of assuming completeness.

What used to take me weeks of hand-written rules and cross-file debugging now goes through ingest, extract, construct, test generation, and a lookup fix loop. It is not perfect every time. It is consistent enough that I review the output instead of rebuilding every step myself.
