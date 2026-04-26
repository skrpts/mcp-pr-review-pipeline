---
type: skill
id: quality-review
title: Quality Review
description: "Evaluates code quality — logic, architecture, testing, error handling, and maintainability"
tags: [Production, Code, Review]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your code review voice — tone, directness, and technical vocabulary"
    required: false
  review_depth:
    label: "Review Depth"
    description: "How thorough the review should be — Quick, Standard, or Thorough"
    default: "Standard"
    required: false
---

## Capability

Evaluates the overall quality of changed code: logic correctness, architectural fit, error handling, test coverage, performance, and maintainability. Provides actionable feedback that helps the author improve the PR.

## When to Use

- As a parallel review agent in a PR review pipeline
- On any PR where code quality and correctness matter

## What It Does

1. **Logic review** — checks for off-by-one errors, race conditions, null reference risks, and incorrect control flow
2. **Architecture** — evaluates whether the change fits the existing codebase patterns and doesn't introduce unnecessary coupling
3. **Error handling** — verifies errors are caught, logged, and propagated appropriately
4. **Testing** — checks whether the change includes adequate tests and whether edge cases are covered
5. **Performance** — flags N+1 queries, unnecessary allocations, missing indexes, and hot-path inefficiencies
6. **Maintainability** — identifies overly complex functions, unclear naming, and missing documentation for non-obvious logic

## Review Depth Levels

- **Quick** — focuses on correctness and obvious issues only. Suitable for small, low-risk changes.
- **Standard** — covers correctness, architecture, error handling, and basic testing. The default for most PRs.
- **Thorough** — everything in Standard plus performance analysis, edge case enumeration, and maintainability review. Use for critical paths.

## Outputs

Structured quality findings with severity (Critical, Major, Minor, Suggestion), file location, description, and recommended change.
