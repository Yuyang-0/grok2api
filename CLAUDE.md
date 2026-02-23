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
