---
type: service
id: github-mcp
title: GitHub MCP
description: "GitHub MCP server for pull request access, code browsing, and review posting"
tags: [Production, Code, Review]
connections: []
metadata:
  provider: github
  protocol: mcp
  auth_type: personal_access_token
  env_var: GITHUB_TOKEN
  required_scopes: [repo, pull_request]
---

## Service Description

Provides access to GitHub repositories and pull requests via the Model Context Protocol (MCP). This service is used at both ends of the review pipeline: fetching PR data at the start and posting review results at the end.

## Configuration

### Authentication

Requires a GitHub personal access token (classic or fine-grained) set as the `GITHUB_TOKEN` environment variable.

**Classic token scopes:**
- **repo** — read access to repository contents and diffs
- **pull_request** — read and write access to pull request comments and reviews

**Fine-grained token permissions:**
- Repository: Contents (read)
- Pull requests: Read and write

### MCP Server Setup

The GitHub MCP server must be configured in your MCP settings. Typical configuration:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "{GITHUB_TOKEN}"
      }
    }
  }
}
```

## Capabilities Used

### Reading

- `pull_request_read` — retrieve PR metadata, diff, changed files, existing reviews, comments, and check run status
- `get_file_contents` — read full file contents for context beyond the diff
- `search_code` — search repository code for related patterns

### Writing

- `pull_request_review_write` — submit a review with status (APPROVE, REQUEST_CHANGES, COMMENT) and body
- `add_comment_to_pending_review` — post inline comments on specific diff lines before submitting the review
- `add_reply_to_pull_request_comment` — reply to existing review comments

## Rate Limiting

GitHub's API rate limit is 5,000 requests per hour for authenticated users. The pipeline typically consumes 10–30 requests per review depending on the number of changed files. The workflow tracks remaining rate limit via response headers and warns if approaching the limit.

## Privacy Considerations

All repository data accessed through this service is sent to your configured LLM provider for analysis. Ensure your organisation's policies permit sending source code to third-party AI services. The `data_handling: source-code` declaration in the manifest makes this explicit during import.
