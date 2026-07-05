

## 2. Background Native Health API Automation
This diagram illustrates how the background execution worker interfaces with the native device health silos (Apple HealthKit / Android Health Connect) to check off automated telemetry habits.

### Sequence 2: Background Native Health API Automation

```mermaid
sequenceDiagram
    autonumber
    participant OS as ⏱️ OS Background Scheduler
    participant Worker as 🤖 Flutter Background Worker
    participant HealthAPI as 🧬 Native Health API (HK/HC)
    participant LocalDB as 📦 Local DB (SQLite/Hive)
    participant Supa as ⚡ Supabase DB

    OS->>Worker: Wake up worker (Interval trigger, e.g., 1 hour)
    Worker->>LocalDB: Fetch habits marked with "Auto-Track"
    LocalDB-->>Worker: List of auto-habits (e.g., Step Counter, Sleep tracking)
    
    Worker->>HealthAPI: Request specific telemetry metrics (Current Day)
    HealthAPI-->>Worker: Return metric data payload (e.g., 10,250 steps)
    
    Worker->>Worker: Evaluate habit metrics target criteria
    
    alt Target Goal Met
        Worker->>LocalDB: Log automated completion entry
        Worker->>Supa: Secure Upsert: Log habit completion to remote DB
        Supa-->>Worker: Confirm remote save
    else Target Goal Not Met Yet
        Worker->>LocalDB: Cache current progress state
    end
    
    Worker-->>OS: Signal work completion execution task (Sleep mode)