# n8n-nodes-timed-buffer

Community node for n8n that collects incoming messages into a time-based buffer (Redis-backed) and emits them all at once.

## Problem

Chat and webhook messages arrive fragmented (one execution per message). Downstream AI agents get spammed with partial inputs.

## What it does

Groups executions by `sessionKey`, waits `waitAmount` + `waitUnit`, then emits all buffered `content` in a single `Resume` output. Executions arriving mid-window go to `Skipped`.

## Use

1. Install via n8n community nodes.
2. Set a Redis credential (required).
3. `Session Key` → conversation id · `Content` → message · `Wait` → e.g. `30 seconds`.

| Output | When |
| --- | --- |
| `Resume` | Wait elapsed → `[{ data: [...] }]` |
| `Skipped` | Message arrived during active window → `{}` |

Example: 5 WhatsApp messages → 4 `Skipped`, then 1 `Resume` with all 5. See `images/example.png`.

## Dev

```bash
npm run build
```

MIT.
