# Roadmap

## Phase 1 — Foundation (Weeks 1-3)

**Goal:** Core pipeline working end-to-end with a single channel (phone).

- [ ] Define `Channel` interface and adapter pattern
- [ ] Implement core pipeline: STT → Agent → TTS orchestration
- [ ] OpenClaw session integration (WebSocket gateway connection)
- [ ] Deepgram STT adapter (streaming)
- [ ] ElevenLabs TTS adapter (streaming)
- [ ] Twilio phone channel adapter (inbound calls)
- [ ] Local development setup with hot reload
- [ ] Basic error handling and reconnection logic

**Milestone:** Make a phone call → talk to an AI agent → hear response.

---

## Phase 2 — Channels (Weeks 4-6)

**Goal:** Multiple voice channels working through the same pipeline.

- [ ] Google Meet channel (extend voice-gateway WebRTC patterns)
- [ ] Slack voice memo transcription (webhook listener + file download + response)
- [ ] WebRTC browser client (web-based voice interface)
- [ ] Channel routing — route incoming audio to the correct agent session
- [ ] Outbound call support (Twilio)
- [ ] Channel health monitoring

**Milestone:** Same agent accessible via phone, Meet, Slack, and browser.

---

## Phase 3 — Intelligence (Weeks 7-9)

**Goal:** Smarter, more natural voice interactions.

- [ ] Cross-channel conversation context (persistent agent session across channels)
- [ ] Interruption handling / barge-in (stop TTS when user speaks)
- [ ] Speaker identification (who is talking in multi-party)
- [ ] Multi-party call support (conference calls, Meet rooms)
- [ ] Silence detection and turn-taking optimization
- [ ] Latency optimization (pipeline parallelism)

**Milestone:** Natural, context-aware conversations with interruption support.

---

## Phase 4 — Platform (Weeks 10-12)

**Goal:** Production-ready platform with management and billing.

- [ ] Dashboard UI for managing agents and channels
- [ ] Usage metering (per-minute billing model)
- [ ] REST API for third-party agent integration
- [ ] Documentation site at voice.alfe.ai
- [ ] Webhook notifications (call events, transcripts)
- [ ] Rate limiting and quota management

**Milestone:** External developers can connect their agents to Alfe Voice.
