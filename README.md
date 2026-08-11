# SMART Tax Filing Service

> **S**tructured & Secure | **M**eticulous | **A**utomated & Affordable | **R**esponsive & Reliable | **T**ransparent, Timely & Trustworthy

An automated, Google Workspace-based income-tax preparation and compliance platform designed to organize taxpayer inputs, process multi-source financial documents, reconcile tax records, compute optimized liabilities, prepare Books of Accounts for business income, and support end-to-end Income Tax Return (ITR) filing workflows.

---

## 📌 Overview

The **SMART Tax Filing Service** is engineered for individual taxpayers, consultants, and traders with multi-faceted income portfolios where tax compliance requires aggregating data across diverse financial documents.

### Key Value Propositions
* **Swift Processing**: Data is processed within 5 minutes of input submission.
* **Zero Call Hassles**: Communication is handled entirely via structured emails—no tedious back-and-forth phone calls.
* **Educative Experience**: Integrated Google Form sections guide and educate users on tax provisions.
* **Strict Compliance Focus**: Priority is placed on accuracy, transparency, and data organization—never manipulating numbers to chase illegitimate refunds.
* **Post-Filing Support**: Includes a complimentary 1-on-1 session post-submission to resolve queries and offer tax-planning tips.

---

## 💼 Supported Business Models

The system flexible architecture supports three distinct operational models:
| Model | Operational Workflow |
| :--- | :--- |
| **Model 1: Direct End-to-End Filing** | Client → Submits Data & Fee → Provider Processes → E-Files Return → Delivers Docs |
| **Model 2: Computation Service Only** | Client → Submits Data & Fee → Provider Processes → Sends Computation Sheet → Client Files Independently |
| **Model 3: CA / CS Partnership (B2B)** | Taxpayers → Submit Data to CA/CS → Provider Processes & Returns Computation → CA/CS Validates & Files on Portal |

---

## ⚙️ Platform Capabilities & Scope

### 1. Supported ITR Scope
Supports **ITR-1 (Sahaj)**, **ITR-2**, and **ITR-3** based on the taxpayer's unique income structure:
* **Salaried Persons**: Multiple employer Form 16s.
* **Capital Gains**: Equity, Mutual Funds, Bonds, Immovable/Movable Property, Foreign Assets/RSUs/ESOPs.
* **Business & Profession (PGBP)**: Speculative (Intraday), Non-Speculative (F&O: Equity, Currency, Commodity), and Books of Accounts cases.

### 2. Multi-Source Income Ingestion
* **Employment**: Handles unlimited Form 16s (up to 10 per submission form upload limit).
* **Income from Other Sources**: Interest (Savings, FD, Bonds, IT Refund), Dividends, Buybacks, Rent, and Debt Mutual Funds.
* **Exempt Income (Schedule EI)**: Agricultural income, Tax-free bond interest, Exempt gifts, LIC 10(10D) maturity, PF withdrawals, Gratuity, Leave encashment.
* **Foreign Investments**: RSUs, ESOPs, and ESPPs (e.g., via E*TRADE).

### 3. Automated Reconciliation & Discrepancy Management
* Cross-checks client inputs against **AIS**, **TIS**, **Form 26AS**, **Bank Statements**, and **Broker P&L Reports**.
* **Auto-Correction Engine**: If a mismatch occurs, the platform automatically selects the higher value across sources to guarantee safe, notice-proof filing.
* **Statement Parsing**: Automated bank interest extraction for **SBI, HDFC, ICICI, and Axis Bank**.
* **Broker P&L Parsing**: Automated Tax P&L extraction for **Zerodha**, **Upstox**, and **E*TRADE**.

### 4. Books of Accounts & Asset Depreciation (ITR-3)
For business filers maintaining Books of Accounts, the engine automatically compiles:
* **Financial Statements**: Trading Account, Profit & Loss (P&L) Statement, Capital Account, and Balance Sheet.
* **Asset Depreciation Engine**: Uses Written Down Value (WDV) block-wise tracking (10%, 15%, 40% asset blocks) and applies rules based on purchase timing (Before/After Sept 30).
* **Loss Tracking**: Tracks and carries forward business losses and unabsorbed depreciation.

### 5. Tax Computation & Dual Regime Engine
* Simultaneously calculates tax liabilities under the **Old** and **New Tax Regimes**.
* Factors in Advance Tax paid, Section 115JC (AMT) applicability, and period-wise split requirements for capital gains and dividends.
* Auto-recommends the most beneficial regime and identifies unutilized deduction limits (e.g., 80C, 80CCD(1B), 80D, 80G, 80TTA/TTB).

---

## 🔄 End-to-End Processing Workflow

```text
[ Client Request ]
       │
       ▼
[ Step 1: Input Collection ]
  └─► Client fills Google Form & uploads documents
       │
       ▼
[ Step 2: Processing & Validation ]
  ├─► Mismatch/Errors ──► Send Auto-Correction Email
  └─► Validated ────────► Proceed to Step 3
       │
       ▼
[ Step 3: Reconciliation Engine ]
  └─► Cross-checks AIS / TIS / 26AS / Bank / Broker P&L
       │
       ▼
[ Step 4: Computation & Accounting ]
  └─► Computes Tax & prepares Books of Accounts (ITR-3)
       │
       ▼
[ Step 5: Fee Calculation & Quote ]
  └─► Itemized fee calculated & sent via email
       │
       ▼
[ Step 6: Client Confirmation ]
  └─► Mutual Agreement signed & Regime choice confirmed
       │
       ▼
[ Step 7: ITR Portal Filing ]
  └─► Data uploaded & submitted on Income Tax Portal
       │
       ▼
[ Step 8: Deliverable Package ]
  └─► Zip archive with tax documents, logs & proofs sent to email
```

