# ADR-001: Channel Abstraction Pattern

## Status
Accepted

## Context
Alfe Voice needs to support multiple voice channels (phone, Meet, Slack, browser) through a unified pipeline. Each channel has different transport mechanisms, audio formats, and interaction patterns.

## Decision
Use an **adapter pattern** where each channel implements a standard `ChannelAdapter` interface. The pipeline manager interacts only with this interface, remaining agnostic to the underlying transport.

### Key Design Choices

1. **Audio normalization at the boundary** — Each adapter converts its native audio format to internal PCM (16-bit, 16kHz mono). This keeps the pipeline simple and adapter-specific complexity contained.

2. **Event-driven architecture** — Adapters emit events (audio chunks, connection state, metadata) rather than exposing transport details. Pipeline subscribes to these events.

3. **Separate STT/TTS adapters** — STT and TTS are not part of the channel adapter. They're independent adapters that the pipeline composes. This allows mixing providers (e.g., Deepgram STT + ElevenLabs TTS).

4. **Session binding** — The pipeline manager maps channel connections to agent sessions. This mapping is external to both the channel and agent adapters.

## Consequences

**Positive:**
- New channels added without modifying pipeline logic
- STT/TTS providers swappable independently
- Clean testing boundaries (mock any adapter)

**Negative:**
- Audio format conversion adds latency at boundaries (~5-10ms, acceptable)
- Some channel-specific features may not map cleanly to the generic interface
- Need escape hatches for channel-specific behavior (metadata passthrough)

## Alternatives Considered

1. **Monolithic handler per channel** — Each channel implements full STT→Agent→TTS. Rejected: massive duplication.
2. **Plugin system** — Dynamic loading of channel plugins. Rejected: premature complexity for 4 known channels.
