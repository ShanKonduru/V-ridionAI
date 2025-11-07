# Véridion System Architecture

## High-Level Architecture Diagram

```mermaid
graph TB
    subgraph "Input Layer"
        A[Unstructured Input] --> B[Input Parser]
        B --> C[NLP Preprocessor]
    end
    
    subgraph "AI Processing Engine"
        C --> D[Requirement Synthesizer]
        D --> E[Ambiguity Detector]
        E --> F[Test Case Generator]
        F --> G[Script Generator]
    end
    
    subgraph "Automation Layer"
        G --> H[CI/CD Pipeline]
        H --> I[Test Executor]
        I --> J[Result Collector]
    end
    
    subgraph "Intelligence Layer"
        J --> K[Root Cause Analyzer]
        K --> L[Bug Triager]
        L --> M[Report Generator]
    end
    
    subgraph "Data Layer"
        N[(PostgreSQL)]
        O[(MongoDB)]
        P[(Redis Cache)]
    end
    
    subgraph "External Integrations"
        Q[OpenAI API]
        R[Jira/Azure DevOps]
        S[GitHub/GitLab]
        T[Slack/Email]
    end
    
    D -.-> Q
    E -.-> Q
    F -.-> Q
    K -.-> Q
    L -.-> Q
    
    D --> N
    F --> N
    G --> N
    J --> O
    
    D --> P
    F --> P
    
    L --> R
    H --> S
    M --> T
    
    style A fill:#e1f5ff
    style D fill:#fff3cd
    style F fill:#fff3cd
    style G fill:#fff3cd
    style K fill:#fff3cd
    style L fill:#fff3cd
    style Q fill:#d4edda
```

## Component Communication Flow

```mermaid
sequenceDiagram
    participant User
    participant WebUI
    participant API
    participant AIEngine
    participant Database
    participant CICD
    participant ExtSys
    
    User->>WebUI: Submit unstructured input
    WebUI->>API: POST /requirements/synthesize
    API->>AIEngine: Process with GPT-4
    AIEngine->>Database: Save requirement
    Database-->>API: req_id
    API-->>WebUI: Display user story + AC
    
    User->>WebUI: Approve requirement
    WebUI->>API: PATCH /requirements/{id}/approve
    API->>AIEngine: Generate test cases
    AIEngine->>Database: Save test cases
    Database-->>API: tc_ids[]
    API-->>WebUI: Display test cases
    
    User->>WebUI: Generate scripts
    WebUI->>API: POST /scripts/generate
    API->>AIEngine: Generate automation code
    AIEngine->>Database: Save scripts
    API->>CICD: Deploy to pipeline
    CICD-->>API: deployment_status
    API-->>WebUI: Script ready
    
    CICD->>CICD: Run tests (scheduled/triggered)
    CICD->>API: POST /executions/results
    API->>Database: Save execution logs
    API->>AIEngine: Analyze failures
    AIEngine->>ExtSys: Create Jira ticket
    ExtSys-->>API: ticket_id
    API-->>WebUI: Update dashboard
```

## Data Flow Architecture

```mermaid
flowchart LR
    A[Raw Input] --> B{Input Type?}
    B -->|Text| C[Text Processor]
    B -->|Voice| D[Speech-to-Text]
    B -->|File| E[Document Parser]
    
    C --> F[Requirement DB]
    D --> F
    E --> F
    
    F --> G[AI Synthesis]
    G --> H[Test Case DB]
    
    H --> I[Script Generator]
    I --> J[Script Repository]
    
    J --> K[CI/CD Pipeline]
    K --> L[Execution Logs]
    
    L --> M{Test Status?}
    M -->|Pass| N[Success Reports]
    M -->|Fail| O[AI Triager]
    
    O --> P[Bug Tracker]
    N --> Q[Analytics Dashboard]
    P --> Q
```

## Microservices Architecture

