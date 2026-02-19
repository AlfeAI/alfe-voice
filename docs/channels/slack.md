# Slack Channel

## Overview
Transcribe Slack voice memos (huddle clips, audio messages) and respond with the AI agent.

## How It Works

1. **Event:** Slack sends `file_shared` event via Events API webhook
2. **Filter:** Check if file is an audio type (webm, mp4, ogg)
3. **Download:** Fetch audio file using Slack Bot Token
4. **Transcribe:** Run through STT adapter (batch mode)
5. **Agent:** Send transcription to OpenClaw agent session
6. **Respond:** Post agent response as a threaded text reply

## Configuration

```bash
SLACK_BOT_TOKEN=xoxb-xxxxxxxx
SLACK_SIGNING_SECRET=xxxxxxxx
SLACK_APP_ID=Axxxxxxxx
```

## Slack App Setup

Required OAuth scopes:
- `files:read` — Download voice memos
- `chat:write` — Post responses
- `channels:history` — Read message context

Event subscriptions:
- `file_shared` — Trigger on new voice memos

## Key Considerations
- Voice memos are async (not real-time) — batch STT is fine
- Response can be text or a synthesized voice memo (TTS → upload audio)
- Thread context: read parent message for conversation context
- Rate limits: Slack API has tier-based rate limiting
