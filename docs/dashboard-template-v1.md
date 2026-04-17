# 📊 Dashboard Template v1 — Board Monthly P&L

> Based on: Star Capital "FS Pillar & Others Pillar — Mar 2026" format

This is the target dashboard layout for monthly board finance updates. One page per vertical/pillar, showing all BUs within that vertical.

---

## Layout Structure

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│  [Pillar Name] — [Month Year]                    P&L Achievement    [LOGO]      │
│                                                  ■ >110% target                 │
│                                                  ■ 100-110% target              │
│  ● ● ●                                          ■ 90-100% target               │
│                                                  ■ <90% target                  │
├──────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│       YoY Growth    YTD Achv.         YoY Growth    YTD Achv.      YoY Growth   │
│         11%           82%               -23%          130%           -204%       │
│                                                                                  │
├───────────────────────┬───────────────────────┬──────────────────────────────────┤
│       REVENUE          │       EBITDA           │          PBT                    │
├───────────────────────┼───────────────────────┼──────────────────────────────────┤
│ MTD  LY   YTD  YTD  FY  %   │ MTD  LY   YTD  YTD  FY  %   │ MTD  LY   YTD  YTD  FY  %   │
│ Act  YTD  Act  Bdg  Bdg Ach │ Act  YTD  Act  Bdg  Bdg Ach │ Act  YTD  Act  Bdg  Bdg Ach │
├───────────────────────┼───────────────────────┼──────────────────────────────────┤
│ BU A   ...             │ BU A   ...             │ BU A   ...                      │
│ BU B   ...             │ BU B   ...             │ BU B   ...                      │
│ BU C   ...             │ BU C   ...             │ BU C   ...                      │
│ ─────────────────────  │ ─────────────────────  │ ────────────────────────────── │
│ Group Total   ...      │ Group Total   ...      │ Group Total   ...              │
├───────────────────────┼───────────────────────┼──────────────────────────────────┤
│ PILLAR TOTAL  ...      │ PILLAR TOTAL  ...      │ PILLAR TOTAL  ...              │
└───────────────────────┴───────────────────────┴──────────────────────────────────┘
```

---

## Exact Column Specification

### Three Metric Panels (side by side)

| Panel | Metric | Definition |
|-------|--------|------------|
| **Left** | Revenue | Net Revenue (line_item_type = REVENUE) |
| **Center** | EBITDA | Revenue + COGS + OPEX + D&A addback |
| **Right** | PBT | Profit Before Tax (all line items except TAX) |

### Six Columns Per Panel

| Column | Header | Source | Format |
|--------|--------|--------|--------|
| 1 | **MTD Actual** | Current month actuals | Number, parentheses for negative |
| 2 | **LY YTD** | Prior year, same YTD period | Number |
| 3 | **YTD Actual** | Year-to-date actuals | Number |
| 4 | **YTD Budget** | Year-to-date budget | Number |
| 5 | **FY Budget** | Full year budget | Number |
| 6 | **% Achvmt** | YTD Actual ÷ YTD Budget | Percentage, color-coded |

### Summary KPIs (above columns, per panel)

| KPI | Formula | Position |
|-----|---------|----------|
| **YoY Growth** | (YTD Actual − LY YTD) ÷ LY YTD × 100 | Above panel, left |
| **YTD Achv.** | YTD Actual ÷ YTD Budget × 100 | Above panel, right |

---

## Row Structure

```
Rows per pillar page:

  [Sub-Group 1 Header]          ← e.g., "ALDIRA" (optional grouping within pillar)
    BU Row: ALDIRA
    BU Row: ALDIRA Holding
    BU Row: STARAM
    BU Row: STARLIFE
    BU Row: KRI
    BU Row: SIGU
    BU Row: STARWEALTH
    BU Row: STARHOUSE CAPITAL
    BU Row: Nanjing
  [Sub-Group 1 Total]           ← Bold, bordered "Total" row

  [Sub-Group 2 Header]          ← e.g., "PRODIGI / KANCIL"
    BU Row: PRODIGI
    BU Row: KANCIL
  [Sub-Group 2 Total]           ← Bold "Total" row

  [PILLAR TOTAL]                ← Bold, distinct styling — "ALDIRA TOTAL"
