---
type: skill
id: style-review
title: Style Review
description: "Checks code style, naming conventions, formatting, and project-specific patterns"
tags: [Production, Code, Review]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your code review voice — tone, directness, and technical vocabulary"
    required: false
  style_strictness:
    label: "Style Strictness"
    description: "How strict to be with style rules — Lenient, Standard, or Strict"
    default: "Standard"
    required: false
---

## Capability

Reviews changed code for style consistency: naming conventions, formatting, import ordering, comment quality, and adherence to project-specific patterns. Only flags issues in changed lines — existing violations in untouched code are out of scope.

## When to Use

- As a parallel review agent in a PR review pipeline
- When consistent code style matters to the team

## What It Does

1. **Naming** — checks variable, function, class, and file names against the project's conventions
2. **Formatting** — flags inconsistent indentation, line length, bracket placement, and whitespace
3. **Imports** — checks import ordering, unused imports, and circular dependency risks
4. **Comments** — flags missing documentation for public APIs, outdated comments, and commented-out code
5. **Patterns** — identifies deviations from established project patterns (e.g. using callbacks where the project uses async/await)
6. **Auto-fix notes** — indicates which issues can be resolved by running the project's formatter or linter

## Strictness Levels

- **Lenient** — only flags clear violations and auto-fixable issues. No opinions on subjective style choices.
- **Standard** — flags violations plus inconsistencies with the surrounding code. The default.
- **Strict** — everything in Standard plus subjective recommendations for clarity and consistency.

## Outputs

Structured style findings with severity (Error, Warning, Info), file location, rule description, and whether the issue is auto-fixable.
