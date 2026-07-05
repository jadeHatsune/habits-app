## 6. Daily Midnight Rollover & Streak Verification

## This details how the application manages calendar rollover logic to evaluate incomplete habits and calculate streak updates when a day changes.


sequenceDiagram
    autonumber
    participant Cron as ⏱️ Supabase Cron / Edge Function Daemon
    participant Postgres as 🐘 Postgres DB
    participant Push as 🔔 Notification Engine (FCM)

    Cron->>Postgres: Trigger midnight calculation script (Per timezone group)
    
    Note over Postgres: Query active user habits target goals vs completed inputs for yesterday
    
    Postgres->>Postgres: Identify broken chains (Habits missing completed status)
    Postgres->>Postgres: UPDATE profiles SET current_streak = 0 WHERE chain_broken = true
    
    Postgres->>Postgres: Identify maintained chains
    Postgres->>Postgres: UPDATE profiles SET current_streak = current_streak + 1 WHERE chain_maintained = true
    
    Postgres-->>Cron: Return summary of modified user profiles
    
    Cron->>Push: Trigger notification requests for streak alert configurations
    Push-->>Cron: Push alerts distributed to client devices