---

## 📦 Customer Deliverable Package

Upon completion, clients receive an organized folder archive containing:

```text
📂 YYYY-YYYY_[PAN]_[ClientName]/
├── 📂 Bank_Statements/                 # Normalized bank interest certificates & statements
├── 📂 Expenses_Business/               # Business expense bills & invoices
├── 📂 Income_ExemptedSources/          # Agricultural & exempt income proofs
├── 📂 PnL_Docs/                        # Broker P&L reports & trading summaries
├── 📂 TAXeFile_Docs/                   # Filed ITR Form & Portal Acknowledgement
├── 📄 [PAN]_[AY]_[Name]_Agreement.pdf   # Mutual service & liability agreement
├── 📄 [PAN]_[AY]_[Name]_Claims_History  # Multi-year track of LTA, Gratuity, Exemptions
├── 📄 [PAN]_[AY]_[Name]_Fee             # Itemized service bill receipt
├── 📄 [PAN]_[AY]_[Name]_Processing_Logs # Automated warning, info, & decision logs
└── 📄 [PAN]_[AY]_[Name]_TaxComputation # Full Computation Sheet (PDF; XLS on request)
```

---

## 💰 Service Fee Structure

Processing fees are calculated transparently based on **income complexity and data processing requirements**, not income magnitude.

| Category | Component / Tier | Base Fee |
| :--- | :--- | :--- |
| **Employment** | Single Form 16 | ₹500 |
| | Additional Form 16s (per extra employer) | ₹300 / each |
| **Other Sources** | Basic Sources (<4 sources) | ₹150 |
| | Extended Sources (>4 sources) | ₹100 / extra source |
| | Exempted Income (Schedule EI) | ₹100 / source |
| **Capital Gains** | LTCG / STCG Equity (per P&L report) | ₹100 / report |
| | Mutual Funds (per account) | ₹200 / account |
| | Foreign RSUs / ESOPs (LTCG & STCG) | ₹300 / equity |
| **Business (PGBP)** | Speculative Intraday (per P&L report) | ₹100 / report |
| | F&O Equity (per P&L report) | ₹400 / report |
| | F&O Currency / Commodity | ₹200 / report |
| | Asset Depreciation Schedule | ₹200 |
| | Books of Accounts Preparation (ITR-3) | ₹500 |
| **eFiling Surcharge** | ITR-1 Filing | ₹300 |
| | ITR-2 Filing | ₹500 |
| | ITR-3 Filing | ₹900 |
| **Urgency Surcharge** | >9 Days before deadline | ₹0 |
| | 5–9 Days before deadline | ₹200 |
| | 3–5 Days before deadline | ₹400 |
| | 1–3 Days before deadline | ₹600 |
| | Last Day filing | ₹900 |

### Discounts & Reward System
* **Loyalty Discount**: ₹100 flat discount for returning clients.
* **Referral Bonus**: ₹50–₹100 discount per referred client who files.
* **New Format Rewards**: Discounts awarded to clients who submit tax P&L or bank statement formats not currently supported natively (e.g., banks other than SBI/HDFC/ICICI/Axis or brokers other than Zerodha/Upstox/E*TRADE).
* **Max Discount Caps**: ₹300 for ITR-1, ₹500 for ITR-2, ₹600 for ITR-3.

---

## 🛠️ Technology Stack & Architecture

Built completely within the **Google Workspace Ecosystem** without requiring dedicated external Web Application hosting, keeping overhead costs zero:

* **Google Forms**: Educative client data & document collection interface.
* **Google Apps Script**: Orchestration, auto-emailing, document parsing, and business logic execution.
* **Google Sheets**: Spreadsheet-based tax computation engine, financial statement generators, and databases.
* **Google Drive**: Organized cloud storage for raw uploads and processed deliverable packages.
* **Google Docs & Gmail API**: Automated agreement generation, notification engine, and email processing.

### Internal Architecture Documentation
* `docs/architecture.md`
* `docs/workflow.md`
* `docs/inspection-process.md`
* `docs/scoring-system.md`
* `docs/automation.md`

---

## 📖 Key Tax Terminology Glossary

| Term | Definition |
| :--- | :--- |
| **26AS** | Consolidated Tax Credit Statement |
| **AIS** | Annual Information Summary |
| **APY** | Atal Pension Yojana |
| **AY / FY** | Assessment Year / Financial Year |
| **EPF / NPS / SSY** | Employee Provident Fund / National Pension System / Sukanya Samriddhi Yojana |
| **LTCG / STCG** | Long-Term Capital Gains / Short-Term Capital Gains |
| **PGBP** | Profits & Gains from Business or Profession |
| **TIS** | Taxpayer Information Summary |
| **WDV** | Written Down Value (for depreciation) |

---

## 🚀 Limited Slots & Contact Details

To maintain precision and quality, processing capacity is limited:
* **AY 2025-26 Capacity**: Limited to **9 ITRs** on a strict first-come, first-served basis.
* **Introductory Offer**:
  * First **3 Clients**: 100% Free Filing
  * Next **3 Clients**: 50% Service Fee Discount

✉️ **Contact / Inquiries**: `srikanthkumar.dara@gmail.com`

---

> **Disclaimer**: *The platform focuses on compliant, accurate tax processing based on taxpayer-provided data. Logic modules are updated periodically in accordance with the Income Tax Act, Rules, and Circulars.*
