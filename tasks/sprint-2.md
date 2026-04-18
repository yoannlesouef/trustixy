# Sprint 2 — `@trustixy/sdk` (npm)

**Goal:** Publish the JavaScript/TypeScript SDK to npm. Wraps Anthropic, OpenAI, and Claude Code hooks. Sends action batches to the ingest endpoint. Done when `npm install @trustixy/sdk` + 3-line snippet sends a real session.

Spec refs: `specs/sdk.md` · `specs/api.md` (ingest endpoint) · `specs/errors.md` (SDK retry logic)

---

## Package setup

- [ ] Create `packages/sdk-js/` directory (or separate repo)
- [ ] `package.json` — name: `@trustixy/sdk`, main, types, exports, files
- [ ] TypeScript config — strict, target ES2020, dual CJS/ESM build
- [ ] `.npmignore` — exclude src, tests, keep dist only

---

## Core: `Trustixy` class

- [ ] Constructor accepts: `apiKey`, `project`, `agent`, `environment` (default: `NODE_ENV`), `flushInterval` (default: 2000ms), `captureContent` (default: false), `redact` (string[] patterns), `endpoint` (default: `https://trustixy.com/api/v1/ingest`), `disabled` (default: false)
- [ ] Read `TRUSTIXY_API_KEY` from env if `apiKey` not passed explicitly
- [ ] `disabled: true` → no-op silently (for test environments)

---

## Session management

- [ ] Auto-generate `session.id` (UUID v4) per process/request context
- [ ] `session.start()` — record `started_at`, assign `human_identity` if provided
- [ ] `session.action(data)` — append action to in-memory buffer with `sequence` counter
- [ ] `session.end()` — flush buffer immediately, mark session ended
- [ ] Auto-flush on `flushInterval` if buffer is non-empty
- [ ] Auto-flush on process `beforeExit` / `SIGTERM`

---

## Action capture fields

Each action record must include:
- [ ] `id` (UUID)
- [ ] `sequence` (1-based, per session)
- [ ] `type` — `llm_call` | `tool_call` | `file_read` | `file_write` | `file_delete` | `api_call` | `shell_command` | `bash` | `mcp_call` | `custom`
- [ ] `timestamp` (ISO 8601)
- [ ] `duration_ms`
- [ ] `input` — metadata only by default; full content if `captureContent: true`
- [ ] `output` — metadata only by default; full content if `captureContent: true`
- [ ] `affected_resources` (string[]) — file paths, URLs, identifiers
- [ ] `model` — nullable; LLM model for `llm_call` actions
- [ ] `token_usage` — nullable; `{ input_tokens, output_tokens, cache_read_tokens }`
- [ ] `status` — `success` | `error` | `timeout`
- [ ] `error_message` — nullable

---

## Anthropic SDK wrapper

- [ ] `trustixy.wrap(new Anthropic(), { project, agent })` — returns a proxied Anthropic client
- [ ] Intercept `client.messages.create()` — capture as `llm_call` action
- [ ] Extract: model, input token count, output token count, cache_read_tokens, stop_reason, duration_ms
- [ ] Input: if `captureContent: false` → record only `{ model, message_count }`; if `true` → record full messages (apply redact patterns first)
- [ ] Output: if `captureContent: false` → record only `{ stop_reason }`; if `true` → record full response
- [ ] Zero behavior change — return original response unmodified

---

## OpenAI SDK wrapper

- [ ] `trustixy.wrap(new OpenAI(), { project, agent })` — returns a proxied OpenAI client
- [ ] Intercept `client.chat.completions.create()` — capture as `llm_call` action
- [ ] Same field extraction as Anthropic wrapper
- [ ] Zero behavior change

---

## Claude Code hooks integration

- [ ] `trustixy hook` CLI command — writes hook entries to `.claude/settings.json`
- [ ] Pre-tool hook: captures tool call start (type = `tool_call`, `bash`, `mcp_call`, etc.)
- [ ] Post-tool hook: captures tool call result + duration + affected_resources
- [ ] Stop hook: signals session end, triggers flush
- [ ] Hook format compatible with Claude Code's `hooks` spec

---

## Manual instrumentation

- [ ] `const session = tx.session({ human_identity? })` — create manual session
- [ ] `session.action({ type, input, output, affected_resources, ... })` — record action
- [ ] `await session.end()` — flush and close

---

## Batch sending + retry

- [ ] Batch format matches `POST /api/v1/ingest` request body (session + actions[])
- [ ] Max 100 actions per batch — split larger buffers automatically
- [ ] `Authorization: Bearer txk_live_...` header on every request
- [ ] On 429: exponential backoff starting at 1s, max 30s, max 5 retries; respect `Retry-After` header
- [ ] On 5xx: exponential backoff starting at 500ms, max 10s, max 3 retries
- [ ] On 4xx (except 429): no retry — log warning to console
- [ ] All retries exhausted: log warning to console, do not throw
- [ ] SDK never throws — wrapped client always returns original response regardless of ingest failure

---

## Content redaction

- [ ] `redact: ['password', 'secret', /api[_-]?key/i]` — string or regex patterns
- [ ] Applied to input/output content when `captureContent: true`
- [ ] Replace matched values with `[REDACTED]`
- [ ] Redaction runs client-side before any data leaves the process

---

## npm publishing

- [ ] `npm run build` — compiles TypeScript to `dist/`
- [ ] `npm run test` — unit tests (vitest or jest); test with `disabled: true` to verify no-op
- [ ] `npm publish --access public`
- [ ] Verify package appears at npmjs.com/package/@trustixy/sdk
- [ ] Verify `npm install @trustixy/sdk` + quickstart snippet works in a fresh project

---

## Verification

- [ ] Wrap Anthropic client → make one LLM call → confirm action appears in ingest payload (log to console in dev)
- [ ] Wrap OpenAI client → same check
- [ ] `disabled: true` → no HTTP requests made
- [ ] `captureContent: false` (default) → no prompt/response text in payload
- [ ] `captureContent: true` + redact pattern → matched values replaced with `[REDACTED]`
- [ ] 429 response from ingest → SDK retries with backoff, does not throw
- [ ] 100+ actions in one session → split into multiple batches of ≤100
