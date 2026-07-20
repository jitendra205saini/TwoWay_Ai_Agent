# Complete Flowchart — NotifyTechAI Two-Way Agent

This file captures the complete end-to-end flow for the NotifyTechAI two-way AI sales agent architecture shown in the existing diagram.

```mermaid
flowchart TD
  %% Top-level sources
  A1[Customer / WhatsApp / Voice] -->|Inbound webhook| B1[Channel Gateway]
  A2[CRM / Scheduler / Timer] -->|Outbound trigger| B2[Outbound Trigger Engine]
  A3[Manager / Dashboard] --> C1[Manager AI Request]

  subgraph Agent Architecture
    direction TB
    B1 --> D1["Enqueue agent.turn<br/> (trigger = INBOUND_MESSAGE)"]
    B2 --> D2[Eligibility Gate]
    D2 --> D3[Session Window Check]
    D3 --> D4["Enqueue agent.turn<br/> (trigger = SCHEDULED)"]
    D4 --> D1

    D1 --> E1[Conversation Lock + Debounce]
    E1 --> E2[Context Assembly]
    E2 --> F1[Guardrails PRE]
    F1 --> G1[Intent Detection + RAG Retrieval]
    G1 --> H1[LLM Tool Loop]
    H1 --> F2[Guardrails POST]
    F2 --> I1[Save agent_turn + message]
    I1 --> J1[Enqueue Outbound Sender]
    J1 --> K1["Outbound Sender<br/> (WhatsApp / Voice / Email)"]
    K1 --> L1[Delivery Status Update]
    I1 --> M1["Async Post-Turn Tasks<br/> (rescore, memory, next outbound)"]
  end

  subgraph Data + Services
    direction LR
    P1["PostgreSQL<br/> (source-of-truth)"]
    P2["Redis / MQ<br/> (BullMQ)"]
    P3["LLM Provider<br/> (Claude / STT / TTS / Embedding)"]
    P4["Tool Layer<br/> (RAG, pricing, booking, follow-up, lead updates, handoff)"]
  end

  E2 --> P1
  H1 --> P3
  H1 --> P4
  P4 --> P1
  P4 --> H1
  J1 --> P2

  subgraph Conversation State Machine
    direction LR
    S1[NEW] --> S2[ENGAGING]
    S2 --> S3[QUALIFIED]
    S3 --> S4[BOOKED]
    S2 --> S5[DORMANT]
    S5 -->|max 2 revivals| S6[CLOSED]
    S2 --> S7[HUMAN_HANDOFF]
    S7 --> S2[Human resolves → AI resumes]
    S6 --> S1[30 days later: new inbound]
  end

  subgraph Guardrails & Handoff
    direction TB
    F1 --> G2[Consent OPTED_IN?]
    F1 --> G3[Mode != HUMAN?]
    F1 --> G4[Rate limit / quiet hours?]
    F2 --> G5[Price / claim / PII check]
    F2 --> G6[Confidence >= 0.7?]
    G5 -->|fail| H2[Escalate to Human]
    G6 -->|fail| H2
    H2 --> H3["conversation.mode=HUMAN<br/> state=HUMAN_HANDOFF"]
    H3 --> H4[AI handoff brief + human notification]
    H4 --> H5[Bridge message to customer]
    H5 --> H7[AI silent, human resumes]
  end

  subgraph Manager AI
    C1 --> N1[Intent = ANALYTICS_QUERY]
    N1 --> N2[Authorized metric query builder]
    N2 --> P1
    N2 --> N3[Dashboard + insight response]
  end

  click B2 "#" "Outbound trigger engine"
  click D1 "#" "agent turn queue"
  click S1 "#" "conversation states"
```

## Key sections included

- Inbound workflow: webhook → enqueue → conversation lock → debounce → context assembly → guardrails → intent + RAG → LLM tool loop → output save → outbound sender → delivery update.
- Outbound workflow: scheduled / CRM-triggered jobs pass eligibility rules, session window checks, then use the same agent runtime and tools as inbound.
- Agent architecture: channel gateway + Redis/MQ + PostgreSQL + agent runtime + tool layer + LLM provider + outbound sender.
- Conversation states: NEW, ENGAGING, QUALIFIED, BOOKED, DORMANT, HUMAN_HANDOFF, CLOSED.
- Guardrails: pre-LLM consent/mode/rate-limit checks, post-LLM safety checks for price, PII, claim, confidence, and escalation to human.
- Handoff: AI prepares brief, sets mode=HUMAN, sends one bridge message, remains silent until human resolves.
- Manager AI: RBAC + typed analytics query builder, read replica access, narrative response.

## Notes

- The diagram reflects the existing two-way AI agent design and extends it into a unified flowchart for both reactive and proactive directions.
- It preserves the principle that the agent uses the same domain services as the CRM UI and never bypasses the authoritative database.
- It shows the critical 24-hour WhatsApp session window and the importance of eligibility checks before outbound messages.
