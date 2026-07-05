### Sequence 1: Offline Habit Completion & Sync Queue

```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 User
    participant UI as 🎨 Flutter UI
    participant State as ⚙️ State Manager
    participant DB as 📦 Local DB (SQLite/Hive)
    participant Queue as 🔄 Sync Queue Manager
    participant Supa as ⚡ Supabase DB

    User->>UI: Taps "Complete Habit"
    UI->>State: Trigger completeHabit(habitId)
    
    %% Optimistic Local Write
    State->>DB: Save transaction (status=completed, synced=false)
    DB-->>State: Write Confirmation
    State->>Queue: Push to Outbound Sync Queue
    State-->>UI: Update UI state instantly (Success Animation 🎉)
    UI-->>User: Visual feedback (Habit checked off)

    %% Background Network Sync Trigger
    Note over Queue, Supa: Connection Monitor detects Internet access
    Queue->>Supa: POST /rest/v1/habit_logs (Batch sync payload)
    
    alt Sync Successful
        Supa-->>Queue: HTTP 201 Created (Server Ack)
        Queue->>DB: Update record status (synced=true)
        DB-->>Queue: Confirmed
        Queue->>State: Notify sync complete
    else Sync Fails (Network drop)
        Supa-->>Queue: HTTP 50x / Timeout
        Queue->>Queue: Retain item in queue & schedule exponential backoff retry
    end