---
name: perplexity
title: "Perplexity AI API"
description: "Search the web and generate grounded Perplexity Sonar responses with citations, search results, related questions, structured JSON, async jobs, and multimodal inputs including text, images, files, PDFs, and video URLs."
use_case: "Use for cited web answers, live research, grounded chat, web search snippets, model discovery, structured JSON generation, async deep-research workflows, multimodal question answering, and agent responses that rely on current sources."
category: ai_ml
service_url: https://pplx.x402.paysponge.com
endpoints:
  - method: POST
    path: search
    resource: search
    description: "Search web pages and return URLs, titles, snippets, and date metadata with optional domain, language, and date filters."
    pricing:
      dimensions:
        - direction: usage
          unit: requests
          scale: 1
          tiers:
            - price_usd: 0.01
  - method: POST
    path: v1/agent
    resource: responses
    description: "Generate grounded Perplexity responses with citations, reasoning, multimodal inputs, and optional structured JSON output."
    pricing:
      dimensions:
        - direction: usage
          unit: requests
          scale: 1
          tiers:
            - price_usd: 0.01
  - method: GET
    path: v1/models
    resource: models
    description: "List supported model identifiers that callers can use with the agent response endpoint."
  - method: POST
    path: v1/async/sonar
    resource: jobs
    description: "Submit an asynchronous Sonar response job for longer research tasks and receive a request id for polling."
    pricing:
      dimensions:
        - direction: usage
          unit: requests
          scale: 1
          tiers:
            - price_usd: 0.01
  - method: GET
    path: v1/async/sonar/{api_request}
    resource: jobs
    description: "Retrieve the current status or completed payload for a previously submitted asynchronous Sonar request."
---

Perplexity search and response API exposed through PaySponge with x402
payments.

Full OpenAPI spec: [openapi.json](https://pplx.x402.paysponge.com/openapi.json)

This listing intentionally covers the routes that currently probe cleanly under
this registry's Solana-first payment requirements: raw web search at `/search`,
OpenAI-style response generation through `POST /v1/agent`, model discovery at
`GET /v1/models`, and async Sonar submit/status routes for longer jobs. Agents
that need the full request schema, enum values, or streaming event shapes
should inspect the OpenAPI document directly.

Responses can include citations, search results, related questions, token and
cost usage, structured JSON output, and streaming SSE events. Agent requests
also support search controls such as domain, language, recency, and date
filters plus `reasoning_effort`, `response_format`, optional search disabling,
and multimodal inputs including image, file, PDF, and video URLs.

Note: the published spec also advertises `POST /v1/sonar` as a separate
chat-completions route at $0.10, but its current x402 payment details do not
probe cleanly for Solana payments. Solana-first callers in this registry should
prefer `/v1/agent` or the async Sonar routes instead.

## Spend-aware usage

- Use `POST /search` when the caller wants raw result URLs, titles, snippets,
  or date metadata. Use `POST /v1/agent` when the caller wants a synthesized,
  cited answer or structured JSON.
- Set search filters up front: domain, language, recency, and date windows are
  cheaper than broad searches followed by repeated retries.
- Call `GET /v1/models` only when model discovery is genuinely needed. For a
  known workflow, reuse a fixed model ID instead of spending an extra request.
- Use `POST /v1/async/sonar` for long-running or deep-research work, then poll
  `GET /v1/async/sonar/{api_request}` instead of resubmitting the same job.
- Set `disable_search` only when the task should rely on model knowledge alone.
  Otherwise keep search enabled so answers stay grounded in current sources.