```

### Row Hierarchy Configuration

```yaml
# templates/pillar-config.yaml (example)

pillar: "FS Pillar & Others Pillar"
period: "2026-03"
currency: "USD'000"
currency_divisor: 1000  # raw data in USD, display in thousands

groups:
  - name: "ALDIRA"
    bus:
      - bu_code: "ALDIRA"
      - bu_code: "ALDIRA_HOLDING"
      - bu_code: "STARAM"
      - bu_code: "STARLIFE"
      - bu_code: "KRI"
      - bu_code: "SIGU"
      - bu_code: "STARWEALTH"
      - bu_code: "STARHOUSE"
      - bu_code: "NANJING"
    show_total: true

  - name: "Others"
    bus:
      - bu_code: "PRODIGI"
      - bu_code: "KANCIL"
    show_total: true

pillar_total:
  label: "ALDIRA TOTAL"
  include_all_groups: true
```

---

## Color Coding (Conditional Formatting)

### Achievement % Colors

```
┌─────────────────────────────────────────────────┐
│  % Achievement         │  Color     │  Hex      │
├────────────────────────┼────────────┼───────────┤
│  > 110%                │  Dark Green│  #2E7D32  │
│  100% — 110%           │  Light Green│ #A5D6A7  │
│  90% — 100%            │  Light Red │  #EF9A9A  │
│  < 90%                 │  Dark Red  │  #C62828  │
└─────────────────────────────────────────────────┘
```

### Applied To

| Element | Color Rule |
|---------|-----------|
| **% Achvmt column** (all 3 panels) | 4-tier as above |
| **MTD Actual** cells | Red text if negative (parentheses) |
| **YoY Growth** summary KPI | Green if positive, red if negative |
| **YTD Achv.** summary KPI | Same 4-tier scale |
| **Row background** | Alternating white/light gray for readability |
| **Total rows** | Bold text, top border, light gray background |
| **Pillar total row** | Bold, dark background, white text |

---

## Number Formatting

| Rule | Example | When |
|------|---------|------|
| Thousands, no decimals | `2,728` | Standard amounts |
| Parentheses for negatives | `(146)` | Negative MTD/YTD values |
| Percentage, no decimals | `17%` | Achievement columns |
| N/A | `N/A` | Division by zero or missing data |
| Zero shown as `0` | `0` | Actual zero (not blank) |
| Currency label | `USD'000` | Top-left corner of each group |

---

## Data Queries

### Revenue Panel — Per BU Row

```sql
SELECT
    bu.bu_name,
    -- MTD Actual
    SUM(CASE WHEN d.year = :year AND d.month = :month
        THEN f.amount_usd ELSE 0 END) / 1000 AS mtd_actual,
    -- LY YTD
    SUM(CASE WHEN d.year = :year - 1 AND d.month <= :month
        THEN f.amount_usd ELSE 0 END) / 1000 AS ly_ytd,
    -- YTD Actual
    SUM(CASE WHEN d.year = :year AND d.month <= :month
        THEN f.amount_usd ELSE 0 END) / 1000 AS ytd_actual,
    -- YTD Budget
    SUM(CASE WHEN d.year = :year AND d.month <= :month
        THEN f.budget_amount ELSE 0 END) / 1000 AS ytd_budget,
    -- FY Budget
    SUM(CASE WHEN d.year = :year
        THEN f.budget_amount ELSE 0 END) / 1000 AS fy_budget,
    -- % Achievement
    CASE WHEN SUM(CASE WHEN d.year = :year AND d.month <= :month
        THEN f.budget_amount ELSE 0 END) != 0
    THEN ROUND(
        SUM(CASE WHEN d.year = :year AND d.month <= :month THEN f.amount_usd ELSE 0 END) /
        SUM(CASE WHEN d.year = :year AND d.month <= :month THEN f.budget_amount ELSE 0 END) * 100
    ) ELSE NULL END AS pct_achvmt
FROM fact_pnl_line_item f
JOIN dim_business_unit bu ON f.bu_id = bu.bu_id
JOIN dim_date d ON f.date_id = d.date_id
JOIN dim_account a ON f.account_id = a.account_id
WHERE a.line_item_type = 'REVENUE'
  AND bu.bu_id = ANY(:bu_ids)
GROUP BY bu.bu_name
ORDER BY bu.bu_name;
```

