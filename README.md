# n8n-nodes-timed-buffer

Community node for n8n that buffers incoming messages in Redis and emits them as a single batch after a time window.

Built for chat-driven workflows (e.g. WhatsApp agents), where each user message triggers its own execution and downstream nodes get spammed with fragments.

## How it works

Executions are grouped by `Session Key`. The first message starts a timer (`Wait Amount` + `Wait Unit`); every message arriving inside the window is appended to the Redis-backed buffer.

| Output | Fires when |
| --- | --- |
| `Resume` | Timer elapses — returns `[{ data: [...] }]` with everything buffered |
| `Skipped` | A message arrived inside an active window — returns `{}` |

> [!NOTE]
> Redis state makes the buffer survive restarts and work across multiple n8n instances. A Redis credential is required.

## Configuration

| Parameter | Type | Description |
| --- | --- | --- |
| Session Key | string, required | Groups messages into the same buffer (e.g. a conversation id) |
| Content | string | Data to accumulate |
| Wait Amount | number | Delay after the last message before emitting |
| Wait Unit | `seconds` \| `minutes` \| `hours` \| `days` | Time unit for the delay |
| Redis credential | credential, required | [Redis credential](https://docs.n8n.io/integrations/builtin/credentials/redis/) used for buffer state |

## Example

Chat buffering with a 10-second window (`images/example.png`):

1. Five WhatsApp messages arrive in quick succession.
2. Four executions exit via **Skipped**.
3. After 10 s of silence, **Resume** emits all five at once:

```json
[{ "data": ["Hi!", "How are you?", "What's up?", "Let's meet.", "Bye!"] }]
```

## Install

Follow the [community nodes installation guide](https://docs.n8n.io/integrations/community-nodes/installation/).

## Develop

```bash
npm run build
```
