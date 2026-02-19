# Alfe Voice

> Voice-agnostic agent communication layer — connect AI agents to any voice channel.

[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

**Alfe Voice** is the voice layer for [Alfe AI](https://alfe.ai). It connects OpenClaw-powered AI agents to any voice channel — phone calls, Google Meet, Slack voice memos, or browser-based WebRTC — through a unified STT → Agent → TTS pipeline.

🌐 **[voice.alfe.ai](https://voice.alfe.ai)**

## Why Alfe Voice?

AI agents shouldn't be locked to text. Alfe Voice makes any agent voice-capable across every channel:

- **📞 Phone** — Twilio-powered inbound/outbound calls
- **🎥 Google Meet** — Join meetings as a participant via WebRTC
- **💬 Slack** — Transcribe and respond to voice memos
- **🌐 Browser** — Real-time voice in the browser via WebRTC

## Architecture

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│   Channel    │────▶│  Alfe Voice   │────▶│  AI Agent   │
│  (Twilio,    │◀────│   Pipeline    │◀────│  (OpenClaw) │
│   Meet, etc) │     │  STT → TTS   │     │             │
└─────────────┘     └──────────────┘     └─────────────┘
```

The core abstraction is a **Channel Adapter** — a standard interface that any voice source implements. The pipeline handles:

1. **Ingest** — Receive audio from the channel
2. **STT** — Convert speech to text (Deepgram primary, Whisper fallback)
3. **Agent** — Route text to an OpenClaw agent session via WebSocket
4. **TTS** — Convert agent response to speech (ElevenLabs primary, Google TTS fallback)
5. **Output** — Stream audio back to the channel

See [ARCHITECTURE.md](ARCHITECTURE.md) for the full technical deep-dive.

## Project Structure

```
packages/
├── core/       # Pipeline abstraction, channel interface, session management
├── stt/        # Speech-to-text adapters (Deepgram, Whisper)
├── tts/        # Text-to-speech adapters (ElevenLabs, Google TTS)
└── channels/   # Channel adapters (Twilio, Meet, Slack, WebRTC)
```

## Quick Start

```bash
# Prerequisites: Node.js 20+, pnpm 9+
pnpm install
cp .env.example .env  # Configure API keys

# Start the voice server
pnpm dev
```

## Roadmap

See [ROADMAP.md](ROADMAP.md) for the phased development plan.

| Phase | Focus | Timeline |
|-------|-------|----------|
| 1 — Foundation | Core pipeline + Twilio | Weeks 1-3 |
| 2 — Channels | Meet, Slack, WebRTC | Weeks 4-6 |
| 3 — Intelligence | Context, barge-in, speaker ID | Weeks 7-9 |
| 4 — Platform | Dashboard, billing, API | Weeks 10-12 |

## Related Projects

- [voice-gateway](https://github.com/AlfeAI/voice-gateway) — Existing WebRTC + Google Meet voice infrastructure (being integrated into Alfe Voice)

## Tech Stack

- **Runtime:** TypeScript / Node.js
- **Monorepo:** pnpm workspaces
- **STT:** Deepgram (primary), Whisper (fallback)
- **TTS:** ElevenLabs (primary), Google TTS (fallback)
- **Phone:** Twilio
- **WebRTC:** Browser + Meet channels
- **Agent:** OpenClaw gateway WebSocket sessions

## License

MIT © [Alfe AI](https://alfe.ai)
