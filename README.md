# CFO Brain 🧠

**Group CFO Command Center** — Live P&L dashboards with conversational AI analytics.

> From raw accounting data to "Why did margin drop last month?" in one platform.

## What This Is

A unified system that:
1. **Ingests** financial data from multiple business units (ERPs, spreadsheets, APIs)
2. **Normalizes** into a single analytical warehouse with consistent metrics
3. **Visualizes** via drill-down P&L dashboards (Group → BU → Channel → Customer → SKU)
4. **Answers questions** via natural language through an AI agent (OpenClaw integration)
5. **Alerts proactively** when margins compress, budgets miss, or anomalies appear

## Architecture Overview

```mermaid
graph TB
    subgraph Sources["📊 Data Sources"]
        ERP["ERP Systems<br/>(SAP, Oracle, Accurate)"]
        ACCT["Cloud Accounting<br/>(Jurnal.id, Xero)"]
        SHEETS["Google Sheets<br/>(Manual Templates)"]
        CRM["CRM / Sales<br/>Systems"]
        BANK["Bank APIs /<br/>Treasury"]
        MKT["Market Data<br/>(Commodities, FX)"]
    end

    subgraph Ingestion["⚙️ Ingestion Layer"]
        ETL["Python ETL<br/>Pipelines"]
        AIRBYTE["Airbyte<br/>Connectors"]
        WEBHOOK["Webhook<br/>Receivers"]
        VALIDATE["Validation &<br/>Quality Checks"]
    end

    subgraph Warehouse["🏗️ Analytics Warehouse"]
        STAGING["Staging Tables<br/>(raw, per-source)"]
        TRANSFORM["dbt Transforms<br/>(normalize, map COA)"]
        STAR["Star Schema<br/>(facts + dimensions)"]
        SEMANTIC["Semantic Layer<br/>(metric definitions)"]
    end

    subgraph Presentation["🖥️ Presentation Layer"]
        DASH["Live P&L<br/>Dashboard"]
        AGENT["Chat Agent<br/>(OpenClaw + LLM)"]
        ALERTS["Proactive<br/>Alerts"]
        REPORTS["Scheduled<br/>Reports"]
    end

    ERP --> ETL
    ACCT --> AIRBYTE
    SHEETS --> WEBHOOK
    CRM --> ETL
    BANK --> ETL
    MKT --> ETL

    ETL --> VALIDATE
    AIRBYTE --> VALIDATE
    WEBHOOK --> VALIDATE
    VALIDATE --> STAGING

    STAGING --> TRANSFORM
    TRANSFORM --> STAR
    STAR --> SEMANTIC

    SEMANTIC --> DASH
    SEMANTIC --> AGENT
    SEMANTIC --> ALERTS
    SEMANTIC --> REPORTS

    style Sources fill:#f0f4ff,stroke:#4a6cf7
    style Ingestion fill:#fff4e6,stroke:#f7944a
    style Warehouse fill:#e6fff0,stroke:#4af794
    style Presentation fill:#ffe6f0,stroke:#f74a94
```

## Dashboard Drill-Down Hierarchy

```mermaid
graph TD
    L0["🏢 Group Consolidated P&L"]
    L1A["⛏️ Mining BUs"]
    L1B["🏦 Financial Services BUs"]
    L1C["💻 Digital BUs"]
    L2A["📦 Channel: Domestic"]
    L2B["📦 Channel: Export"]
    L2C["📦 Channel: Online"]
    L3A["👤 Customer A"]
    L3B["👤 Customer B"]
    L4A["📋 SKU-001"]
    L4B["📋 SKU-002"]

    L0 --> L1A
    L0 --> L1B
    L0 --> L1C
    L1A --> L2A
    L1A --> L2B
    L1A --> L2C
    L2A --> L3A
    L2A --> L3B
    L3A --> L4A
    L3A --> L4B

    style L0 fill:#1a1a2e,color:#fff
    style L1A fill:#16213e,color:#fff
    style L1B fill:#16213e,color:#fff
    style L1C fill:#16213e,color:#fff
    style L2A fill:#0f3460,color:#fff
    style L2B fill:#0f3460,color:#fff
    style L2C fill:#0f3460,color:#fff
    style L3A fill:#533483,color:#fff
    style L3B fill:#533483,color:#fff
    style L4A fill:#e94560,color:#fff
    style L4B fill:#e94560,color:#fff
```

## Conversational Agent Flow

```mermaid
sequenceDiagram
    participant U as User (Telegram)
    participant OC as OpenClaw
    participant LLM as LLM (Opus)
    participant DB as Warehouse (Postgres)

    U->>OC: "Why did mining margin drop in March?"
    OC->>LLM: Schema + question → generate SQL
    LLM->>OC: SELECT query
    OC->>DB: Execute (read-only)
    DB->>OC: Result set
    OC->>LLM: Results → narrate insight
    LLM->>OC: "Mining gross margin fell from 34% to 28%..."
    OC->>U: Insight + chart
```

## Project Structure

```
cfo-brain/
├── README.md                    # This file
├── docs/
│   ├── architecture.md          # Detailed system architecture
│   ├── dashboard-template.md    # P&L dashboard design decisions
│   ├── data-model.md            # Star schema & dimension design
│   ├── agent-design.md          # Conversational agent architecture
│   ├── ingestion-guide.md       # How data flows in
│   └── decisions/               # Architecture Decision Records
│       └── 001-warehouse-choice.md
├── templates/
│   ├── pnl-template.md          # P&L line item structure
│   └── bu-onboarding.md         # Checklist for adding a new BU
├── scripts/
│   ├── ingestion/               # ETL scripts per source
│   └── queries/                 # Reference SQL queries
├── skills/
│   └── cfo-query/               # OpenClaw agent skill
│       ├── SKILL.md
│       ├── schema.md
│       ├── examples.md
│       ├── scripts/
│       └── references/
└── dashboard/                   # Next.js dashboard app (future)
```

## Build Phases

| Phase | Timeline | What Ships |
|-------|----------|------------|
| **0 — Data Audit** | Week 1 | Source inventory, access map, COA gaps identified |
| **1 — Warehouse** | Weeks 2-3 | Schema deployed, first BU data flowing |
| **2 — Dashboard v0** | Weeks 4-5 | Group + BU P&L with drill-down |
| **3 — Agent v0** | Weeks 6-7 | Text-to-SQL queries via Telegram |
| **4 — Full Dashboard** | Weeks 7-8 | All 5 levels, charts, budget vs actual |
| **5 — Automation** | Weeks 9-10 | Scheduled reports, proactive alerts |

## Key Documents

- [📐 Architecture Deep Dive](docs/architecture.md)
- [📊 Dashboard Template Decisions](docs/dashboard-template.md)
- [🗄️ Data Model & Schema](docs/data-model.md)
- [🤖 Agent Design](docs/agent-design.md)
- [📥 Ingestion Guide](docs/ingestion-guide.md)

---

*Built for the Group CFO who'd rather ask a question than open a spreadsheet.*
