# Phase 2 — Channels (Weeks 4-6)

## Goal
Same AI agent accessible via phone, Google Meet, Slack voice memos, and browser.

## Deliverables

### 1. Google Meet Channel
- Extend existing voice-gateway WebRTC patterns
- Join Meet calls programmatically (headless browser or API)
- Audio track capture and injection
- Meeting lifecycle events (join, leave, participants)

### 2. Slack Voice Memo Transcription
- Slack Events API webhook for `file_shared` events
- Voice memo file download (audio/webm, audio/mp4)
- Batch transcription via STT adapter
- Post agent response as text reply (or voice memo via TTS)

### 3. WebRTC Browser Client
- Minimal web client at voice.alfe.ai
- WebRTC peer connection to voice server
- Opus codec handling
- UI: push-to-talk and continuous modes
- Connection state management

### 4. Channel Routing
- Route incoming connections to correct agent session
- Session lookup by phone number, Meet ID, Slack user, etc.
- Multi-tenant support (multiple agents, multiple channels)
- Channel priority and fallback logic

## Success Criteria
- [ ] Google Meet: agent joins a call and responds to speech
- [ ] Slack: voice memo triggers transcription + agent response
- [ ] Browser: real-time voice conversation at voice.alfe.ai
- [ ] All channels route through the same pipeline
- [ ] Channel-specific metadata preserved (caller ID, meeting name, etc.)
