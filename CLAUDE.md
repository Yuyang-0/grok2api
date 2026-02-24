# CLAUDE.md

## Project Notes

- Repository: `grok2api`
- API style: OpenAI-compatible endpoints powered by FastAPI
- Key runtime entry: `main.py`
- Core chat service: `app/services/grok/services/chat.py`

## Important Runtime Data

- Runtime config file: `data/config.toml`
- Runtime token store: `data/token.json`
- Default config baseline: `config.defaults.toml`

## 2026-02-24 Updates

1. Fixed non-stream chat error handling in `CollectProcessor`.
   - File: `app/services/grok/services/chat.py`
   - Behavior change: upstream/transport errors are now raised instead of being swallowed.
   - Expected result: non-stream `/v1/chat/completions` no longer returns misleading empty `200` responses when upstream fails.

2. Improved skill chat script error reporting.
   - File: `/Users/yuyangguan/Documents/personal/agent-skills/grok2api/scripts/chat.sh`
   - Behavior change: script now checks HTTP status and surfaces upstream error messages; exits non-zero on failures.
   - Also removed `eval` usage and switched request body generation to JSON-safe Python serialization.

3. Added chat content object compatibility for OpenAI-like clients.
   - Files: `app/api/v1/chat.py`, `app/services/grok/services/chat.py`
   - Behavior change: `messages[].content` now accepts a single object payload (e.g. `{"type":"text","text":"..."}`) in addition to string and array formats.
   - Expected result: clients that send object-form content (such as some OpenClaw request paths) no longer fail with `400 Input should be a valid string`.

4. Added assistant null-content compatibility for OpenAI-like history payloads.
   - File: `app/api/v1/chat.py`
   - Behavior change: `messages[].content = null` is now accepted only when `role` is `assistant`.
   - Expected result: clients that include assistant placeholder/tool-call history messages with null content no longer fail at request validation.

5. Added tool-call session compatibility for OpenAI-like clients.
   - Files: `app/api/v1/chat.py`, `app/services/grok/services/chat.py`
   - Behavior change:
     - `role=tool` is now accepted.
     - `MessageItem` preserves `tool_calls` / `tool_call_id` / `name` fields.
     - Extractor now keeps tool-call traces and tool message identity in context text.
   - Expected result: multi-turn non-fresh sessions with tool-calling history are less likely to fail validation or lose ordering context.
