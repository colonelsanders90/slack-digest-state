# slack-digest-state

This repo holds the dedup memory for the slack-digest routines. The state repo is **separate from the prompts repo** so prompt iteration doesn't pollute state history.

## What's here

- `seen.jsonl` — append-only log of every story ever served. One JSON object per line.

## Schema (seen.jsonl)

```json
{"url": "https://...", "headline": "...", "beat": "tech|politics|global_affairs", "served_at": "2026-06-25T07:00:00+08:00", "run_id": "daily-2026-06-25"}
```

- `url` — canonical article URL (used for exact-match dedup)
- `headline` — used for fuzzy-match dedup (different sites can host the same story)
- `beat` — one of `tech`, `politics`, `global_affairs`
- `served_at` — ISO 8601 with SGT offset
- `run_id` — `daily-YYYY-MM-DD` or `weekly-YYYY-MM-DD`

The routines only consider the **last 90 days** as the "seen" window. Older entries are kept for history but ignored when deciding what to serve.

## When to edit this manually

- A story got incorrectly dedup-suppressed → delete its line.
- The routine got stuck thinking everything is "already seen" → truncate the file (`> seen.jsonl`) to start fresh.
- You want to seed a few headlines you don't want to see → append them with any `run_id`.

## Push access

The routines push to this repo using a fine-scoped PAT stored as the routine secret `GH_PAT`. The PAT is scoped to Contents:read+write on this repo only.
