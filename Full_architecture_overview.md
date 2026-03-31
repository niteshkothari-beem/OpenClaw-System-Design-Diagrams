```mermaid
%%{init: {'theme':'default', 'themeVariables': {
    'fontSize': '20px',
    'primaryTextColor': '#000',
    'lineColor': '#333'
}}}%%
flowchart TD
    RN["React Native app"]:::client
    WS["API Gateway WebSocket"]:::infra
    RUNTIME["beem-copilot-runtime Lambda · Agent Runtime"]:::core

    RN -->|"user message"| WS
    WS --> RUNTIME

    subgraph FOUNDATION["Phase 0 — Foundation"]
        SOUL["SOUL.md + openclaw.json · in S3"]:::store
        DISC["Disclaimer Registry · in S3"]:::store
    end

    RUNTIME -->|"load SOUL"| SOUL
    RUNTIME -->|"get disclaimer"| DISC

    subgraph SECURITY["Security"]
        KK["KMS CMK · per-user key"]:::sec
        VG["VGS tokenization · before Bedrock"]:::sec
    end

    RUNTIME -->|"decrypt"| KK
    RUNTIME -->|"strip PII"| VG

    subgraph BEDROCK["AWS Bedrock"]
        HAI["Claude Haiku · primary LLM"]:::llm
        SON["Claude Sonnet · quality tasks"]:::llm
    end

    VG --> RUNTIME
    RUNTIME -->|"converse"| HAI
    HAI -->|"tool_use"| RUNTIME

    subgraph DATASTORE["Data layer — DynamoDB"]
        UFP["user_financial_profiles"]:::db
        TXN["transactions"]:::db
        CAL["copilot_calendar"]:::db
        IQ["insights_queue"]:::db
    end

    RUNTIME -->|"load profile"| UFP
    RUNTIME -->|"invoke skill"| IQ

    subgraph CALENDAR["Calendar engine"]
        CP["CalendarPopulator"]:::sched
        CE["CalendarEvaluator"]:::sched
        CA["CalendarAPI"]:::sched
    end

    CE -->|"fire skill"| RUNTIME
    CP -->|"write entries"| CAL
    CE -->|"scan"| CAL

    subgraph ENRICHMENT["Background enrichment"]
        PE["profile-enricher"]:::bg
        TI["transaction-ingestor"]:::bg
    end

    PE -->|"update"| UFP
    TI -->|"write"| TXN
    PE -->|"read"| TXN

    classDef client fill:#E1F5EE,stroke:#0F6E56,color:#085041,font-size:18px
    classDef infra fill:#EEEDFE,stroke:#534AB7,color:#26215C,font-size:18px
    classDef core fill:#534AB7,stroke:#3C3489,color:#EEEDFE,font-size:18px
    classDef store fill:#FAEEDA,stroke:#854F0B,color:#412402,font-size:18px
    classDef db fill:#FAEEDA,stroke:#854F0B,color:#633806,font-size:18px
    classDef sched fill:#EEEDFE,stroke:#534AB7,color:#3C3489,font-size:18px
    classDef bg fill:#F1EFE8,stroke:#5F5E5A,color:#2C2C2A,font-size:18px
    classDef sec fill:#FCEBEB,stroke:#A32D2D,color:#501313,font-size:18px
    classDef llm fill:#E1F5EE,stroke:#0F6E56,color:#085041,font-size:18px

```
