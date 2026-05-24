# 🏦 Lender Portfolio Risk & DPD Analytics System

> **A production-style fintech analytics project** simulating the reporting and risk monitoring workflows of a multi-lender NBFC (Non-Banking Financial Company) operating across India.

---

## 📌 Table of Contents

1. [Project Summary](#project-summary)
2. [Business Problem](#business-problem)
3. [Dataset Overview](#dataset-overview)
4. [Project Architecture](#project-architecture)
5. [Key Performance Indicators (KPIs)](#key-performance-indicators)
6. [Analysis Questions](#analysis-questions)
7. [Looker Studio Dashboard Layout](#looker-studio-dashboard-layout)
8. [Tools & Technologies](#tools--technologies)
9. [Folder Structure](#folder-structure)
10. [Resume Description](#resume-description)
11. [Interview Talking Points](#interview-talking-points)

---

# Project Summary

This is an end-to-end finance reporting and analytics system built to solve real operational challenges faced by the finance, collections, and investor relations teams at a multi-lender lending company.

The system ingests loan disbursement data, EMI transaction records, and lender-level summaries — then produces DPD (Days Past Due) analytics, portfolio health KPIs, reconciliation checks, and investor-ready reports via a Looker Studio dashboard.

> **Domain:** Fintech / NBFC Operations  
> **Target Roles:** Data Analyst · Finance Analyst · MIS Analyst · Business Intelligence Analyst  
> **Lenders Covered:** HDFC Bank · Bajaj Finserv · Tata Capital · Aditya Birla Finance · L&T Finance

---

# Business Problem

A growing NBFC co-lends loan products (Consumer Loans, Secured Loans, Income-Based Loans, Micro Investment Products) with five banking and NBFC partners. As the portfolio scales, the finance team struggles with:

| Challenge | Impact |
|---|---|
| No unified view of overdue loans per lender | Collections team operates blind |
| Manual DPD tracking in Excel | Error-prone, delayed reporting |
| Reconciliation done ad hoc | Audit risk, investor complaints |
| No monthly trend visibility | Management decisions delayed |
| Default rate calculated inconsistently | Investor reporting inaccuracies |

### Objective

Build a data pipeline + reporting system that automatically tracks loan performance, classifies DPD buckets, measures lender exposure, and produces audit-ready monthly reports.

---

# Dataset Overview

Three interrelated CSV files simulate the core loan management system (LMS) database.

---

## 1. `loans.csv` — 500 Rows

The master loan register. One row per loan disbursed.

| Column | Type | Description |
|---|---|---|
| `loan_id` | String | Unique loan identifier (e.g., LN000001) |
| `customer_id` | String | Borrower identifier (e.g., CUST00001) |
| `lender_name` | String | Lending partner (5 lenders) |
| `loan_type` | String | CLM / SL / IB / MIP |
| `disbursal_date` | Date | Date loan was issued |
| `principal_amount` | Integer | Loan principal in INR |
| `interest_rate` | Float | Annual interest rate (%) |
| `tenure_months` | Integer | Loan tenure in months |
| `emi_amount` | Float | Monthly EMI in INR |
| `loan_status` | String | Active / Closed / Default |

### Loan Types Explained

- **CLM** — Consumer Loan (Merchandise)
- **SL** — Secured Loan
- **IB** — Income-Based Loan
- **MIP** — Micro Investment Product

---

## 2. `transactions.csv` — ~8,000 Rows

EMI payment records. Multiple rows per loan (one per EMI cycle).

| Column | Type | Description |
|---|---|---|
| `txn_id` | String | Unique transaction ID |
| `loan_id` | String | Foreign key to loans table |
| `payment_date` | Date | Actual date payment was received (null if Missed) |
| `due_date` | Date | Scheduled EMI due date |
| `payment_amount` | Float | Amount paid (0 if Missed) |
| `payment_status` | String | Paid / Delayed / Missed |
| `dpd` | Integer | Days Past Due (0 = on-time, 999 = Missed) |

### DPD Logic

- `Paid` → DPD = 0
- `Delayed` → DPD = payment_date − due_date (in days)
- `Missed` → DPD = 999 (flagged as unresolved)

---

## 3. `lender_summary.csv` — 5 Rows

Aggregated lender-level portfolio snapshot.

| Column | Type | Description |
|---|---|---|
| `lender_name` | String | Partner lender |
| `total_disbursed` | Integer | Total principal disbursed (INR) |
| `active_loans` | Integer | Count of currently active loans |
| `closed_loans` | Integer | Count of fully repaid loans |
| `default_loans` | Integer | Count of defaulted loans |
| `overdue_loans` | Integer | Distinct loans with ≥1 delayed/missed payment |
| `total_collection` | Float | Total EMI payments collected (INR) |
| `avg_interest_rate` | Float | Weighted average interest rate |

---

# Project Architecture

```text
Raw CSVs (LMS Export)
        │
        ▼
Data Cleaning & Validation (Python / SQL)
        │
        ├──► DPD Bucket Classification
        ├──► Reconciliation Logic
        ├──► KPI Aggregations
        └──► Risk Segmentation
                │
                ▼
        Looker Studio Dashboard
                │
        ┌───────┴────────┐
        │                │
Management        Investor
Reports           MIS Reports
```

---

# Key Performance Indicators

## Portfolio-Level KPIs

| KPI | Formula |
|---|---|
| **Total Disbursed Amount** | `SUM(principal_amount)` |
| **Total Collections** | `SUM(payment_amount WHERE status != 'Missed')` |
| **Collection Efficiency %** | `(total_collection / total_disbursed) × 100` |
| **Default Rate %** | `(default loan count / total loans) × 100` |
| **Delinquency Ratio %** | `(loans with any DPD > 0 / active loans) × 100` |
| **Average Interest Rate** | `AVG(interest_rate)` |
| **Average Ticket Size** | `AVG(principal_amount)` |

## DPD Bucket KPIs

| Bucket | DPD Range | Risk Category |
|---|---|---|
| Standard | 0 DPD | Performing |
| SMA-0 | 1–30 DPD | Special Mention |
| SMA-1 | 31–60 DPD | Substandard |
| SMA-2 | 61–90 DPD | Doubtful |
| NPA | 90+ DPD | Non-Performing Asset |

## Lender-Level KPIs

| KPI | Description |
|---|---|
| **Lender Exposure** | % share of total disbursed portfolio |
| **Recovery Rate** | Collections / Disbursed per lender |
| **Overdue Ratio** | Overdue loans / Active loans per lender |
| **Monthly Disbursal Trend** | Month-over-month disbursal volumes |

---

# Analysis Questions

These are the questions your analysis must answer.

---

## Section A — Portfolio Health (SQL / Python)

1. What is the total loan amount disbursed across all lenders? Break it down by lender and loan type.
2. How many loans are currently Active, Closed, and in Default? What is the default rate?
3. Which loan type (CLM / SL / IB / MIP) has the highest average principal amount?
4. What is the average tenure and average EMI amount per lender?
5. Which months saw the highest disbursals? Plot the monthly disbursal trend.

---

## Section B — DPD & Delinquency Analysis (Python)

6. Calculate the DPD for every transaction. Classify each into:
   - Standard (0)
   - SMA-0 (1–30)
   - SMA-1 (31–60)
   - SMA-2 (61–90)
   - NPA (90+)

7. What percentage of active loans have at least one missed payment?
8. Which lender has the highest proportion of Delayed + Missed transactions?
9. Identify the top 10 loans with the highest total DPD accumulated across all their EMIs.
10. Is there a correlation between loan tenure and likelihood of delayed payments?

---

## Section C — Collections & Recovery (SQL / Python)

11. What is the collection efficiency per lender (total collected / total disbursed)?
12. What is the month-wise collection trend across all lenders? Are collections improving or declining?
13. For loans with Default status, what percentage of the total principal was recovered?
14. Which loan type has the worst recovery rate?
15. Find all loans where total payments collected exceed the principal amount (fully recovered loans).

---

## Section D — Reconciliation & Data Quality (SQL / Python)

16. Identify loans in the loans table that have zero transactions in the transactions table.
17. Flag transactions where payment_amount is greater than the EMI amount (possible duplicate or overpayment).
18. Find customers with more than one loan (potential over-leveraged borrowers).
19. Check if any loan has a disbursal date after its first transaction date (data integrity issue).
20. Identify all Missed transactions where the DPD value is not set to 999 (data inconsistency).

---

## Section E — Lender Risk Comparison (SQL / Python)

21. Rank all 5 lenders by their overdue ratio (overdue loans / active loans). Who is highest risk?
22. For each lender, what is the split of loans by DPD bucket? Create a heatmap or stacked bar.
23. Which lender has the best and worst collection efficiency?
24. Build a risk scorecard for each lender using:
   - Default rate
   - Overdue ratio
   - Collection efficiency

25. If you had to recommend reducing exposure to one lender based on data, which would it be and why?

---

## Section F — Advanced / Optional

26. Build a month-on-month cohort analysis:
    - For loans disbursed in each month, what % went overdue within 90 days?

27. Forecast next 3 months of expected collections using historical monthly trends.
28. Flag any loan as "High Risk" if it meets 2+ of:
    - Default status
    - DPD > 60 on any EMI
    - Principal > ₹3,00,000

29. Create an investor MIS summary table:
    - Lender-wise disbursed
    - Collected
    - Overdue
    - Default rate

30. Build a mock audit trail:
    - List all data anomalies found across Q16–Q20 in a single summary DataFrame.

---

# Looker Studio Dashboard Layout

Design the report with **4 pages**.

---

## Page 1 — Executive Summary

### Purpose
One-screen snapshot for senior management.

### Scorecards
- Total Disbursed (INR)
- Total Collected (INR)
- Collection Efficiency %
- Default Rate %
- Active Loans Count

### Charts
- Bar chart: Monthly Disbursal Trend
- Donut chart: Loan Status Distribution
- Bar chart: Disbursed Amount by Lender
- Bar chart: Disbursed Amount by Loan Type

### Filters
- Lender Name
- Loan Type
- Date Range

---

## Page 2 — DPD & Delinquency Analytics

### Purpose
Risk team view — who owes, how late, and how bad.

### Scorecards
- Total Overdue Loans
- NPA Loan Count
- Delinquency Ratio %
- % EMIs Paid on Time

### Charts
- Stacked bar chart: DPD Bucket Distribution
- Bar chart: DPD Bucket by Lender
- Line chart: DPD Trend Over Time
- Table: Top 20 Loans by Highest DPD

### Filters
- Lender Name
- Loan Type
- Payment Status
- DPD Bucket

---

## Page 3 — Lender Performance & Risk

### Purpose
Lender relationship management and risk exposure monitoring.

### Scorecards
- Lender Exposure %
- Recovery Rate %
- Overdue Ratio %
- Avg Interest Rate

### Charts
- Horizontal bar chart: Collection Efficiency by Lender
- Stacked bar chart: Loan Status Breakdown by Lender
- Scatter plot: Lender Exposure vs Default Rate
- Table: Lender Risk Scorecard

### Filters
- Lender Name
- Loan Type
- Month

---

## Page 4 — Reconciliation & Data Quality

### Purpose
Finance operations and audit support.

### Scorecards
- Loans with Zero Transactions
- Overpayment Flag Count
- Data Inconsistency Count
- Fully Recovered Loan Count

### Charts
- Table: Loans with no transactions
- Table: Flagged overpayment transactions
- Bar chart: Monthly Missed Payment Count
- Pie chart: Transaction Status Split

### Filters
- Lender Name
- Payment Status
- Flag Type

---

# Tools & Technologies

| Category | Tools |
|---|---|
| **Data Generation & Cleaning** | Python (Pandas, NumPy) |
| **Data Analysis** | Python (Pandas), SQL |
| **Database** | PostgreSQL / MySQL / BigQuery |
| **Visualization** | Looker Studio |
| **Reporting** | Google Sheets, Excel |
| **Version Control** | Git, GitHub |
| **Documentation** | Markdown |

---

# Folder Structure

```text
Analytics/
│
├── data/
│   ├── loans.csv
│   ├── transactions.csv
│   └── lender_summary.csv
│
├── sql/
│   ├── 01_schema.sql
│   ├── 02_portfolio_kpis.sql
│   ├── 03_dpd_analysis.sql
│   ├── 04_collections.sql
│   ├── 05_reconciliation.sql
│   └── 06_lender_risk.sql
│
├── python/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_dpd_buckets.ipynb
│   ├── 03_portfolio_analysis.ipynb
│   ├── 04_reconciliation.ipynb
│   └── 05_advanced_analysis.ipynb
│
├── dashboard/
│   └── looker_studio_link.txt
│
├── reports/
│   ├── lender_risk_scorecard.xlsx
│   └── investor_mis_report.xlsx
│
├── screenshots/
│   ├── executive_summary.png
│   ├── dpd_analytics.png
│   ├── lender_performance.png
│   └── reconciliation.png
│
├── README.md
│
└── presentation/
    └── Analytics_Deck.pdf
```

---

# Resume Description

> Developed an end-to-end fintech reporting system simulating real NBFC operations — covering loan disbursement tracking, DPD bucket classification (Standard → NPA), lender-wise portfolio risk scoring, and collections reconciliation. Built interactive Looker Studio dashboards for executive, risk, and audit audiences. Answered 30 business questions using SQL aggregations and Python (Pandas), including cohort analysis, recovery forecasting, and data integrity checks.

---

# Interview Talking Points

### Q: What business problem did this solve?

The finance team of a multi-lender NBFC had no unified view of overdue loans, collection efficiency, or lender-level risk. Everything was manual and siloed. This project automated that reporting.

---

### Q: What was the most complex part?

The DPD classification and reconciliation logic. DPD is not just a subtraction — it also requires handling:
- Missed payments
- Null payment dates
- Partial payments
- Data quality edge cases

---

### Q: How did you validate data quality?

I built a reconciliation notebook that flagged:
- Loans with no transactions
- Payments exceeding EMI amounts
- Incorrect DPD mappings for Missed transactions
- Customers with multiple active loans

---

### Q: What did the Looker Studio dashboard show?

The dashboard had four stakeholder-focused pages:
1. Executive Summary
2. DPD Analytics
3. Lender Performance & Risk
4. Reconciliation & Audit Flags

---

### Q: What would you add with more time?

- A default prediction model using logistic regression
- Automated monthly MIS emails
- Risk alerts triggered by abnormal DPD spikes
- Scheduled ETL pipelines for production deployment

---

*Built as a portfolio project to demonstrate fintech domain expertise in loan analytics, risk monitoring, and financial reporting.*
