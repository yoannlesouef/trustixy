# SDK Reference

## Overview

The Trustixy SDK is the data engine of the platform. It wraps AI frameworks to capture every agent action automatically — zero behavior change, one-line instrumentation.

Two packages:
- `@trustixy/sdk` — npm, for JavaScript/TypeScript
- `trustixy` — pip, for Python

---

## JavaScript / TypeScript SDK

### Installation

```bash
npm install @trustixy/sdk
```

### Configuration

Set your API key via environment variable:

```bash
TRUSTIXY_API_KEY=txk_live_...
```

Or pass it explicitly:

```typescript
import { Trustixy } from "@trustixy/sdk";

const tx = new Trustixy({
  apiKey: process.env.TRUSTIXY_API_KEY,
  project: "my-backend",      // required — groups agents into a project
  agent: "code-assistant",    // required — identifies this agent
  environment: "production",  // optional — defaults to NODE_ENV
  flushInterval: 2000,        // optional — ms between batch sends, default 2000
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

### Vercel AI SDK

```typescript
import { generateText, streamText } from "ai";
import { wrapVercelAI } from "@trustixy/sdk/vercel";

const { generateText: traced } = wrapVercelAI({ generateText }, {
  project: "my-app",
  agent: "content-generator"
});

const result = await traced({
  model: openai("gpt-4o"),
  prompt: "...",
});
```

### LangChain

```typescript
import { ChatOpenAI } from "@langchain/openai";
import { TrustixyCallbackHandler } from "@trustixy/sdk/langchain";

const handler = new TrustixyCallbackHandler({
  project: "my-pipeline",
  agent: "rag-chain"
});

const model = new ChatOpenAI({ callbacks: [handler] });
```

### Manual instrumentation

For custom agents or frameworks not natively supported:

```typescript
import { trustixy } from "@trustixy/sdk";

const session = trustixy.session({
  project: "my-pipeline",
  agent: "custom-agent",
  humanIdentity: "user@company.com"  // optional — who triggered this session
});

// Record an action
await session.action({
  type: "llm_call",
  input: { prompt: "..." },
  output: { response: "..." },
  model: "custom-model-v1",
  affectedResources: [],
  durationMs: 420,
});

// Record a file operation
await session.action({
  type: "file_write",
  input: { path: "/src/utils.ts", content: "..." },
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

# Identical API
message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Refactor this function..."}]
)
```

### LangChain

```python
from langchain_anthropic import ChatAnthropic
from trustixy.langchain import TrustixyCallbackHandler

handler = TrustixyCallbackHandler(
    project="my-pipeline",
    agent="rag-chain"
)

model = ChatAnthropic(
    model="claude-sonnet-4-6",
    callbacks=[handler]
)
```

### LlamaIndex

```python
from llama_index.core import Settings
from trustixy.llama_index import TrustixyObserver

observer = TrustixyObserver(project="my-docs", agent="query-engine")
Settings.callback_manager.add_handler(observer)
```

### CrewAI

```python
from crewai import Agent, Task, Crew
from trustixy.crewai import TrustixyObserver

observer = TrustixyObserver(project="my-crew", agent="research-crew")

crew = Crew(
    agents=[...],
    tasks=[...],
    callbacks=[observer]
)
```

---

## Claude Code Hook Integration

Instrument every Claude Code session automatically via hooks. Add to `.claude/settings.json` in your project root:

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

The hook CLI (`trustixy hook`) reads the tool name, input, and output from the Claude Code hook event payload and sends an action record to the ingest API.

Hook CLI installation:
```bash
npm install -g @trustixy/cli
# or
pip install trustixy[cli]
```

---

## MCP Server

Trustixy provides an MCP server that instruments MCP tool calls at the protocol level:

```jsonc
// .claude/settings.json
{
  "mcpServers": {
    "trustixy": {
      "command": "npx",
      "args": ["-y", "@trustixy/mcp"],
      "env": {
        "TRUSTIXY_API_KEY": "txk_live_...",
        "TRUSTIXY_PROJECT": "my-project"
      }
    }
  }
}
```

When the Trustixy MCP server is active, it intercepts all MCP tool calls from connected clients and records them as actions.

---

## What Gets Captured

Every action record includes:

| Field | Description |
|---|---|
| `session_id` | Groups actions from one agent run |
| `agent_identity` | Which agent (name + framework) |
| `human_identity` | Who triggered the session (if known) |
| `sequence` | Order within the session |
| `type` | `llm_call` · `tool_call` · `file_read` · `file_write` · `file_delete` · `api_call` · `shell_command` · `bash` · `mcp_call` · `custom` |
| `timestamp` | UTC, millisecond precision |
| `duration_ms` | Execution time |
| `input` | Sanitized input (prompt, file path, command, etc.) |
| `output` | Sanitized output (response, content, exit code, etc.) |
| `affected_resources` | File paths, URLs, or identifiers modified or accessed |
| `model` | LLM model identifier (for `llm_call` actions) |
| `token_usage` | `{ input_tokens, output_tokens, cache_read_tokens }` |
| `status` | `success` · `error` · `timeout` |
| `error_message` | If status is `error` |

### Sensitive data handling

By default, the SDK **does not capture** the full content of file writes or LLM outputs. Only metadata and structural information are sent. Full content capture must be explicitly enabled:

```typescript
trustixy.wrap(client, {
  project: "...",
  agent: "...",
  captureContent: true,  // default: false — captures full input/output content
  redact: ["password", "token", "secret", /Bearer\s+\S+/]  // redact patterns
});
```

When `captureContent` is false:
- File write: captures path + byte size, not content
- LLM call: captures model + token usage, not prompt or response text
- API call: captures URL + method + status code, not request/response body

---

## Session Context

Sessions group related actions:

```typescript
// Explicit session
const session = trustixy.startSession({
  project: "my-app",
  agent: "ci-assistant",
  humanIdentity: "github-actions",  // who or what triggered this
  metadata: {
    pullRequestId: "123",
    branch: "feature/payments"
  }
});

const client = trustixy.wrap(new Anthropic(), { session });
```

When no explicit session is created, the SDK creates one automatically per process or per request context (using AsyncLocalStorage in Node.js).

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `apiKey` | string | `TRUSTIXY_API_KEY` env | Authentication key |
| `project` | string | required | Project slug |
| `agent` | string | required | Agent identifier |
| `environment` | string | `NODE_ENV` | `production` · `staging` · `development` |
| `captureContent` | boolean | `false` | Capture full input/output content |
| `redact` | string[] / RegExp[] | `[]` | Patterns to redact from captured content |
| `flushInterval` | number | `2000` | Milliseconds between batch sends |
| `endpoint` | string | `https://api.trustixy.com` | Override API endpoint (for self-hosted) |
| `disabled` | boolean | `false` | Disable SDK entirely (useful for tests) |

---

## Ingest API

The SDK sends action batches to:

```
POST https://api.trustixy.com/v1/ingest
Authorization: Bearer txk_live_...
Content-Type: application/json

{
  "session": { ... },
  "actions": [ ... ]
}
```

Full ingest API spec: [`/specs/api.md`](api.md)

---

## Privacy & Security

- API keys are scoped to a project; a compromised key cannot read data from other projects
- All data is encrypted in transit (TLS 1.3) and at rest (AES-256)
- `captureContent: false` (the default) means raw prompts and file contents never leave your environment
- Self-hosted deployment available on Enterprise plan — the ingest endpoint can point to your own infrastructure
- SDK tokens can be revoked per-project from the web platform at any time
