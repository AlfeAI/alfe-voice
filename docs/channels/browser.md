# Browser Channel (WebRTC)

## Overview
Real-time voice conversation with an AI agent directly in the browser at voice.alfe.ai.

## How It Works

1. **Connect:** Browser opens WebRTC peer connection to Alfe Voice server
2. **Capture:** `getUserMedia()` captures microphone audio
3. **Stream:** Opus-encoded audio sent via WebRTC data/media track
4. **Pipeline:** Server decodes Opus → PCM → STT → Agent → TTS → Opus → streams back
5. **Playback:** Browser receives and plays TTS audio

## UI Modes

- **Push-to-talk:** Hold button to speak, release to send
- **Continuous:** Always listening with VAD-based turn detection
- **Text fallback:** Type messages if mic unavailable

## Configuration

Server-side only — browser client connects via WebSocket signaling.

```bash
WEBRTC_STUN_SERVER=stun:stun.l.google.com:19302
WEBRTC_TURN_SERVER=  # Optional, for NAT traversal
```

## Key Considerations
- HTTPS required for `getUserMedia()`
- Opus codec provides good quality at low bandwidth
- Signaling via WebSocket, media via WebRTC
- Mobile browser support (iOS Safari, Android Chrome)
- Connection quality indicators in UI
