# Voice SDR Agent — Trinity Bridge

Signal-triggered outbound voice AI for B2B sales development. Built on Vapi + LiteLLM, targeting biotech/pharma/medtech quality and compliance decision-makers.

## What it does

Extends the [trinity-sdr-agent](https://github.com/nsharma1972/trinity-sdr-agent) email pipeline with an outbound voice layer:

1. Signal fires (FDA warning letter, 510(k) clearance, 10-K AI risk disclosure, etc.)
2. Email goes out → no reply in 5 days → voice follow-up call dispatched
3. Vapi places the call; AI agent identifies itself, references the specific regulatory trigger, offers a 20-minute discovery call with Narendra
4. If prospect answers: books via Cal.com tool call mid-conversation
5. If no answer: leaves an 18–25 second signal-specific voicemail (raises email reply rate 115%)
6. All outcomes logged to Postgres → reviewed in portal

## Architecture

```
Signal (openFDA/SEC/ClinTrials) → Score → Email → [5 days, no reply] → Voice
                                                                          ↓
                                                     Vapi (STT: Deepgram Nova-3)
                                                          ↓
                                               LiteLLM Router (oci-apps)
                                              /                          \
                                   Mistral Small 3 7B              GPT-4o mini
                                   (local, ~80ms)                  (cloud fallback)
                                              \                          /
                                           ElevenLabs Flash v2.5 (TTS)
                                                          ↓
                                             Cal.com booking tools
                                                          ↓
                                              Postgres → Portal review
```

## Stack

| Layer | Choice |
|---|---|
| Voice infra | Vapi |
| STT | Deepgram Nova-3 |
| TTS | ElevenLabs Flash v2.5 |
| LLM primary | Mistral Small 3 7B (local, Apache 2.0) |
| LLM fallback | GPT-4o mini |
| LLM router | LiteLLM (Complexity Router) |
| Meeting booking | Cal.com |
| Backend | Python / FastAPI |
| Database | Postgres |

## ICP

Biotech / Pharma / Medical Device companies:
- 300–3,000 employees, US-HQ, active FDA exposure
- Buyer personas: Head of Quality (Track A), Head of ClinOps (Track B), VP IT (Track C)
- Fit score ≥ 60 required before any outreach

## Compliance

- AI disclosure within first 5 seconds (FCC 2024 ruling)
- Business landlines only in Phase 1 (TCPA mobile consent requirement)
- DNC registry scrub before first call, every 31 days
- Real-time opt-out suppression (< 10 seconds)
- 8am–9pm prospect local time enforcement

## Docs

- [`docs/BLUEPRINT.md`](docs/BLUEPRINT.md) — full solution design (20K words, 34 ADRs)

## Status

Blueprint v1.0 complete. Implementation starting Phase 1 (MVP, 4 weeks).
