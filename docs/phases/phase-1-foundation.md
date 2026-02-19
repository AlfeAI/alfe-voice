# Phase 1 — Foundation (Weeks 1-3)

## Goal
Core voice pipeline working end-to-end: make a phone call, talk to an AI agent, hear a response.

## Deliverables

### 1. Core Pipeline Abstraction
- Define `ChannelAdapter`, `STTAdapter`, `TTSAdapter` interfaces
- Implement `PipelineManager` to orchestrate STT → Agent → TTS flow
- Audio format utilities (PCM conversion, resampling)

### 2. OpenClaw Session Integration
- WebSocket client for OpenClaw gateway
- Session lifecycle management (create, resume, destroy)
- Message serialization/deserialization
- Reconnection logic with exponential backoff

### 3. Deepgram STT Adapter
- Streaming transcription via Deepgram WebSocket API
- Interim results for early processing
- Language detection support
- Error handling and stream recovery

### 4. ElevenLabs TTS Adapter
- Streaming synthesis via ElevenLabs API
- Voice selection and configuration
- Audio chunk streaming (don't wait for full synthesis)
- Rate limit handling

### 5. Twilio Phone Channel
- Inbound call webhook handler
- WebSocket media stream connection
- μ-law ↔ PCM audio conversion
- Call lifecycle events (ringing, answered, ended)
- Basic DTMF handling

### 6. Local Development
- pnpm workspace setup with hot reload
- Docker Compose for local Twilio testing (ngrok tunnel)
- Example `.env` with all required keys
- Basic logging and debugging tools

## Success Criteria
- [ ] Inbound Twilio call connects to pipeline
- [ ] Speech is transcribed in real-time via Deepgram
- [ ] Transcription is sent to OpenClaw agent
- [ ] Agent response is synthesized via ElevenLabs
- [ ] Audio response plays back to caller
- [ ] End-to-end latency < 2 seconds
