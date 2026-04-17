# 📐 Architecture Deep Dive

## System Architecture

### Design Principles

1. **Start lean, scale when needed** — Postgres before ClickHouse, Vercel before AWS
2. **One schema to rule them all** — every BU's data maps to the same star schema
3. **Semantic layer is the API** — dashboards and agents query metrics, not raw tables
4. **Security by default** — RLS at the data layer, not just the UI
5. **Conversational-first** — if you can't ask it in English, the system isn't done

---

## Component Architecture

```mermaid
graph TB
    subgraph Client["🖥️ Client Layer"]
        BROWSER["Web Browser<br/>(Next.js Dashboard)"]
        TELEGRAM["Telegram<br/>(OpenClaw Agent)"]
        EMAIL["Email<br/>(Scheduled Reports)"]
    end

    subgraph API["🔌 API Layer"]
        NEXTAPI["Next.js API Routes<br/>(Dashboard queries)"]
        SUPAEDGE["Supabase Edge Functions<br/>(Webhooks, transforms)"]
        OCSKILL["OpenClaw Skill<br/>(cfo-query)"]
    end

    subgraph Compute["⚡ Compute Layer"]
        LLM["LLM (Claude Opus)<br/>SQL generation +<br/>insight narration"]
        DBT["dbt Core<br/>Transform &<br/>metric definitions"]
        CRON["Cron Jobs<br/>Ingestion schedules<br/>& alert checks"]
    end

    subgraph Storage["💾 Storage Layer"]
        PG["Supabase Postgres<br/>(Analytics Warehouse)"]
        GSHEETS["Google Sheets<br/>(Input templates)"]
        S3["Supabase Storage<br/>(Report PDFs, exports)"]
    end

    BROWSER --> NEXTAPI
    TELEGRAM --> OCSKILL
    NEXTAPI --> PG
    OCSKILL --> LLM
    LLM --> PG
    SUPAEDGE --> PG
    DBT --> PG
    CRON --> DBT
    CRON --> SUPAEDGE
    CRON --> EMAIL
    GSHEETS --> SUPAEDGE

    style Client fill:#f0f4ff,stroke:#4a6cf7
    style API fill:#fff4e6,stroke:#f7944a
    style Compute fill:#e6fff0,stroke:#4af794
    style Storage fill:#ffe6f0,stroke:#f74a94
```

---

## Data Flow — End to End

### Ingestion Pipelines

```mermaid
flowchart LR
    subgraph Sources
        S1["ERP Export<br/>(CSV/API)"]
        S2["Accounting API<br/>(Jurnal/Xero)"]
        S3["Google Sheet<br/>(Manual entry)"]
        S4["Bank Statement<br/>(CSV/API)"]
    end

    subgraph Staging["Staging (raw)"]
        R1["raw_erp_gl"]
        R2["raw_accounting_txn"]
        R3["raw_sheet_upload"]
        R4["raw_bank_txn"]
    end

    subgraph Transform["Transform (dbt)"]
        T1["stg_general_ledger"]
        T2["stg_transactions"]
        T3["int_unified_journal"]
        T4["int_coa_mapped"]
    end

    subgraph Mart["Mart (star schema)"]
        F["fact_pnl_line_item"]
        D1["dim_business_unit"]
        D2["dim_channel"]
        D3["dim_customer"]
        D4["dim_product"]
        D5["dim_date"]
        D6["dim_account"]
    end

    S1 --> R1
    S2 --> R2
    S3 --> R3
    S4 --> R4

    R1 --> T1
    R2 --> T2
    R3 --> T1
    R4 --> T2

    T1 --> T3
    T2 --> T3
    T3 --> T4
    T4 --> F

    F --- D1
    F --- D2
    F --- D3
    F --- D4
    F --- D5
    F --- D6

    style Sources fill:#f0f4ff,stroke:#4a6cf7
    style Staging fill:#fff4e6,stroke:#f7944a
    style Transform fill:#e6fff0,stroke:#4af794
    style Mart fill:#ffe6f0,stroke:#f74a94
```

### Validation Rules

Every record passes through validation before landing in staging:

| Rule | Action on Fail |
|------|---------------|
| Period is a valid fiscal month | Reject + alert |
| BU code exists in dim_business_unit | Reject + alert |
| Account code maps to COA mapping table | Quarantine + flag |
| Debit = Credit (double-entry check) | Quarantine + flag |
| Amount is within 3σ of historical average | Accept + flag as anomaly |
| Duplicate detection (source_id + period) | Skip (idempotent) |

---

## Tech Stack Details

### Chosen Stack (v1)

```mermaid
graph LR
    subgraph Frontend
        NEXT["Next.js 15<br/>App Router"]
        TREMOR["Tremor<br/>Chart components"]
        TAILWIND["Tailwind CSS"]
    end

    subgraph Backend
        SUPA["Supabase<br/>Postgres + Auth + Edge"]
        DBT2["dbt Core<br/>Transforms"]
        PY["Python 3.12<br/>ETL scripts"]
    end

    subgraph AI
        OPUS["Claude Opus<br/>SQL + Narration"]
        OC2["OpenClaw<br/>Agent framework"]
    end

    subgraph Infra
        VERCEL["Vercel<br/>Hosting"]
        GH["GitHub<br/>CI/CD"]
        CRON2["Mac Mini cron<br/>or GitHub Actions"]
    end

    NEXT --> SUPA
    NEXT --> TREMOR
    DBT2 --> SUPA
    PY --> SUPA
    OC2 --> OPUS
    OPUS --> SUPA
    NEXT --> VERCEL
    GH --> VERCEL

    style Frontend fill:#f0f4ff,stroke:#4a6cf7
    style Backend fill:#e6fff0,stroke:#4af794
    style AI fill:#ffe6f0,stroke:#f74a94
    style Infra fill:#fff4e6,stroke:#f7944a
```

