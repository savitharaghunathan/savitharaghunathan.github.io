---
layout: post
title: "Agentic Development: Building an AI-Powered Rule Generation Pipeline for Konveyor"
date: 2026-06-05 12:00:00
description: >
    I built an agentic pipeline to generate Konveyor migration rules and the things that actually mattered are - keeping context light with sub-agents, JSON contracts between stages, Go tools for the boring parts, and an orchestrator that is thin.
tags: AI, Konveyor, Agentic Development
categories: AI, Agentic Development
---

*I was tasked with building an AI rule generation pipeline for Konveyor — and the real lesson turned out to be agentic design itself: how to keep context light, enforce contracts between agents, and know when to use code instead of an LLM.*

---

## The problem

I needed to build an AI pipeline that generates Konveyor rules. If you're not familiar with the platform, Konveyor is a modernization platform for migrating legacy apps. The analysis runs on rules that explain what's being flagged, what changed, and what needs attention. Writing those rules takes domain knowledge, which makes it expensive.

I've written rules in the pre-AI era for some applications, and it took me many hours of testing and debugging. I have firsthand experience of how painful it is. So this project meant a whole lot to me when I got the opportunity to improve it so that no SME knowledge is needed to create rules.

## Finding the Right Architecture

Initially I started experimenting with the idea of skills + MCP server path and having two different methods to generate rules. After some exploration, I settled on using skills and taking advantage of spinning up sub-agents using the orchestrator pattern.

The architecture has three core components: a rule writer, a test data generator, and a test validator. All of these are controlled by an orchestrator agent that acts as the conductor and it doesn't do the heavy lifting itself, it just tells the right agent what to do and when.

The pipeline has six stages: ingest the migration guide, extract patterns, generate test data, validate with Kantra (Konveyor's CLI), fix failures in the generated test data, and write a summary. At each handoff the orchestrator either calls a deterministic tool or dispatches a sub-agent, takes the output, and moves on.


## Challenge 1: Context Rot

The first major challenge was keeping the context light. When you have one big agent trying to do everything like read a migration guide, understand API changes, write rule YAML, generate test code, debug failures, the context window fills up fast. The quality of output degrades as the conversation grows. 

Sub-agents fixed that for me. Each one starts fresh with only what it needs. The rule writer gets guide sections and extraction instructions. The test generator gets the rule YAML and a test scaffold. The validator gets failing rules and a lookup table of fix strategies. Nobody carries another agent's leftovers.

I also kept the orchestrator thin on purpose. It doesn't read the files the sub-agents will read. It doesn't hold migration guide content. It passes file paths, collects structured outputs, and kicks off the next step. 


## Challenge 2: Standardizing Input/Output

Initially, every sub-agent invented its own format. The rule writer returned patterns one way, the test generator expected them another way, and things broke at the boundaries.

So I added contracts — JSON schemas for what each agent gets and what it has to return. Before a sub-agent runs, the orchestrator checks inputs against the schema. After it returns, it checks outputs. Fail validation and the step fails right there, with a clear error. No quiet garbage sliding three stages downstream.


## Put deterministic work in Go tools

I also split tasks that need exact answers from tasks that need reasoning. For the exact ones I wrote small Go CLI tools and called them from the pipeline: parsing a migration guide into sections, building rule YAML from patterns, scaffolding test directories, validating XML, running kantra tests. None of that needs an LLM. It needs the same transformation every time.

I ended up with 13+ Go tools for the mechanical work. The agents only do what LLMs are decent at: reading a migration guide and figuring out what changed, writing compilable test code that hits a specific pattern, and diagnosing why a test failed.

Every time I moved something from "let the LLM decide" to "let the Go tool run it," reliability went up. The model doesn't need to guess a rule ID format or which directory a test file belongs in.

## Parallelism without shared state

For bigger migration guides I split the work. The guide gets divided into balanced chunks by top-level heading groups, and I spawn up to five rule-writer agents in parallel and each on its own chunk. Test generation works the same way, groups get batched across parallel test-generator agents.

Each parallel agent is independent. Chunk 2 doesn't wait on chunk 1. They write to separate files, and a Go tool (`merge-patterns`) combines and deduplicates after everyone finishes. No shared state, no race conditions, no fancy coordination.

## What I'd tell myself going in

Sub-agents aren't mainly about parallelism. They're about isolation. Even serially, they're worth it because each one gets a clean window for one job.

Contracts make the pipeline debuggable. Without them you're chasing weird LLM output across stages. With them you know which boundary failed.

Pull anything deterministic out of the model. LLMs are useful for reasoning and messy language. They're a bad fit for formatting rule IDs and laying out directories.

Keep the orchestrator thin. Mine doesn't read migration guides, parse rule YAML, or edit test files. It validates contracts, chains tools, dispatches agents, and logs. Making it "smarter" usually just means more context and more drift.

## Where it landed

What used to take me weeks of hand-written rules and cross-file debugging now goes through those six stages. I trust it enough on real migration guides — not perfect every time, but consistent enough that I review the output instead of rebuilding every step myself.

For me, that’s a clear winner. I can spend less time stuck writing and debugging rules by hand, and more time reviewing something the pipeline already got most of the way there.
