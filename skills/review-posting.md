---
type: skill
id: review-posting
title: Review Posting
description: "Posts the approved review to GitHub via MCP — summary comment, inline comments, and verdict"
tags: [Production, Code]
connections:
  - target: github-mcp
    type: runs_on
  - target: llm-service
    type: runs_on
---

## Capability

Takes the gate-approved review and posts it to GitHub using the MCP server. Formats findings as inline comments on the relevant diff lines and posts the overall review with the appropriate verdict.

## When to Use

- As the final step in the PR review pipeline, after the human gate
- Only runs when the gate step approves posting

## What It Does

1. **Submit review** — calls `pull_request_review_write` to create the review with the approved verdict (APPROVE, REQUEST_CHANGES, or COMMENT) and a summary body
2. **Inline comments** — calls `add_comment_to_pending_review` for each finding, placed on the relevant file and line in the diff
3. **Security report** — if security findings exist, posts a separate collapsed comment via `add_reply_to_pull_request_comment` for detailed security analysis

## What It Does NOT Do

- Post without gate approval — this step only runs after the human gate
- Merge or close the PR — it only posts review feedback
- Modify the PR branch — it is read-only apart from comments

## Outputs

Confirmation that the review was posted, with a link to the PR and a count of inline comments posted.