### EBITDA Panel

Same structure, but filter:
```sql
WHERE a.line_item_type IN ('REVENUE', 'COGS', 'OPEX')
-- Note: D&A is in OPEX; EBITDA = Revenue + COGS + OPEX + D&A addback
-- If D&A tracked separately: subtract account where category = 'Depreciation & Amortization'
```

### PBT Panel

```sql
WHERE a.line_item_type IN ('REVENUE', 'COGS', 'OPEX', 'OTHER_INCOME', 'OTHER_EXPENSE')
-- Excludes TAX
```

---

## Summary KPI Calculations

```sql
-- YoY Growth (for Revenue, at pillar level)
WITH current AS (
    SELECT SUM(f.amount_usd) AS ytd
    FROM fact_pnl_line_item f
    JOIN dim_date d ON f.date_id = d.date_id
    JOIN dim_account a ON f.account_id = a.account_id
    WHERE a.line_item_type = 'REVENUE'
      AND d.year = :year AND d.month <= :month
      AND f.bu_id = ANY(:pillar_bu_ids)
),
prior AS (
    SELECT SUM(f.amount_usd) AS ytd
    FROM fact_pnl_line_item f
    JOIN dim_date d ON f.date_id = d.date_id
    JOIN dim_account a ON f.account_id = a.account_id
    WHERE a.line_item_type = 'REVENUE'
      AND d.year = :year - 1 AND d.month <= :month
      AND f.bu_id = ANY(:pillar_bu_ids)
)
SELECT
    ROUND((current.ytd - prior.ytd) / NULLIF(prior.ytd, 0) * 100) AS yoy_growth,
    -- YTD Achievement needs budget data similarly
FROM current, prior;
```

---

## Template Configuration Schema

The dashboard is **config-driven**. Each board page is defined by a YAML/JSON config:

```yaml
# Dashboard page configuration

page:
  title: "FS Pillar & Others Pillar"
  period: "2026-03"              # Drives all date filters
  currency: "USD"
  display_unit: "thousands"      # thousands | millions | units
  display_label: "USD'000"

  logo: "/assets/star-capital-logo.png"

  # The three metric panels
  panels:
    - metric: "revenue"
      label: "Revenue"
      line_item_types: ["REVENUE"]
      sign: 1                    # positive = good

    - metric: "ebitda"
      label: "EBITDA"
      line_item_types: ["REVENUE", "COGS", "OPEX"]
      da_addback: true           # add back D&A for EBITDA calc
      sign: 1

    - metric: "pbt"
      label: "PBT"
      line_item_types: ["REVENUE", "COGS", "OPEX", "OTHER_INCOME", "OTHER_EXPENSE"]
      sign: 1

  # Columns per panel
  columns:
    - key: "mtd_actual"
      label: "MTD Actual"
    - key: "ly_ytd"
      label: "LY YTD"
    - key: "ytd_actual"
      label: "YTD Actual"
    - key: "ytd_budget"
      label: "YTD Budget"
    - key: "fy_budget"
      label: "FY Budget"
    - key: "pct_achvmt"
      label: "% Achvmt"
      format: "percentage"
      conditional_format:
        - range: [110, null]
          color: "#2E7D32"
          bg: "#E8F5E9"
        - range: [100, 110]
          color: "#2E7D32"
          bg: "#C8E6C9"
        - range: [90, 100]
          color: "#C62828"
          bg: "#FFCDD2"
        - range: [null, 90]
          color: "#C62828"
          bg: "#EF9A9A"

  # Summary KPIs above each panel
  summary_kpis:
    - key: "yoy_growth"
      label: "YoY Growth"
      formula: "(ytd_actual - ly_ytd) / abs(ly_ytd) * 100"
    - key: "ytd_achvmt"
      label: "YTD Achv."
      formula: "ytd_actual / ytd_budget * 100"

  # Row groupings
  groups:
    - name: "ALDIRA"
      bus: ["ALDIRA", "ALDIRA_HOLDING", "STARAM", "STARLIFE", "KRI", "SIGU", "STARWEALTH", "STARHOUSE", "NANJING"]
      show_total: true

    - name: "Others"
      bus: ["PRODIGI", "KANCIL"]
      show_total: true

  pillar_total:
    label: "ALDIRA TOTAL"
    style: "bold_inverted"       # dark bg, white text

  # Number formatting
  formatting:
    negatives: "parentheses"     # (123) not -123
    decimals: 0
    thousands_separator: ","
    null_display: "N/A"
    zero_display: "0"

  # Color rules
  colors:
    negative_text: "#C62828"
    positive_growth: "#2E7D32"
    negative_growth: "#C62828"
    total_row_bg: "#F5F5F5"
    pillar_total_bg: "#1A1A2E"
    pillar_total_text: "#FFFFFF"
    alternating_row: "#FAFAFA"
```

