# 🏗️ Technical Architecture — edugrow-ma-platform

```mermaid
flowchart TD
    classDef blue fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    
    subgraph Intake_Layer [Phase 1: Entry]
    W1[Intake WF]:::blue
    W2[Sourcing WF]:::blue
    end
    
    subgraph Analysis_Layer [Phase 2: Qualify]
    W3[AI Qual WF]:::blue
    W4[Buyer Match WF]:::blue
    end
    
    subgraph Closing_Layer [Phase 3: Execution]
    W5[DD Gate WF]:::blue
    W6[Audit/Close WF]:::blue
    end
    
    Intake_Layer --> Analysis_Layer
    Analysis_Layer --> Closing_Layer
    
    W3 -- Log --> Audit[(PostgreSQL Audit)]:::blue
    W5 -- Verify --> DS[DocuSign API]:::blue
    W6 -- Alert --> TG[Telegram Bot]:::blue
```

## Components
- **State-Machine Engine**: The core logic that ensures deals proceed through stages (Intake -> NDA -> DD -> Closing) without skipping steps.
- **AI Qualification**: A GPT-4 assisted module that summarizes deal decks and highlights risks for advisors.
- **Audit Module**: A centralized service that captures 100% of event data into an append-only PostgreSQL table.

## Data Flow
1. **Initiation**: A new deal is created in the intake workflow, triggering a state record in PostgreSQL.
2. **Analysis**: Documents are analyzed by AI to determine sector fit and financial viability.
3. **Execution**: The system automates NDA issuance via DocuSign and tracks signatures.
4. **Due Diligence**: A gated portal opens for buyers, where document access is logged and tracked.
5. **Notification**: Stakeholders receive real-time updates via Telegram and Email at every milestone.

## Resilience & Compliance
- **Dead-Letter Queues (DLQ)**: Failed DocuSign requests are moved to a DLQ for manual intervention rather than stopping the workflow.
- **Exponential Backoff**: 5× retry logic ensures that API outages don't break the long-running deal lifecycle.
- **Idempotency**: Workflow triggers are de-duplicated to prevent multiple NDAs from being sent for the same deal.

## Confidentiality
Due to the extreme sensitivity of M&A data, all database schemas, specific qualification prompts, and workflow JSON exports are strictly withheld.
