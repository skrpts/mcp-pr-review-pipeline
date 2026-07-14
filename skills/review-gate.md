---
type: skill
id: review-gate
title: Review Gate
description: "Human gate — pauses for you to review findings before posting to GitHub"
tags: [Production, Gate, Code]
connections:
  - target: llm-service
    type: runs_on
metadata:
  gate: true
---

## Capability

Pauses the workflow and presents a synthesized summary of all review findings. You review the combined security, quality, and style results, then decide whether to post the review to GitHub, edit findings, or abort.

This is a **gate step** — execution pauses until you respond.

## What Happens

1. Execution pauses with status `awaiting_input`
2. You see a consolidated review summary: findings count by severity, key issues, and the proposed review verdict (approve, request changes, or comment)
3. You respond with:
   - **Approve** — "post it" or "looks good, submit" → the review is posted as-is
   - **Edit** — "remove finding 3" or "change the verdict to approve" → findings are adjusted before posting
   - **Abort** — "don't post" → the pipeline stops without posting anything to GitHub
4. Your decision feeds into the review posting step

## Why a Gate

Automated reviews should never post to a PR without human oversight. The gate ensures you see exactly what will be posted and can catch false positives, adjust tone, or add context before the review lands on the author's PR.
