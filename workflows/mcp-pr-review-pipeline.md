---
type: workflow
id: mcp-pr-review-pipeline
title: MCP PR Review Pipeline
description: "Multi-agent PR review: fetch via MCP, three parallel review agents, human gate, post via MCP"
tags: [Production, Code, Review]
connections:
  - target: pr-data-fetch
    type: uses
  - target: security-review
    type: uses
  - target: quality-review
    type: uses
  - target: style-review
    type: uses
  - target: review-gate
    type: uses
  - target: review-posting
    type: uses
  - target: github-mcp
    type: runs_on
  - target: llm-service
    type: runs_on
metadata:
  estimated_duration: "30-90 seconds"
  avg_tokens: 25000
  trigger: manual
output_step: "review-posting"
composite_steps:
  - "pr-data-fetch"
  - "security-review"
  - "quality-review"
  - "style-review"
  - "review-gate"
  - "review-posting"
execution:
  - skill: "pr-data-fetch"
    step_type: "generation"
    prompt: "fetch-pr-data"
    output: { name: "pr_data", type: "text" }
  - parallel:
    - skill: "security-review"
      step_type: "review"
      prompt: "security-analysis"
      output: { name: "security_review", type: "text" }
      context:
        voice_profile: "Neutral professional tone"
        severity_threshold: "Medium"
    - skill: "quality-review"
      step_type: "review"
      prompt: "quality-analysis"
      output: { name: "quality_review", type: "text" }
      context:
        voice_profile: "Neutral professional tone"
        review_depth: "Standard"
    - skill: "style-review"
      step_type: "review"
      prompt: "style-analysis"
      output: { name: "style_review", type: "text" }
      context:
        voice_profile: "Neutral professional tone"
        style_strictness: "Standard"
  - skill: "review-gate"
    step_type: "validation"
    gate: true
    prompt: "review-gate-summary"
    output: { name: "gate_decision", type: "decision" }
  - skill: "review-posting"
    step_type: "content"
    prompt: "post-review"
    output: { name: "posted_review", type: "text" }
---

## Overview

This workflow runs a complete automated code review on a pull request using the GitHub MCP server. It fetches the PR data, runs three parallel analysis agents (security, quality, style), pauses for human approval at a gate step, then posts the approved review back to GitHub.

The **review gate** is the critical decision point — you see exactly what will be posted and can edit findings, change the verdict, or abort entirely before anything lands on the PR.

## Pipeline Stages

### Stage 1: Fetch PR Data

**Input:** {{input.pr_url}}

Using the GitHub MCP service, fetch the pull request and retrieve metadata, the full diff, changed file list, existing reviews, and CI check status.

**Output:** Structured PR data object passed to all three review agents.

### Stage 2: Parallel Review (Three Agents)

Three review agents run concurrently against the PR data:

#### 2a. Security Review

Scans changed files for injection vulnerabilities, credential exposure, insecure dependencies, unsafe API usage, and missing input validation. Configurable via the `severity_threshold` persona dial.

#### 2b. Quality Review

Evaluates logic correctness, architectural fit, error handling, test coverage, and maintainability. Configurable via the `review_depth` persona dial (Quick, Standard, Thorough).

#### 2c. Style Review

Checks naming conventions, formatting, import ordering, and project-specific patterns. Only flags issues in changed lines. Configurable via the `style_strictness` persona dial.

### Stage 3: Review Gate (Human Approval)

Execution **pauses** at the gate step. You see a consolidated summary of all findings with a proposed verdict. You respond:

- **Approve** — post the review as-is
- **Edit** — remove or modify findings before posting
- **Abort** — stop without posting anything

### Stage 4: Post Review

Using the GitHub MCP service, post the approved review:

1. Submit the review with the approved verdict (APPROVE, REQUEST_CHANGES, or COMMENT)
2. Post inline comments on specific diff lines for each finding
3. If critical security findings exist, post a separate collapsed security report

## Error Handling

- If the GitHub MCP server is unreachable, abort and report the connection error
- If any review agent fails, continue with the remaining agents and note the gap in the gate summary
- If the diff is too large (over 5,000 changed lines), the fetch step flags this and review agents batch their analysis
- If posting a specific inline comment fails, log the failure and continue with remaining comments

## Inputs

| Name | Required | Description | Example |
|------|----------|-------------|---------|
| `{{input.pr_url}}` | Yes | GitHub pull request URL or `owner/repo#number` | `https://github.com/acme/api/pull/42` |

## Outputs

| Name | Description |
|------|-------------|
| Review summary | Top-level PR review with findings count, key issues, and verdict |
| Inline comments | Per-line review comments posted on the diff |
| Security report | Collapsed security analysis (only if Critical/High findings exist) |

## Setup

Before running this workflow:

1. **GitHub MCP server** — install and configure the GitHub MCP server in your skrptiq settings. The workflow uses it to fetch PR data and post review comments.
2. **GitHub access** — ensure the MCP server has read/write access to the target repository (a personal access token with `repo` scope).
3. **Network access** — the workflow needs to reach `github.com` to fetch PR data and post results.

No specific AI provider is required beyond your configured skrptiq LLM provider.

## Provider Notes

- Security and quality review agents benefit from a model with strong reasoning capabilities.
- Style checking is lighter — a faster, smaller model works well here.
- The gate synthesis step benefits from a model that handles structured data and tables well.
- Large PRs (1,000+ changed lines) will use more tokens — consider a provider with generous context limits.

## Example Input

To test this workflow immediately after import:

```
PR URL: https://github.com/octocat/Hello-World/pull/1
```

For a more realistic test, point it at any open PR in a repository you have access to. The workflow adapts to any language and framework.
