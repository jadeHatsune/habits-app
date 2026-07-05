# Architectural Component Diagram - 'Habits' Project

This diagram represents the high-level modular architecture of the 'Habits' application. It maps the structural boundaries between the Flutter client, local device APIs, the Supabase BaaS, and external AI systems.

```mermaid
graph TB
    %% Styling Configuration
    classDef frontend fill:#1389FD,stroke:#0A5AAB,color:#fff,stroke-width:2px;
    classDef storage fill:#F39C12,stroke:#D35400,color:#fff,stroke-width:2px;
    classDef backend fill:#3ECF8E,stroke:#249F65,color:#fff,stroke-width:2px;
    classDef ai fill:#9B59B6,stroke:#8E44AD,color:#fff,stroke-width:2px;
    classDef native fill:#7F8C8D,stroke:#34495E,color:#fff,stroke-width:2px;

    %% Subgraph: Client Architecture (Flutter)
    subgraph Client [📱 Flutter Cross-Platform Client]
        UI[🎨 UI Components & Views]
        State[⚙️ State Management Layer]
        Sync[🔄 Synchronization Manager]
        LocalDB[(📦 SQLite / Hive Offline Store)]
        
        UI --> State
        State --> Sync
        Sync <--> LocalDB
    end

    %% Subgraph: Native Device Bridging
    subgraph Device [🧬 Native OS Integrations]
        HK[🍎 Apple HealthKit]
        HC[🤖 Android Health Connect]
    end

    %% Subgraph: Backend-as-a-Service (Supabase)
    subgraph BaaS [⚡ Supabase Ecosystem]
        Auth[🔐 Supabase Auth]
        Gateway[🌐 Edge Functions / REST API]
        DB[(🐘 PostgreSQL Database)]
        Realtime[🛰️ Realtime Engine]

        Gateway --> Auth
        Gateway --> DB
        Realtime --> DB
    end

    %% Subgraph: AI Intelligence Engines
    subgraph AI [🧠 Intelligence Layer]
        LocalAI[📱 Local LLM Engine <br> Qwen3 VL 4B / GPT-4.1 Nano]
        RemoteAI[☁️ Cloud AI Orchestration]
    end

    %% Component Interconnections (Data Flow)
    State ==>|Query / Update| Device
    Sync ==>|HTTPS / WebSockets| Gateway
    Sync -.->|Real-time Streams| Realtime
    
    %% AI System Communications
    State -->|Contextual Prompt Processing| LocalAI
    Gateway -->|Heavy Batch Analysis| RemoteAI

    %% Assigning Classes for Visual Identification
    class UI,State,Sync frontend;
    class LocalDB storage;
    class Auth,Gateway,DB,Realtime backend;
    class LocalAI,RemoteAI ai;
    class HK,HC native;