### Why Config-Driven?

1. **New pillars/verticals** = new config file, no code change
2. **Column changes** (add forecast, remove LY YTD) = config edit
3. **Metric changes** (swap PBT for Net Income) = config edit
4. **Theming** (different logo, colors per vertical) = config edit
5. **The dashboard renderer reads config + queries data** — it doesn't know about specific BUs or metrics

---

## Component Wireframe

```
┌─────────────────────────────────────────────────────────────────────────┐
│ <PageHeader>                                                            │
│   title="FS Pillar & Others Pillar — Mar 2026"                         │
│   logo="/assets/star-capital.png"                                       │
│   legend={achievementColors}                                            │
│                                                                         │
│ <SummaryKPIBar>                                                         │
│   panels=[                                                              │
│     { label: "Revenue", yoyGrowth: 11, ytdAchvmt: 82 },               │
│     { label: "EBITDA", yoyGrowth: -23, ytdAchvmt: 130 },              │
│     { label: "PBT", yoyGrowth: -204, ytdAchvmt: -70 }                 │
│   ]                                                                     │
│                                                                         │
│ <PnLGrid>                                                               │
│   panels={3}                                                            │
│   columns={columns}                                                     │
│   groups={[                                                             │
│     <BUGroup name="ALDIRA">                                            │
│       <BURow bu="ALDIRA" data={...} />                                 │
│       <BURow bu="STARAM" data={...} />                                 │
│       ...                                                               │
│       <TotalRow label="Total" data={groupTotal} />                     │
│     </BUGroup>                                                          │
│     <BUGroup name="Others">                                            │
│       <BURow bu="PRODIGI" data={...} />                                │
│       <BURow bu="KANCIL" data={...} />                                 │
│       <TotalRow label="Total" data={groupTotal} />                     │
│     </BUGroup>                                                          │
│     <PillarTotalRow label="ALDIRA TOTAL" data={pillarTotal} />         │
│   ]}                                                                    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## React Component Tree

```
BoardPnLPage
├── PageHeader (title, logo, legend)
├── SummaryKPIBar
│   ├── KPICard (Revenue: YoY, YTD Achv)
│   ├── KPICard (EBITDA: YoY, YTD Achv)
│   └── KPICard (PBT: YoY, YTD Achv)
├── PnLGrid
│   ├── PanelHeader ("Revenue" | "EBITDA" | "PBT")
│   ├── ColumnHeaders (MTD Act, LY YTD, YTD Act, YTD Bdg, FY Bdg, % Ach)
│   ├── BUGroup[]
│   │   ├── BURow[] (one per BU, with conditional formatting)
│   │   └── GroupTotalRow
│   └── PillarTotalRow
├── PeriodSelector (month picker → re-renders everything)
└── ExportBar (Excel, PDF, Share link)
```

---

*This template is the v1 target. All rendering is driven by the YAML config + data queries. Change the config, get a different board page.*
