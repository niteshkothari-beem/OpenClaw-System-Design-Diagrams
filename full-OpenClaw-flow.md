```mermaid
flowchart TD

%% =======================
%% USER FLOW
%% =======================

A[User (React Native Copilot UI)] --> B[API Gateway]
B --> C[OpenClaw Agent Runtime (Lambda)]

C --> D[Fetch User Context (DynamoDB)]
D --> C

C --> E[LLM Reasoning (Bedrock / Claude)]

E -->|Tool Required| F[Select MCP Tool]
E -->|No Tool| G[Direct Response]

F --> H[Invoke MCP Tool (Lambda)]
H --> I[Tool Logic Execution]

I --> J[External APIs / Data Sources]
J --> I

I --> K[Structured JSON Output]
K --> C

C --> L[LLM Formats Response + Actions]
G --> L

L --> M[Response to UI]

%% =======================
%% USER ACTION FLOW
%% =======================

M --> N{User Clicks Action?}

N -->|Yes| O[Invoke Action MCP Tool / API]
O --> P[Execute Action (Astra / Upwardli / etc.)]
P --> Q[Send Event (Mixpanel / MoEngage)]
Q --> R[Return Confirmation to User]

N -->|No| S[End]

%% =======================
%% PROACTIVE FLOW
%% =======================

T[EventBridge Daily Trigger] --> U[Run Bedrock Inference on Users]
U --> V[Detect Insight / Risk]

V -->|Threshold Met| W[Invoke MCP Tool]
W --> X[Generate Insight / Offer]

X --> Y[Send Push Notification (MoEngage)]
Y --> A

V -->|No Insight| Z[End]

%% =======================
%% NOTES
%% =======================

subgraph MCP Tools
    H
    O
end

subgraph Data Layer
    D
    J
end
```
