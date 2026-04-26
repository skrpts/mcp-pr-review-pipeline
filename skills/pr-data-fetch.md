---
type: skill
id: pr-data-fetch
title: PR Data Fetch
description: "Retrieves pull request metadata, diff, and changed files from GitHub via MCP"
tags: [Production, Code]
connections:
  - target: github-mcp
    type: runs_on
  - target: llm-service
    type: runs_on
---

## Capability

Fetches all data needed for a pull request review using the GitHub MCP server. Produces a structured data object that feeds into the parallel review agents.

## When to Use

- As the first step in any PR review pipeline
- When you need PR metadata, diff content, and file change statistics in a single pass

## What It Does

1. **PR metadata** — calls `pull_request_read` to retrieve title, description, author, base branch, head branch, and labels
2. **Diff** — retrieves the full diff for all changed files with surrounding context
3. **Changed files** — lists modified files with addition/deletion counts and change type (added, modified, removed, renamed)
4. **Existing reviews** — fetches any prior reviews and comments to avoid duplicating feedback
5. **Check status** — retrieves CI/CD check run results for context

## Inputs

| Name | Required | Description | Example |
|------|----------|-------------|---------|
| `{{input.pr_url}}` | Yes | GitHub pull request URL or `owner/repo#number` | `https://github.com/acme/api/pull/42` |

## Outputs

Structured PR data object containing metadata, diff, file list, existing reviews, and check status. Passed to all subsequent review agents.
