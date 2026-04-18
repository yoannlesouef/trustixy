# Sprint 7 — Python SDK (`trustixy` pip)

**Goal:** Publish the Python SDK to PyPI. Wraps Anthropic and OpenAI Python clients. Sends action batches to the ingest endpoint. Done when `pip install trustixy` + 3-line snippet sends a real session.

Spec refs: `specs/sdk.md` · `specs/api.md` (ingest endpoint) · `specs/errors.md` (SDK retry logic)

Can run in parallel with Sprints 4–6.

---

## Package setup

- [ ] Create `packages/sdk-py/` directory
- [ ] `pyproject.toml` — name: `trustixy`, version, dependencies (`anthropic`, `openai` as optional extras), Python ≥3.9
- [ ] `src/trustixy/__init__.py` — exports `Trustixy`, `Session`
- [ ] Type hints throughout — `py.typed` marker file
- [ ] `pytest` + `pytest-asyncio` for tests

---

## Core: `Trustixy` class

- [ ] Constructor accepts: `api_key`, `project`, `agent`, `environment` (default: `os.getenv("ENV", "production")`), `flush_interval` (default: `2.0` seconds), `capture_content` (default: `False`), `redact` (list of str/regex patterns), `endpoint` (default: `https://trustixy.com/api/v1/ingest`), `disabled` (default: `False`)
- [ ] Read `TRUSTIXY_API_KEY` from env if `api_key` not passed explicitly
- [ ] `disabled=True` → no-op silently (for test environments)
- [ ] Thread-safe buffer using `threading.Lock`

---

## Session management

- [ ] `trustixy.session(human_identity=None)` → returns `Session` object
- [ ] Auto-generate `session.id` (UUID v4) on creation; record `started_at`
- [ ] `session.action(data: dict)` — append action to in-memory buffer with `sequence` counter (1-based)
- [ ] `session.end()` — flush buffer immediately, mark session ended
- [ ] Context manager support: `with trustixy.session() as session:`  → auto-calls `end()` on exit
- [ ] Background flush thread: flush every `flush_interval` seconds if buffer non-empty
- [ ] `atexit` handler: flush all open sessions on process exit

---

## Action capture fields

Each action record must include:
- [ ] `id` (UUID)
- [ ] `sequence` (1-based, per session)
- [ ] `type` — `llm_call` | `tool_call` | `file_read` | `file_write` | `file_delete` | `api_call` | `shell_command` | `bash` | `mcp_call` | `custom`
- [ ] `timestamp` (ISO 8601)
- [ ] `duration_ms`
- [ ] `input` — metadata only by default; full content if `capture_content=True`
- [ ] `output` — metadata only by default; full content if `capture_content=True`
- [ ] `affected_resources` (list[str]) — file paths, URLs, identifiers
- [ ] `model` — nullable; LLM model for `llm_call` actions
- [ ] `token_usage` — nullable; `{ input_tokens, output_tokens, cache_read_tokens }`
- [ ] `status` — `success` | `error` | `timeout`
- [ ] `error_message` — nullable

---

## Anthropic Python SDK wrapper

- [ ] `trustixy.wrap(anthropic.Anthropic(), project=..., agent=...)` → returns proxied client
- [ ] Intercept `client.messages.create()` — capture as `llm_call` action
- [ ] Extract: model, input token count, output token count, cache_read_tokens (if present), stop_reason, duration_ms
- [ ] Input: if `capture_content=False` → record only `{ model, message_count }`; if `True` → record full messages (apply redact first)
- [ ] Output: if `capture_content=False` → record only `{ stop_reason }`; if `True` → record full response text
- [ ] Streaming support: intercept streaming responses, capture when stream completes
- [ ] Zero behavior change — return original response unmodified

---

## OpenAI Python SDK wrapper

- [ ] `trustixy.wrap(openai.OpenAI(), project=..., agent=...)` → returns proxied client
- [ ] Intercept `client.chat.completions.create()` — capture as `llm_call` action
- [ ] Same field extraction as Anthropic wrapper
- [ ] Streaming support: intercept streaming responses
- [ ] Zero behavior change

---

## Async support

- [ ] Wrap `anthropic.AsyncAnthropic()` — intercept `await client.messages.create()`
- [ ] Wrap `openai.AsyncOpenAI()` — intercept `await client.chat.completions.create()`
- [ ] `await session.end()` — async flush variant
- [ ] Async context manager: `async with trustixy.session() as session:`
- [ ] Background flush via `asyncio` task when running in async context

---

## Manual instrumentation

- [ ] `session = tx.session(human_identity=None)` — create manual session
- [ ] `session.action({ "type": ..., "input": ..., "output": ..., "affected_resources": [...] })` — record action
- [ ] `session.end()` — flush and close
- [ ] Works in both sync and async contexts

---

## Batch sending + retry

- [ ] Batch format matches `POST /api/v1/ingest` request body (session + actions[])
- [ ] `httpx` for HTTP (supports both sync and async)
- [ ] Max 100 actions per batch — split larger buffers automatically
- [ ] `Authorization: Bearer txk_live_...` header on every request
- [ ] On 429: exponential backoff starting at 1s, max 30s, max 5 retries; respect `Retry-After` header
- [ ] On 5xx: exponential backoff starting at 500ms, max 10s, max 3 retries
- [ ] On 4xx (except 429): no retry — log warning via `logging.getLogger("trustixy")`
- [ ] All retries exhausted: log warning, do not raise
- [ ] SDK never raises — wrapped client always returns original response regardless of ingest failure

---

## Content redaction

- [ ] `redact=["password", "secret", re.compile(r"api[_-]?key", re.I)]` — str or compiled pattern
- [ ] Applied to input/output content when `capture_content=True`
- [ ] Replace matched values with `[REDACTED]`
- [ ] Redaction runs client-side before any data leaves the process

---

## PyPI publishing

- [ ] `python -m build` — produces `dist/` with wheel + sdist
- [ ] `python -m pytest` — all tests pass; test with `disabled=True` to verify no-op
- [ ] `twine upload dist/*`
- [ ] Verify package appears at pypi.org/project/trustixy
- [ ] Verify `pip install trustixy` + quickstart snippet works in a fresh virtualenv

---

## Verification

- [ ] Wrap `anthropic.Anthropic()` → make one LLM call → confirm action appears in ingest payload (logged in dev)
- [ ] Wrap `openai.OpenAI()` → same check
- [ ] Wrap async clients → same check with `asyncio.run()`
- [ ] `disabled=True` → no HTTP requests made
- [ ] `capture_content=False` (default) → no prompt/response text in payload
- [ ] `capture_content=True` + redact pattern → matched values replaced with `[REDACTED]`
- [ ] 429 response from ingest → SDK retries with backoff, does not raise
- [ ] 100+ actions in one session → split into multiple batches of ≤100
- [ ] Context manager → `session.end()` called automatically on exit
- [ ] Process exit with open session → `atexit` handler flushes remaining actions