```mermaid
graph TB
    subgraph "API Gateway"
        GW[Kong/Nginx Gateway]
    end
    
    subgraph "Core Services"
        RS[Requirement Service]
        TCS[Test Case Service]
        SS[Script Service]
        ES[Execution Service]
    end
    
    subgraph "AI Services"
        NLP[NLP Service]
        TCG[Test Gen Service]
        SG[Script Gen Service]
        RCA[Root Cause Service]
    end
    
    subgraph "Integration Services"
        CICD[CI/CD Service]
        JIRA[Jira Connector]
        NOTIF[Notification Service]
    end
    
    subgraph "Data Services"
        PSQL[(PostgreSQL)]
        MONGO[(MongoDB)]
        REDIS[(Redis)]
    end
    
    GW --> RS
    GW --> TCS
    GW --> SS
    GW --> ES
    
    RS --> NLP
    TCS --> TCG
    SS --> SG
    ES --> RCA
    
    RS --> PSQL
    TCS --> PSQL
    SS --> PSQL
    ES --> MONGO
    
    NLP --> REDIS
    TCG --> REDIS
    
    SS --> CICD
    RCA --> JIRA
    ES --> NOTIF
    
    style NLP fill:#fff3cd
    style TCG fill:#fff3cd
    style SG fill:#fff3cd
    style RCA fill:#fff3cd
```

## Deployment Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        WEB[Web Browser]
        MOB[Mobile App]
    end
    
    subgraph "CDN/Load Balancer"
        LB[Load Balancer]
    end
    
    subgraph "Kubernetes Cluster"
        subgraph "Frontend Pods"
            FE1[React UI - Pod 1]
            FE2[React UI - Pod 2]
        end
        
        subgraph "Backend Pods"
            BE1[API Server - Pod 1]
            BE2[API Server - Pod 2]
            BE3[API Server - Pod 3]
        end
        
        subgraph "AI Worker Pods"
            AI1[AI Worker 1]
            AI2[AI Worker 2]
            AI3[AI Worker 3]
        end
        
        subgraph "Job Runners"
            JOB1[Test Executor 1]
            JOB2[Test Executor 2]
        end
    end
    
    subgraph "Data Tier"
        DB[(PostgreSQL Cluster)]
        DOC[(MongoDB Cluster)]
        CACHE[(Redis Cluster)]
    end
    
    subgraph "External Services"
        OPENAI[OpenAI API]
        GIT[GitHub/GitLab]
        TRACK[Jira/ADO]
    end
    
    WEB --> LB
    MOB --> LB
    LB --> FE1
    LB --> FE2
    FE1 --> BE1
    FE2 --> BE2
    BE1 --> AI1
    BE2 --> AI2
    BE3 --> AI3
    
    BE1 --> DB
    BE2 --> DB
    BE3 --> DB
    
    AI1 --> DOC
    AI2 --> DOC
    
    AI1 --> CACHE
    AI2 --> CACHE
    
    AI1 --> OPENAI
    JOB1 --> GIT
    AI3 --> TRACK
    
    style AI1 fill:#fff3cd
    style AI2 fill:#fff3cd
    style AI3 fill:#fff3cd
```

## Security Architecture

```mermaid
graph TB
    subgraph "Security Perimeter"
        WAF[Web Application Firewall]
    end
    
    subgraph "Authentication Layer"
        AUTH[OAuth 2.0 / OIDC]
        SSO[Single Sign-On]
    end
    
    subgraph "Authorization Layer"
        RBAC[Role-Based Access Control]
        IAM[Identity & Access Management]
    end
    
    subgraph "Encryption Layer"
        TLS[TLS/SSL Encryption]
        VAULT[Secrets Vault]
    end
    
    subgraph "Application Layer"
        API[API Services]
        AI[AI Services]
    end
    
    subgraph "Data Protection"
        ENC[(Encrypted at Rest)]
        MASK[Data Masking]
        AUDIT[Audit Logs]
    end
    
    WAF --> AUTH
    AUTH --> SSO
    SSO --> RBAC
    RBAC --> IAM
    IAM --> TLS
    TLS --> API
    TLS --> AI
    
    VAULT --> API
    VAULT --> AI
    
    API --> ENC
    AI --> MASK
    API --> AUDIT
    AI --> AUDIT
    
    style VAULT fill:#d4edda
    style ENC fill:#d4edda
    style AUDIT fill:#d4edda
```
