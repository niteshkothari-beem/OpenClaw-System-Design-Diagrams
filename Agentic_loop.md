```mermaid
flowchart TD
    A(["User message arrives · or calendar fires"]):::start

    B["1. Load user profile · DynamoDB decrypt via KMS"]:::core
    C["2. Strip PII via VGS · PAN, SSN, account numbers removed"]:::sec
    D["3. Build system prompt · SOUL.md + user context + disclaimer"]:::core
    E["4. Call Bedrock Converse API · with all 15 tool definitions"]:::llm

    F{{"stopReason == tool_use?"}}:::decision

    G["5. Invoke skill Lambda · e.g. beem-mcp-tax-classifier"]:::skill
    H["6. Inject tool result · back into message thread"]:::core
    I["7. Call Bedrock again · continue reasoning"]:::llm

    J["8. Stream final response · via WebSocket to React Native"]:::out
    K["9. Extract entities · goals, preferences, actions"]:::core
    L["10. Save context · DynamoDB upsert"]:::db

    M(["User sees response · + one-click actions"]):::end

    A --> B --> C --> D --> E --> F
    F -->|"yes"| G --> H --> I --> F
    F -->|"no"| J --> K --> L --> M

    classDef start fill:#E1F5EE,stroke:#0F6E56,color:#085041
    classDef core fill:#EEEDFE,stroke:#534AB7,color:#26215C
    classDef sec fill:#FCEBEB,stroke:#A32D2D,color:#501313
    classDef llm fill:#534AB7,stroke:#3C3489,color:#EEEDFE
    classDef skill fill:#FAEEDA,stroke:#854F0B,color:#412402
    classDef decision fill:#FAC775,stroke:#BA7517,color:#412402
    classDef out fill:#E1F5EE,stroke:#0F6E56,color:#085041
    classDef db fill:#EAF3DE,stroke:#3B6D11,color:#173404
    classDef end fill:#1D9E75,stroke:#0F6E56,color:#E1F5EE
```
