# Google Meet Channel

## Overview
Join Google Meet calls as a participant, listen to speech, and respond via the AI agent.

## How It Works

Extends patterns from the existing [voice-gateway](https://github.com/AlfeAI/voice-gateway) project.

1. **Join:** Headless browser (Puppeteer) joins a Meet link as a bot participant
2. **Capture:** WebRTC audio tracks captured from the meeting
3. **Pipeline:** Audio decoded from Opus → PCM → STT → Agent → TTS → Opus → injected back
4. **Multi-party:** Multiple audio tracks handled via speaker separation

## Configuration

```bash
GOOGLE_MEET_BOT_EMAIL=voice-bot@alfe.ai
GOOGLE_MEET_BOT_NAME="Alfe Voice"
```

## Key Considerations
- Meet may require Google Workspace account for bot access
- Bot appears as a named participant in the meeting
- Need to handle meeting permissions (admit from waiting room)
- Audio quality varies — need robust VAD and noise handling
- Existing voice-gateway code provides the WebRTC foundation
