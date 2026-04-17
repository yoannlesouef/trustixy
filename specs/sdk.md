# SDK Reference

## Overview

The Trustixy SDK wraps AI framework clients to capture every agent action automatically — zero behavior change, one-line instrumentation.

Two packages:
- `@trustixy/sdk` — npm, for JavaScript/TypeScript
- `trustixy` — pip, for Python

**v1 supported:** Anthropic SDK · OpenAI SDK · Claude Code hooks  
**Planned (v2):** Vercel AI SDK · LangChain · LlamaIndex · CrewAI · MCP

---

## JavaScript / TypeScript SDK

### Installation

```bash
npm install @trustixy/sdk
```

### Configuration

```bash
TRUSTIXY_API_KEY=txk_live_...
```

Or pass explicitly:

```typescript
import { Trustixy } from "@trustixy/sdk";

const tx = new Trustixy({
  apiKey: process.env.TRUSTIXY_API_KEY,
  project: "my-backend",   // required — groups agents into a project
  agent: "code-assistant", // required — identifies this agent
  environment: "production", // optional — defaults to NODE_ENV
  flushInterval: 2000,     // optional — ms between batch sends
});
```

### Anthropic SDK

```typescript
import Anthropic from "@anthropic-ai/sdk";
import { trustixy } from "@trustixy/sdk";

const client = trustixy.wrap(new Anthropic(), {
  project: "my-backend",
  agent: "code-assistant"
});

// Identical API — all calls traced
const response = await client.messages.create({
  model: "claude-sonnet-4-6",
  max_tokens: 1024,
  messages: [{ role: "user", content: "Refactor this function..." }]
});
```

### OpenAI SDK

```typescript
import OpenAI from "openai";
import { trustixy } from "@trustixy/sdk";

const openai = trustixy.wrap(new OpenAI(), {
  project: "my-backend",
  agent: "gpt-assistant"
});

const response = await openai.chat.completions.create({ ... });
```

### Manual instrumentation

For any framework not natively supported:

```typescript
import { trustixy } from "@trustixy/sdk";

const session = trustixy.session({
  project: "my-pipeline",
  agent: "custom-agent",
  humanIdentity: "user@company.com"
});

await session.action({
  type: "llm_call",
  input: { prompt: "..." },
  output: { response: "..." },
  model: "custom-model-v1",
  affectedResources: [],
  durationMs: 420,
});

await session.action({
  type: "file_write",
  input: { path: "/src/utils.ts" },
  affectedResources: ["/src/utils.ts"],
  durationMs: 12,
});

await session.end();
```

---

## Python SDK

### Installation

```bash
pip install trustixy
```

### Anthropic Python SDK

```python
import anthropic
from trustixy import wrap

client = wrap(
    anthropic.Anthropic(),
    project="my-backend",
    agent="code-assistant"
)

message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Refactor this function..."}]
)
```

### OpenAI Python SDK

```python
from openai import OpenAI
from trustixy import wrap

client = wrap(OpenAI(), project="my-backend", agent="gpt-assistant")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "..."}]
)
```

### Manual instrumentation

```python
from trustixy import Session

session = Session(project="my-pipeline", agent="custom-agent")

session.action(
    type="llm_call",
    input={"prompt": "..."},
    output={"response": "..."},
    model="custom-model",
    affected_resources=[],
    duration_ms=420,
)

session.end()
```

---

## Claude Code Hook Integration

Instrument every Claude Code session automatically via hooks. Add to `.claude/settings.json`:

```jsonc
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": ".*",
        "command": "trustixy hook pre --project my-project --api-key $TRUSTIXY_API_KEY"
      }
    ],
    "PostToolUse": [
      {
        "matcher": ".*",
        "command": "trustixy hook post --project my-project --api-key $TRUSTIXY_API_KEY"
      }
    ],
    "Stop": [
      {
        "command": "trustixy hook stop --project my-project"
      }
    ]
  }
}
```

Hook CLI installation:
```bash
npm install -g @trustixy/cli
```

The hook CLI reads the tool name, input, and output from the Claude Code hook event payload and sends an action record to the ingest API.

---

## What Gets Captured

| Field | Description |
|---|---|
| `agent_id` | Which agent (name + framework) |
| `session_id` | Groups actions from one run |
| `human_identity` | Who triggered the session (if known) |
| `sequence` | Order within the session |
| `type` | `llm_call` · `tool_call` · `file_read` · `file_write` · `file_delete` · `api_call` · `shell_command` · `bash` · `custom` |
| `timestamp` | UTC, millisecond precision |
| `duration_ms` | Execution time |
| `input` | Sanitized input (prompt, path, command, etc.) |
| `output` | Sanitized output (response, exit code, etc.) |
| `affected_resources` | File paths, URLs, or identifiers accessed or modified |
| `model` | LLM model identifier (for `llm_call` actions) |
| `token_usage` | `{ input_tokens, output_tokens, cache_read_tokens }` |
| `status` | `success` · `error` · `timeout` |

### Sensitive data handling

By default, the SDK captures metadata only — no raw content. Full content capture must be explicitly enabled:

```typescript
trustixy.wrap(client, {
  project: "...",
  agent: "...",
  captureContent: true,
  redact: ["password", "token", "secret", /Bearer\s+\S+/]
});
```

When `captureContent` is false:
- File write: path + byte size only
- LLM call: model + token counts only
- API call: URL + method + status code only

---

## Session Context

```typescript
const session = trustixy.startSession({
  project: "my-app",
  agent: "ci-assistant",
  humanIdentity: "github-actions",
  metadata: { pullRequestId: "123", branch: "feature/payments" }
});

const client = trustixy.wrap(new Anthropic(), { session });
```

When no explicit session is created, the SDK groups actions automatically per process or request context (AsyncLocalStorage in Node.js).

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `apiKey` | string | `TRUSTIXY_API_KEY` env | Authentication key |
| `project` | string | required | Project slug |
| `agent` | string | required | Agent identifier |
| `environment` | string | `NODE_ENV` | `production` · `staging` · `development` |
| `captureContent` | boolean | `false` | Capture full input/output content |
| `redact` | string[] / RegExp[] | `[]` | Patterns to redact from content |
| `flushInterval` | number | `2000` | ms between batch sends |
| `endpoint` | string | `https://api.trustixy.com` | Override for self-hosted |
| `disabled` | boolean | `false` | Disable SDK (useful for tests) |

---

## Privacy & Security

- API keys are scoped to a project; a compromised key cannot read other projects
- All data encrypted in transit (TLS 1.3) and at rest (AES-256)
- `captureContent: false` (default) means raw prompts and file contents never leave your environment
- Self-hosted ingest endpoint available on Enterprise
- SDK tokens revocable per-project from the web platform
