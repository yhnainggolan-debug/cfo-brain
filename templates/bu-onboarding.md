# BU Onboarding Checklist

## Business Unit: [NAME]
**BU Code:** [CODE]  
**Sector:** mining / finserv / digital / other  
**Onboarding Date:** YYYY-MM-DD  
**Owner:** [BU Controller Name]

---

### Phase 1: Discovery
- [ ] Identify accounting system (ERP / cloud accounting / spreadsheet)
- [ ] Obtain chart of accounts (full list with descriptions)
- [ ] Identify available data granularity (BU-only? Channel? Customer? SKU?)
- [ ] Confirm functional currency
- [ ] Identify data contact person at BU
- [ ] Confirm reporting cadence (monthly close timeline)

### Phase 2: Mapping
- [ ] Create COA mapping (source account → unified account)
- [ ] Map channels (if applicable)
- [ ] Map customers (if applicable)
- [ ] Map products/SKUs (if applicable)
- [ ] Validate mapping with BU controller

### Phase 3: Historical Load
- [ ] Load 12+ months historical P&L data
- [ ] Load current year budget
- [ ] Load prior year actuals (for YoY comparison)
- [ ] Reconcile totals against BU's own P&L reports
- [ ] Sign off on historical accuracy

### Phase 4: Pipeline Setup
- [ ] Configure ingestion pipeline for BU's source system
- [ ] Set up scheduled sync (daily/monthly)
- [ ] Test end-to-end: source → staging → fact table → dashboard
- [ ] Set up data quality alerts for this BU

### Phase 5: Go Live
- [ ] BU appears correctly in Group dashboard
- [ ] Drill-down to available levels works
- [ ] Budget vs actual comparisons correct
- [ ] BU controller trained on template/process
- [ ] First monthly cycle completed successfully

---

### Notes
_Add BU-specific notes, quirks, or issues here._
