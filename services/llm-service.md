---
type: service
id: llm-service
title: LLM Service
description: "Language model service for code analysis, security scanning, and review generation"
tags: [Production, Tested]
connections: []
metadata:
  serviceType: llm
  auth_type: api_key
---

## LLM Service

This skrpt uses a language model for analytical and generative tasks. The LLM handles code analysis, security scanning, style checking, and review synthesis across each stage of the pipeline.

### Usage Pattern

The LLM is invoked at each stage of the pipeline. The parallel review agents (security, quality, style) each run independent analysis passes. The gate step synthesises findings into a human-readable summary. The final posting step formats the approved review for GitHub.

### Configuration

- **Temperature:** 0.2 for security and style analysis, 0.4 for quality review and synthesis
- **Max tokens:** 4,000 per review agent, 8,000 for the gate synthesis step
- **Context window:** Each parallel agent receives the full PR diff. The gate step receives all three agent outputs.

### Requirements

- A configured LLM provider in skrptiq settings
- Sufficient token quota for the full pipeline (typically 20,000–30,000 tokens per review)
- No external network access required beyond your AI provider's endpoint
