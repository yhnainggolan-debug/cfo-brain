# P&L Line Item Template

## Standard Management P&L Structure

This is the canonical line item ordering for the dashboard. All BU data maps to these lines.

### Line Items (display_order)

```
 10  REVENUE
 11    Gross Revenue
 12    Sales Discounts & Returns (contra)
 19  = Net Revenue
 ──────────────────────────────
 20  COST OF GOODS SOLD
 21    Direct Materials / Raw Materials
 22    Direct Labor
 23    Production Overhead
 24    Freight & Logistics
 25    Royalties & Levies
 29  = Total COGS
 ──────────────────────────────
 30  = GROSS PROFIT (19 - 29)
 31    Gross Margin %
 ──────────────────────────────
 40  OPERATING EXPENSES
 41    Sales & Marketing
 42    General & Administrative
 43    Employee Costs (non-production)
 44    Technology & IT
 45    Professional Fees
 46    Travel & Entertainment
 47    Rent & Facilities
 48    Depreciation & Amortization
 49  = Total OPEX
 ──────────────────────────────
 50  = EBITDA (30 + 48 - 49)
 51    EBITDA Margin %
 52  = EBIT / Operating Income (30 - 49)
 ──────────────────────────────
 60  OTHER INCOME / (EXPENSE)
 61    Interest Income
 62    Interest Expense
 63    FX Gains / (Losses)
 64    Gain / (Loss) on Investments
 65    Other Non-Operating
 69  = Total Other Income / (Expense)
 ──────────────────────────────
 70  = PROFIT BEFORE TAX (52 + 69)
 ──────────────────────────────
 80  INCOME TAX
 81    Current Tax
 82    Deferred Tax
 89  = Total Tax
 ──────────────────────────────
 90  = NET INCOME (70 - 89)
 91    Net Margin %
```

### Contribution Margin P&L (for Channel/Customer/SKU levels)

```
 10  Revenue
 20  Variable Costs
 21    Variable COGS
 22    Variable S&M (commissions, incentives)
 23    Variable Logistics
 29  = Total Variable Costs
 ──────────────────────────────
 30  = CONTRIBUTION MARGIN (10 - 29)
 31    Contribution Margin %
 ──────────────────────────────
 40  Allocated Fixed Costs (optional)
 49  = Segment Profit
```

### Sector-Specific KPIs (appended below P&L)

#### Mining
```
 100  Production Volume (tons)
 101  Average Selling Price ($/ton)
 102  Cash Cost per Ton
 103  Strip Ratio
 104  Calorific Value (GAR)
```

#### Financial Services
```
 100  Net Interest Margin (NIM) %
 101  Non-Performing Loan (NPL) Ratio %
 102  BOPO Ratio %
 103  Loan Book Size
 104  Cost of Funds %
```

#### Digital
```
 100  Monthly Recurring Revenue (MRR)
 101  Customer Acquisition Cost (CAC)
 102  Lifetime Value (LTV)
 103  Churn Rate %
 104  Active Users
```
