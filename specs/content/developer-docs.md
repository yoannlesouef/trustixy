# Developer Documentation

Public-facing docs at `docs.trustixy.com` (or `/docs` on trustixy.com). Target audience: developers instrumenting Trustixy for the first time.

---

## npm Package README (`@trustixy/sdk`)

This README is the primary developer discovery surface. Shown on npmjs.com and in editors.

```markdown
# @trustixy/sdk

One-line audit trail for your AI agents. Every LLM call, file write,
API call, and shell command — captured automatically.

## Install

npm install @trustixy/sdk

## Anthropic

import Anthropic from "@anthropic-ai/sdk";
import { trustixy } from "@trustixy/sdk";

const client = trustixy.wrap(new Anthropic(), {
  project: "my-app",
  agent: "code-assistant"
});

// Identical API. Every action traced.
const response = await client.messages.create({ ... });

## OpenAI

import OpenAI from "openai";
import { trustixy } from "@trustixy/sdk";

const openai = trustixy.wrap(new OpenAI(), {
  project: "my-app",
  agent: "gpt-assistant"
});

## Claude Code hooks

npm install -g @trustixy/cli

Add to .claude/settings.json — see docs.

## What gets captured

- Agent identity · session grouping · action sequence
- LLM calls: model, token usage, duration
- File operations: path, byte size
- API calls: URL, method, status code
- Shell commands: command, exit code, duration
- Sensitive file alerts: .env, secrets, credentials

## Docs

https://docs.trustixy.com

## Get an API key

https://trustixy.com
```

---

## PyPI Package README (`trustixy`)

```markdown
# trustixy

One-line audit trail for your AI agents.

## Install

pip install trustixy

## Anthropic

import anthropic
from trustixy import wrap

client = wrap(
    anthropic.Anthropic(),
    project="my-backend",
    agent="code-assistant"
)

## OpenAI

from openai import OpenAI
from trustixy import wrap

client = wrap(OpenAI(), project="my-backend", agent="gpt-assistant")

## Docs

https://docs.trustixy.com
```

---

## Docs Site Structure

### Quickstart (default landing page)

**Goal:** developer has their first session in the platform in under 5 minutes.

1. Install the SDK
2. Set the API key
3. Wrap your client
4. Run one call
5. See the session in the dashboard

Each step: one code block, one sentence. No prose beyond what is essential.

---

### SDK Reference

`/docs/sdk` — mirrors `specs/sdk.md` but formatted for external reading.

Sections:
- Installation (npm / pip)
- Configuration options (table)
- Anthropic (JS + Python)
- OpenAI (JS + Python)
- Claude Code hooks
- Manual instrumentation
- What gets captured (table)
- Sensitive data handling
- Session context
- v2 frameworks (coming soon — Vercel AI SDK, LangChain, LlamaIndex, CrewAI, MCP)

---

### Claude Code Hooks Guide

`/docs/claude-code-hooks`

Dedicated page for Claude Code users. Content:

1. What hooks capture (PreToolUse, PostToolUse, Stop events)
2. Install CLI: `npm install -g @trustixy/cli`
3. Add to `.claude/settings.json` (copy-paste block)
4. Verify: run a Claude Code session → check Activity in dashboard

Note: this audience skews non-developer (founders, researchers using Claude Code). Write plainly.

---

### API Reference

`/docs/api` — generated from OpenAPI spec. Key endpoints documented with examples:

- `POST /ingest` — SDK calls this automatically; shown for manual instrumentation
- `GET /sessions` — fetch sessions programmatically
- `GET /agents` — list agents
- `GET /agents/:id/classification` — fetch current classification
- `POST /agents/:id/classify` — trigger reclassification
- `GET /documents/:id` — fetch compliance document
- `GET /verify/:docId` — public verification endpoint

---

### Self-hosted Ingest (Enterprise)

`/docs/self-hosted`

Brief: how to point the SDK at a private ingest endpoint using the `endpoint` configuration option. Covers Docker deployment and environment variables.

---

## SEO Requirements for Docs

- Each page has a unique `<title>` and meta description
- Code blocks are copyable (one-click)
- Pages are indexable (no `noindex` on docs unless internal)
- Quickstart page targets keyword: "instrument Anthropic SDK"
- Claude Code hooks page targets keyword: "Claude Code audit log"
- Framework pages linked from npm/PyPI READMEs

---

## Maintenance Rules

- Docs are updated before a release ships, not after
- Code examples in docs are tested in CI against the live SDK
- "v2 coming soon" sections list planned frameworks without committing to dates
- Changelog at `/docs/changelog` — one entry per release, bullet-point format
