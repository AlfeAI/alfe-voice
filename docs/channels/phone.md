# Phone Channel (Twilio)

## Overview
Inbound and outbound phone calls via Twilio Programmable Voice.

## How It Works

1. **Inbound:** Twilio receives a call → webhook hits Alfe Voice → TwiML connects WebSocket media stream
2. **Audio:** Twilio streams μ-law 8kHz audio over WebSocket → adapter converts to 16-bit PCM 16kHz
3. **Pipeline:** PCM audio flows through STT → Agent → TTS pipeline
4. **Response:** TTS audio converted back to μ-law → streamed to Twilio WebSocket → caller hears response

## Configuration

```bash
TWILIO_ACCOUNT_SID=ACxxxxxxxx
TWILIO_AUTH_TOKEN=xxxxxxxx
TWILIO_PHONE_NUMBER=+1234567890
```

## Webhook Setup

Configure your Twilio phone number's voice webhook to:
```
POST https://voice.alfe.ai/channels/twilio/incoming
```

## Audio Format
- **Twilio sends:** μ-law 8kHz mono
- **Internal:** 16-bit PCM 16kHz mono
- **Conversion:** Handled at adapter boundary (both directions)

## Key Considerations
- Twilio WebSocket media streams have a 2-channel format (inbound + outbound marks)
- DTMF tones can be captured for IVR-style interactions
- Call recording available via Twilio API
- Maximum call duration: configurable, default 30 minutes
