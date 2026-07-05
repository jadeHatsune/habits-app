## 4. App Initialization & Sync Hydration (Delta Sync)

### When the user cold-boots the application, this design manages how local data hydrates instantaneously to render the UI, checking for data deltas right after.


sequenceDiagram
    autonumber
    actor User as 👤 User
    participant UI as 🎨 Flutter UI
    participant State as ⚙️ State Manager
    participant LocalDB as 📦 Local DB (SQLite/Hive)
    participant Supa as ⚡ Supabase DB

    User->>UI: Launches "Habits" App
    UI->>State: Initialize App State Engine
    
    %% Instant Local Hydration
    State->>LocalDB: Fetch last known cached state
    LocalDB-->>State: Return local habit arrays
    State->>UI: Populate interface immediately (Zero-loading lag)
    
    %% Asynchronous Delta Validation
    State->>LocalDB: Get maximum `updated_at` timestamp from local records
    LocalDB-->>State: Returns timestamp (t)
    
    State->>Supa: Fetch habit changes where `updated_at` > timestamp (t)
    
    alt Remote Delta Changes Found
        Supa-->>State: Return newly modified cloud objects
        State->>LocalDB: Overwrite / Patch local storage records
        State->>UI: Smoothly update active view state
    else No New Remote Changes
        Supa-->>State: Return Empty Set (HTTP 304 / Equivalent)
    end