---
type: prompt
id: fetch-pr-data
title: Fetch PR Data
description: "Retrieves and structures pull request data from GitHub via MCP"
tags: [Production, Code]
inputs:
  pr_url:
    label: "Pull Request URL"
    description: "GitHub pull request URL or owner/repo#number"
    example: "https://github.com/acme/api/pull/42"
    required: true
    type: text
connections:
  - target: pr-data-fetch
    type: derived_from
metadata:
  output_format: structured
  prompt_type: task
---

## Purpose

Fetches all pull request data needed for a multi-agent review. Produces a structured object that the parallel review agents consume.

## Prompt

You are a data retrieval agent. Using the GitHub MCP server, fetch all data for the pull request specified below.

### Steps

1. Call `pull_request_read` to retrieve the PR metadata: title, description, author, base branch, head branch, labels, and current status
2. Retrieve the full diff — all changed files with surrounding context lines
3. List all modified files with their change type (added, modified, removed, renamed) and line counts (additions, deletions)
4. Fetch any existing reviews and review comments on this PR, so subsequent agents can avoid duplicating feedback
5. Retrieve check run results (CI/CD status) for context

### Input

- **Pull request:** {{input.pr_url}}

### Output Format

Return a structured object with these sections:

```
metadata:
  title, description, author, base, head, labels, status, url

diff:
  Full diff content for all changed files

files:
  - path, change_type, additions, deletions

existing_reviews:
  - author, state, body, comments[]

checks:
  - name, status, conclusion
```

### Error Handling

- If the PR URL is invalid or the repository is not accessible, report the error clearly
- If the diff is too large (over 5,000 changed lines), note this in the metadata so downstream agents can batch their analysis
