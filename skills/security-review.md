---
type: skill
id: security-review
title: Security Review
description: "Scans changed files for security vulnerabilities, credential exposure, and unsafe patterns"
tags: [Production, Code, Review]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your code review voice — tone, directness, and technical vocabulary"
    required: false
  severity_threshold:
    label: "Severity Threshold"
    description: "Minimum severity to report — Low, Medium, High, or Critical"
    default: "Low"
    required: false
---

## Capability

Analyses changed files for security issues: injection vulnerabilities, credential exposure, insecure dependencies, unsafe API usage, and missing input validation. Reports findings with severity, location, and remediation guidance.

## When to Use

- As a parallel review agent in a PR review pipeline
- On any PR that touches authentication, authorisation, data handling, or external service calls

## What It Does

1. **Injection analysis** — checks for SQL injection, command injection, XSS, and path traversal in changed lines
2. **Credential scanning** — flags hardcoded secrets, API keys, tokens, and connection strings
3. **Dependency review** — checks new or changed dependency versions against known vulnerabilities
4. **API safety** — reviews external API calls for missing authentication, unvalidated responses, and insecure transport
5. **Input validation** — verifies user input is validated and sanitised before use
6. **Configuration review** — checks for insecure defaults, overly permissive CORS, and missing security headers

## What It Does NOT Do

- Runtime or dynamic analysis — this is static review only
- Full dependency audit — it checks changed dependencies, not the entire lockfile
- Penetration testing — it identifies patterns, not exploitable proof-of-concepts

## Outputs

Structured security findings with severity (Critical, High, Medium, Low, Info), file location, affected lines, description, and suggested fix.
