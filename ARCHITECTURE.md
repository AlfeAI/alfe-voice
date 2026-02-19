# Architecture

## Overview

Alfe Voice is a **channel-agnostic voice pipeline** that connects any audio source to an AI agent via a standard adapter pattern.

```
┌─────────────────────────────────────────────────────────┐
│                     Alfe Voice Server                    │
│                                                          │
│  ┌──────────┐   ┌─────────┐   ┌──────────┐   ┌───────┐ │
│  │ Channel  │──▶│   STT   │──▶│  Agent   │──▶│  TTS  │ │
│  │ Adapter  │◀──│ Adapter │   │ Session  │   │Adapter│ │
│  └──────────┘   └─────────┘   └──────────┘   └───────┘ │
│       ▲                            │                     │
│       │         ┌──────────┐       │                     │
│       └─────────│ Pipeline │◀──────┘                     │
│                 │ Manager  │                             │
│                 └──────────┘                             │
└─────────────────────────────────────────────────────────┘
```

## Core Concepts

### Channel Adapter

A channel adapter handles the transport layer for a specific voice source. It implements:

```typescript
interface ChannelAdapter {
  readonly type: string; // 'twilio' | 'meet' | 'slack' | 'webrtc'

  // Lifecycle
  connect(config: ChannelConfig): Promise<void>;
  disconnect(): Promise<void>;

  // Audio I/O
  onAudio(handler: (audio: AudioChunk) => void): void;
  sendAudio(audio: AudioChunk): Promise<void>;

  // Events
  onEvent(handler: (event: ChannelEvent) => void): void;
}
```

### STT Adapter

Converts audio streams to text. Supports streaming for real-time transcription.

```typescript
interface STTAdapter {
  readonly provider: string; // 'deepgram' | 'whisper'

  startStream(config: STTConfig): TranscriptionStream;
  transcribe(audio: Buffer): Promise<string>; // Batch fallback
}
```

### TTS Adapter

Converts text to audio. Supports streaming for low-latency playback.

```typescript
interface TTSAdapter {
  readonly provider: string; // 'elevenlabs' | 'google'

  synthesize(text: string, config: TTSConfig): Promise<AudioChunk>;
  stream(text: string, config: TTSConfig): AudioStream; // Streaming
}
```

### Pipeline Manager

Orchestrates the flow: channel audio → STT → agent → TTS → channel output.

Key responsibilities:
- **Session binding** — Maps channel connections to OpenClaw agent sessions
- **Stream management** — Handles concurrent audio streams, buffering, backpressure
- **Interruption** — Cancels TTS output when new user speech is detected (barge-in)
- **Error recovery** — Reconnects adapters on failure

### Agent Session

Manages the WebSocket connection to an OpenClaw gateway session.

```typescript
interface AgentSession {
  connect(gatewayUrl: string, sessionId?: string): Promise<void>;
  send(text: string): Promise<string>; // Send text, receive response
  onMessage(handler: (message: string) => void): void;
  disconnect(): Promise<void>;
}
```

## Data Flow

### Inbound Voice (User → Agent)

1. Channel receives audio (e.g., Twilio WebSocket, WebRTC track)
2. Audio chunks flow to STT adapter (streaming)
3. STT produces partial + final transcriptions
4. Final transcription sent to Agent Session
5. Agent responds with text
6. Text flows to TTS adapter
7. TTS audio streams back to channel

### Latency Budget

Target end-to-end latency: **< 1.5 seconds**

| Stage | Target | Notes |
|-------|--------|-------|
| Channel → STT | 100ms | Streaming, minimal buffering |
| STT processing | 300ms | Deepgram streaming ~200-400ms |
| Agent response | 500ms | OpenClaw gateway response |
| TTS synthesis | 300ms | ElevenLabs streaming |
| TTS → Channel | 100ms | Direct stream |

### Audio Format

- **Internal format:** 16-bit PCM, 16kHz mono (STT-optimized)
- **Twilio:** μ-law 8kHz (converted at adapter boundary)
- **WebRTC:** Opus codec (decoded at adapter boundary)
- **Output:** Adapter-specific (re-encoded per channel requirements)

## Monorepo Structure

```
packages/
├── core/          # Pipeline, interfaces, session management
│   └── src/
│       ├── pipeline.ts        # Pipeline orchestration
│       ├── interfaces/        # Channel, STT, TTS interfaces
│       ├── session.ts         # Agent session (OpenClaw WS)
│       └── audio.ts           # Audio format utilities
├── stt/           # STT adapter implementations
│   └── src/
│       ├── deepgram.ts
│       └── whisper.ts
├── tts/           # TTS adapter implementations
│   └── src/
│       ├── elevenlabs.ts
│       └── google.ts
└── channels/      # Channel adapter implementations
    └── src/
        ├── twilio.ts
        ├── meet.ts
        ├── slack.ts
        └── webrtc.ts
```

## Configuration

Environment-based configuration with sensible defaults:

```bash
# Core
ALFE_VOICE_PORT=3100
OPENCLAW_GATEWAY_URL=ws://localhost:3000

# STT
DEEPGRAM_API_KEY=
WHISPER_API_URL=

# TTS
ELEVENLABS_API_KEY=
ELEVENLABS_VOICE_ID=
GOOGLE_TTS_CREDENTIALS=

# Channels
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_PHONE_NUMBER=
SLACK_BOT_TOKEN=
SLACK_SIGNING_SECRET=
```

## Deployment

Target deployment: Docker container on Fly.io or similar, with Cloudflare tunnel for webhook endpoints. Domain: `voice.alfe.ai`.
