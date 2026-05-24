## Stakeholders

Here are the relevant stakeholders grouped by their relationship to the data:

---

### 1. Internal Stakeholders

#### Finance & Accounts Team
The primary users of reconciliation reports and monthly closing data. They care about transaction mismatches, overpayments, and ensuring collected amounts match book entries.

#### Collections Team
Consumes DPD analytics daily. Their decisions on which borrowers to call, escalate, or write off are driven directly by the DPD bucket classification and overdue loan lists.

#### Risk & Credit Team
Monitors portfolio-level delinquency ratios, default rates, and lender exposure. They use the lender risk scorecard to flag concentration risk and recommend credit policy changes.

#### Senior Management / CXOs
Use the Executive Summary page exclusively. They want collection efficiency, default rate, and portfolio trend — nothing granular.

#### MIS / Reporting Analyst
The person who actually builds and maintains this system. Responsible for data accuracy, report freshness, and distributing reports on schedule.

#### Internal Audit Team
Consumes Page 4 (Reconciliation). Needs documented data anomalies, audit trails, and flagged inconsistencies for compliance reviews.

---

### 2. External Stakeholders

#### Lender Partners (HDFC, Bajaj Finserv, etc.)
Each lender wants visibility into their own book — how much they've disbursed, what's been recovered, and the health of loans they co-funded. They receive lender-specific MIS reports.

#### Investors / Board Members
Receive the investor MIS report — a clean, aggregated view of portfolio health, default rates, and collection trends. They don't want raw data; they want confidence in the business.

#### Regulators (RBI, in the Indian context)
NBFCs are required to report NPA classifications and portfolio quality to the Reserve Bank of India. The DPD bucket logic (SMA-0 → NPA) directly maps to RBI's asset classification norms.