### Why These Choices

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Database** | Supabase Postgres | Known stack, RLS built-in, free tier generous, Edge Functions for webhooks |
| **Transform** | dbt Core | Industry standard, version-controlled SQL, metric definitions |
| **Frontend** | Next.js + Tremor | React ecosystem (known), Tremor = beautiful charts with minimal code |
| **AI** | Claude Opus via OpenClaw | Best judgment for SQL generation, already integrated |
| **Hosting** | Vercel | Known deploy flow, preview URLs per PR |
| **ETL** | Python scripts | Flexible, handles any source format |
| **Scheduling** | Mac Mini cron | Already running 24/7, no extra infra |

### Upgrade Path

| When | Migrate From | Migrate To | Trigger |
|------|-------------|------------|---------|
| >1M rows/month | Postgres | ClickHouse or BigQuery | Query latency >2s on aggregations |
| >10 concurrent users | Vercel Hobby | Vercel Pro | Bandwidth / function limits |
| Complex orchestration | cron scripts | Dagster or Prefect | >10 interdependent pipelines |
| Real-time data needed | Batch ETL | Kafka + streaming | BU provides real-time API |

---

## Security Architecture

```mermaid
graph TD
    subgraph Auth["🔐 Authentication"]
        SAUTH["Supabase Auth<br/>(email + magic link)"]
        ROLES["Role Assignment<br/>(group_cfo, bu_cfo, controller, viewer)"]
    end

    subgraph Access["🛡️ Authorization"]
        RLS["Row-Level Security<br/>(Postgres policies)"]
        BUFILTER["BU Filter<br/>(user.bu_access[] array)"]
        READONLY["Read-Only Role<br/>(for agent queries)"]
    end

    subgraph Data["📊 Data Protection"]
        ENCRYPT["Encryption at Rest<br/>(Supabase default)"]
        TLS["TLS in Transit"]
        AUDIT["Query Audit Log"]
    end

    SAUTH --> ROLES
    ROLES --> RLS
    RLS --> BUFILTER
    BUFILTER --> PG2["Postgres"]
    READONLY --> PG2

    style Auth fill:#f0f4ff,stroke:#4a6cf7
    style Access fill:#ffe6f0,stroke:#f74a94
    style Data fill:#e6fff0,stroke:#4af794
```

### RLS Policy Example

```sql
-- Users can only see P&L data for their assigned BUs
CREATE POLICY "bu_access_policy" ON fact_pnl_line_item
    FOR SELECT
    USING (
        bu_id = ANY(
            SELECT unnest(bu_access)
            FROM user_profiles
            WHERE user_id = auth.uid()
        )
        OR
        EXISTS (
            SELECT 1 FROM user_profiles
            WHERE user_id = auth.uid()
            AND role IN ('group_cfo', 'group_ceo')
        )
    );
```

---

## Deployment Architecture

```mermaid
graph LR
    subgraph Dev["Development"]
        LOCAL["Local Dev<br/>(Mac Mini)"]
        PREVIEW["Vercel Preview<br/>(per PR)"]
    end

    subgraph Prod["Production"]
        VPROD["Vercel Prod<br/>(main branch)"]
        SUPAPROD["Supabase Prod<br/>(hosted Postgres)"]
    end

    subgraph Jobs["Background Jobs"]
        MINI["Mac Mini<br/>cron: ETL + alerts"]
        OC3["OpenClaw<br/>Agent skill"]
    end

    LOCAL --> |git push| GH2["GitHub"]
    GH2 --> |PR merge| VPROD
    GH2 --> |PR open| PREVIEW
    VPROD --> SUPAPROD
    MINI --> SUPAPROD
    OC3 --> SUPAPROD

    style Dev fill:#f0f4ff,stroke:#4a6cf7
    style Prod fill:#e6fff0,stroke:#4af794
    style Jobs fill:#fff4e6,stroke:#f7944a
```

### Branch Strategy

```
main              → Production (Vercel prod deployment)
staging           → Integration testing (Vercel preview)
feat/*            → Feature branches (PR into staging)
data/bu-*         → BU-specific ingestion work
```

---

## Monitoring & Observability

| What | How | Alert Channel |
|------|-----|---------------|
| ETL pipeline failures | Script exit codes + log files | Telegram via OpenClaw |
| Data freshness | `MAX(loaded_at)` per BU check | Telegram alert if >36h stale |
| Query performance | Postgres `pg_stat_statements` | Log slow queries >2s |
| Dashboard uptime | Vercel analytics | Email |
| Agent query accuracy | Log all generated SQL + results | Weekly review |
| Data quality | dbt tests (not_null, unique, accepted_values) | Telegram alert on failure |

---

## Disaster Recovery

| Scenario | Mitigation |
|----------|------------|
| Supabase outage | Point-in-time recovery (Supabase built-in) |
| Bad data loaded | Reload from staging tables (immutable raw layer) |
| Schema migration breaks | dbt snapshot + migration rollback |
| Dashboard down | Vercel auto-recovery; agent still works via direct DB |
| Mac Mini offline | GitHub Actions as backup scheduler |

---

*Architecture is living documentation. Update this as decisions are made and the system evolves.*
