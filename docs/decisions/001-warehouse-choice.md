# ADR-001: Analytics Warehouse Choice

**Status:** Proposed  
**Date:** 2026-04-17  
**Decision Maker:** Yosua

## Context

We need a database to store normalized financial data from multiple business units. The warehouse must support:
- Star schema with fact + dimension tables
- Aggregation queries across millions of rows
- Row-level security for multi-tenant access
- Integration with Next.js dashboard and OpenClaw agent

## Options Considered

### Option A: Supabase Postgres (Recommended)
- **Pros:** Already in use (ShareHive), RLS built-in, Edge Functions, free tier, team knows it
- **Cons:** Not optimized for heavy OLAP workloads, may slow down at >10M rows
- **Cost:** Free tier → $25/mo Pro

### Option B: ClickHouse
- **Pros:** Purpose-built for analytics, blazing fast aggregations, columnar storage
- **Cons:** Separate infra, no built-in auth/RLS, steeper learning curve
- **Cost:** ClickHouse Cloud ~$100/mo minimum

### Option C: BigQuery
- **Pros:** Serverless, scales infinitely, good BI tool ecosystem
- **Cons:** Google lock-in, pay-per-query can surprise, less suitable for real-time dashboard
- **Cost:** Variable, ~$50-200/mo depending on query volume

### Option D: DuckDB (local)
- **Pros:** Zero infra, runs on Mac Mini, great for prototyping
- **Cons:** Single-user, no network access, no RLS
- **Cost:** Free

## Decision

**Start with Supabase Postgres.** Migrate to ClickHouse or BigQuery only when:
- Query latency exceeds 2 seconds on standard aggregations
- Data volume exceeds 10M rows in fact table
- Concurrent user count exceeds 10

## Consequences

- Can reuse Supabase expertise from ShareHive
- RLS gives us BU-level security from day one
- May need to add materialized views or summary tables for performance
- Migration path is clean: star schema SQL translates directly to ClickHouse/BigQuery
