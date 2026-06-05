---
layout: post
title: "Agentic Development: Building an AI-Powered Rule Generation Pipeline for Konveyor"
date: 2026-06-05 12:00:00
description: >
    This post walks through what I learned building an agentic pipeline to automate Konveyor migration rules: sub-agents for context isolation, JSON contracts between stages, Go tools for deterministic tasks, and an orchestrator that stays thin.
tags: AI, Konveyor, Agentic Development
categories: AI, Agentic Development
---

*I was tasked with building an AI rule generation pipeline for Konveyor — and the real lesson turned out to be agentic design itself: how to keep context light, enforce contracts between agents, and know when to use code instead of an LLM.*

---

## The Problem

I was tasked with building an AI rule generation pipeline for Konveyor rules. For those who don't know, Konveyor is a modernization platform that helps with migrating legacy applications. The analysis is powered by rules that provide insights into what's being flagged, what changed, and what needs attention. The problem is, to write valid rules, domain knowledge is required — which makes rule writing a costly operation.

I've written rules in the pre-AI era for some applications, and it took me many hours of testing and debugging. I have firsthand experience of how painful it is. So this project meant a whole lot to me when I got the opportunity to improve it so that no SME knowledge is needed to create rules.


## Finding the Right Architecture

Initially I started experimenting with the idea of skills + MCP server path — having two different methods to generate rules. After some exploration, I settled on using skills and taking advantage of spinning up sub-agents using the orchestrator pattern.

The architecture has three core components: a rule writer, a test data generator, and a test validator. All of these are controlled by an orchestrator agent that acts as the conductor — it doesn't do the heavy lifting itself, it just tells the right agent what to do and when.

The orchestrator runs a 6-stage pipeline: ingest the migration guide, extract patterns, generate test data, validate with kantra (Konveyor's cli), fix failures in generated test data, and produce a summary at the end. Each stage is a clear handoff — the orchestrator calls a deterministic tool or dispatches a sub-agent, collects the output, and moves on.


## Challenge 1: Context Rot

The first major challenge was keeping the context light. When you have one big agent trying to do everything — read a migration guide, understand API changes, write rule YAML, generate test code, debug failures — the context window fills up fast. The quality of output degrades as the conversation grows. The context rot is very real.

Sub-agents solved this. Each sub-agent gets a fresh context with only what it needs. The rule writer gets the guide sections and extraction instructions. The test generator gets the rule YAML and test scaffold. The validator gets the failing rules and a lookup table of fix strategies for the test data. No agent carries the baggage of another agent's work.

The orchestrator itself stays thin. It doesn't read the files that sub-agents will read. It doesn't hold migration guide content in its context. It passes file paths, collects structured outputs, and chains the next step. This was a deliberate design choice — the orchestrator is a contract-driven conductor, not a micro-manager.


## Challenge 2: Standardizing Input/Output

The next big challenge was standardizing the input/output format. When I first started, each sub-agent came up with its own format. The rule writer would return patterns one way, the test generator expected them another way, and things would break at the boundaries.

Then I standardized contracts — JSON schemas that define exactly what each agent receives and what it must return. Before every sub-agent invocation, the orchestrator validates the inputs against the contract. After the agent returns, it validates the outputs. If validation fails, the step fails immediately with a clear error — no silent corruption propagating through the pipeline.

This sounds like overhead, but it saved me countless hours of debugging. When something breaks, I know exactly where the contract was violated instead of chasing issues three stages downstream.



## Challenge 3: Reducing Hallucinations with Deterministic Tools

To reduce hallucinations, I differentiated the tasks that are deterministic from the ones that genuinely need LLM reasoning. For the deterministic parts, I built small Go CLI tools and invoked them programmatically. Things like parsing the migration guide into sections, constructing rule YAML from patterns, scaffolding test directories, validating XML syntax, running kantra tests — none of these need an LLM. They need exact, reproducible transformations.

I ended up with 13+ Go tools that handle all the mechanical work. The LLM agents only do what LLMs are actually good at: reading a migration guide and understanding what changed, generating compilable test code that triggers a specific pattern, and diagnosing why a test failed.

Every time I moved a task from "LLM decides" to "Go tool executes," the reliability went up. The LLM doesn't need to guess how to format a rule ID or which directory to write a test file to. The tool does it the same way every time.


## Parallelism: Scaling Without Chaos

For larger migration guides, I split the work across parallel sub-agents. The guide gets divided into balanced chunks by top-level heading groups, and I spawn up to 1-5 rule-writer agents in parallel — each processing its own chunk. Same for test generation: groups get batched and distributed across parallel test-generator agents.

The trick is that each parallel agent is fully independent. Rule-writer chunk 2 doesn't need chunk 1's output. They all write to separate output files, and a deterministic Go tool (merge-patterns) combines and deduplicates the results after all agents finish. No shared state, no race conditions, no coordination overhead.

## What I Learned

Building this pipeline taught me a few things about agentic development:

### Sub-agents are about context isolation

Sub-agents are not just about parallelism — they're about context isolation. Even if I ran everything serially, sub-agents would still be worth it because each one gets a clean context window focused on its specific task.

### Contracts are non-negotiable

The moment I added JSON schema validation between agents, the entire pipeline became debuggable. Without contracts, you're debugging "why did the LLM produce weird output" across a chain of multiple stages. With contracts, you know exactly which boundary failed.

### Move deterministic work out of the LLM

Every task I pulled out of the LLM that can be deterministic and into a Go tool was a task that stopped hallucinating. LLMs are powerful, but they're also unpredictable. Use them for what requires reasoning. Use code for everything else.

### The orchestrator should be thin

Mine doesn't read migration guides, doesn't parse rule YAML, doesn't edit test files. It validates contracts, chains tools, dispatches agents, and logs everything. That's it. The smarter you make the orchestrator, the more context it accumulates, and the more it drifts.


## Results

What used to take me weeks of manual rule writing and cross-file debugging now flows through six bounded stages — ingest, extract, generate, validate, fix, report. The pipeline reached a point where I trusted it on real migration guides: not perfectly every time, but consistently enough to matter. I review what comes back; I don't rebuild every step by hand.

That was the outcome I actually cared about — less "can an LLM write a rule?" and more "can I design a system where agents, tools, and contracts carry the work that needs time and expertise?"