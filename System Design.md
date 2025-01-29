

# High Level Architecture

```mermaid
flowchart TB
  subgraph Frontend
    A[Web - React / Angular] --> B[Mobile - React Native]
    A --> C[CDN - CloudFlare]
    B --> C
  end

  subgraph Backend
    D[API Gateway - Kong/NGINX] --> E[User Service]
    D --> F[Content Service]
    D --> G[Q&A Service]
    D --> H[Comment Service]
    D --> I[Challenge Service]
  end

  subgraph Database
    J[PostgreSQL] --> K[Structured Data: Users, Roadmaps]
    L[MongoDB] --> M[Unstructured Data: Comments, Stories]
    N[Redis] --> O[Caching: Quotes, Leaderboards]
    P[Elasticsearch] --> Q[Search: Posts, Skills]
  end

  subgraph Storage
    R[MinIO] --> S[Media Files]
    T[ImageMagick / FFmpeg] --> U[Media Optimization]
  end

  Frontend --> Backend
  Backend --> Database
  Backend --> Storage
```

# Microservices interaction
```mermaid
flowchart LR
  User((User)) --> Frontend
  Frontend --> API[API Gateway]
  API -->|Auth| UserService
  API -->|Roadmap| ContentService
  API -->|Q&A Threads| QAService
  API -->|Comments| CommentService
  API -->|Challenges| ChallengeService

  subgraph BackendServices
    UserService --> PostgreSQL
    ContentService --> PostgreSQL
    QAService --> MongoDB
    CommentService --> MongoDB
    ChallengeService --> Redis
  end
```

# Data FLow for Key Features
```mermaid
sequenceDiagram
  participant User
  participant Frontend
  participant Backend
  participant DB

  User->>Frontend: Submit Q&A Post
  Frontend->>Backend: POST /api/questions
  Backend->>DB: Save to MongoDB (Q&A Collection)
  Backend->>Elasticsearch: Index for Search
  Backend-->>Frontend: 201 Created
  Frontend-->>User: Confirmation

  User->>Frontend: Upload Media (Story/Post)
  Frontend->>Backend: POST /api/media
  Backend->>MinIO: Store File
  Backend->>FFmpeg: Resize/Compress
  Backend-->>Frontend: Media URL
```

# Deployment Pipeline
```mermaid
graph TD
  Code[GitHub/GitLab Repo] --> CI[CI/CD: GitHub Actions/Jenkins]
  CI --> Build[Docker Build]
  Build -->|Push Image| Registry[Docker Registry]
  Registry --> Deploy[Kubernetes -K3s]
  Deploy --> Monitor[Prometheus/Grafana]
  Monitor --> Alert[Alert on Metrics]
```

# Security Layer 
```mermaid
flowchart LR
  User --> Auth[Keycloak/OAuth2]
  Auth -->|JWT Token| API[API Gateway]
  API --> RateLimit[Rate Limiting]
  API --> Sanitize[Input Sanitization]
  API --> Backup[Encrypted Backups - BorgBase]
```

# Tools and Libraries Flow
```mermaid
graph LR
  A[Authentication] --> Keycloak, JWT, OAuth2
  B[Search] --> Elasticsearch, Apache Solr
  C[Real-Time] --> Socket.io, MQTT - for lightweight messaging
  D[Moderation] --> PerspectiveAPI - toxicity detection, custom rules engine
  E[Analytics] --> Matomo
  F[Notifications] --> Firebase Cloud Messaging - FCM, self-hosted Gotify
```
