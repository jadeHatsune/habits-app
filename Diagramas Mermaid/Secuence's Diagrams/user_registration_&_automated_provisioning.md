## 5. User Registration & Automated Provisioning

## This captures the architecture's onboarding stream, detailing how a brand-new user record creates dependent profile state metadata utilizing structural infrastructure automation.

sequenceDiagram
    autonumber
    actor User as 👤 User
    participant UI as 🎨 Flutter UI
    participant Auth as 🔐 Supabase Auth Module
    participant Trigger as 🛠️ Postgres DB Trigger
    participant ProfileDB as 🐘 Postgres Profiles Table

    User->>UI: Enter Email/Password & Click Sign Up
    UI->>Auth: Request SignUp execution (email, password)
    
    Note over Auth: Validates email formatting & applies cryptography salting
    Auth->>Auth: Write internal auth user row
    
    %% Automated Server-Side Infrastructure Pipeline
    critical Automated Database Lifecycle Event
        Auth->>Trigger: Fire `after_user_created` Row Trigger
        Trigger->>ProfileDB: INSERT Into public.profiles (id, default_xp, streak_count)
        ProfileDB-->>Trigger: Row Initialization Acknowledged
    end
    
    Auth-->>UI: Return Access Token & Session Object
    UI->>UI: Authenticate instance state
    UI-->>User: Present customized onboarding setup sequence