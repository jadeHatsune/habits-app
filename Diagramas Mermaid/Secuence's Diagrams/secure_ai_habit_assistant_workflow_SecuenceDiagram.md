## 3. Secure AI Habit Assistant Execution

## This maps out the workflow of your conversational AI interface, securely routing requests via Supabase Edge Functions to ensure API credentials remain unexposed.

sequenceDiagram
    autonumber
    actor User as 👤 User
    participant UI as 🎨 Flutter UI
    participant State as ⚙️ State Manager
    participant Edge as ⚡ Supabase Edge Function
    participant Postgres as 🐘 Postgres DB
    participant LLM as 🧠 LLM Provider (Qwen/GPT)

    User->>UI: Submits text prompt ("Optimize my routine")
    UI->>State: Collect Contextual State Data
    State->>State: Bundle user prompt + current habit stats + telemetry
    
    State->>Edge: POST /functions/v1/habit-coach (Payload + JWT Bearer Token)
    
    Note over Edge: Edge Function validates user JWT session token securely
    Edge->>Postgres: Query historical trends / user metrics profile
    Postgres-->>Edge: Return raw structured timeline data
    
    Edge->>Edge: Inject system prompt instructions & construct contextual matrix
    
    Edge->>LLM: Secure HTTPS POST request with System + User payload
    LLM-->>Edge: Return structured token response stream (AI Advice string)
    
    Edge->>Postgres: Log AI interaction entry to audit history
    Edge-->>State: Return HTTP 200 containing parsed AI response
    State->>UI: Update Conversational State Vector
    UI-->>User: Render highly personalized AI guidance message