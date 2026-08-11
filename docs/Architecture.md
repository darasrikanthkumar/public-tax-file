# SMART Tax Filing Platform — Architecture

## 1. Architecture Overview

The SMART Tax Filing Platform follows a **modular, workflow-driven, document-centric architecture**.

The current implementation uses Google Workspace as the infrastructure platform, with Google Apps Script acting as the application and orchestration layer.

```text
                         ┌──────────────────────┐
                         │      TAXPAYER        │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │    Google Forms      │
                         │  Input / Documents   │
                         └──────────┬───────────┘
                                    │
                                    ▼
                 ┌─────────────────────────────────────┐
                 │       WORKFLOW ORCHESTRATOR         │
                 │       Google Apps Script            │
                 └────────────────┬────────────────────┘
                                  │
             ┌────────────────────┼────────────────────┐
             │                    │                    │
             ▼                    ▼                    ▼
      Document Intake       Data Processing       Workflow State
             │                    │                    │
             ▼                    ▼                    ▼
        Google Drive       Processing Engines      Google Sheets
                                  │
             ┌────────────────────┼─────────────────────┐
             │                    │                     │
             ▼                    ▼                     ▼
       Extraction Engine   Reconciliation Engine   Validation Engine
             │                    │                     │
             └────────────────────┼─────────────────────┘
                                  ▼
                       ┌────────────────────────┐
                       │  Financial Computation │
                       └───────────┬────────────┘
                                   │
        ┌──────────────┬───────────┼──────────────┬──────────────┐
        ▼              ▼           ▼              ▼              ▼
      Salary       Capital       PGBP           Other          Exempt
                   Gains         Business       Sources        Income
        │              │           │              │              │
        └──────────────┴───────────┼──────────────┴──────────────┘
                                   ▼
                       ┌────────────────────────┐
                       │   Books of Accounts    │
                       ├────────────────────────┤
                       │ Trading Account        │
                       │ Profit & Loss          │
                       │ Capital Account        │
                       │ Balance Sheet          │
                       │ Depreciation           │
                       │ Carry Forward Losses   │
                       └───────────┬────────────┘
                                   │
                                   ▼
                       ┌────────────────────────┐
                       │  Tax Computation Engine │
                       ├────────────────────────┤
                       │ Old Regime             │
                       │ New Regime             │
                       │ Set-off / Carryforward │
                       │ Tax / Rebate / Cess    │
                       │ Interest / Payments    │
                       └───────────┬────────────┘
                                   │
                                   ▼
                       ┌────────────────────────┐
                       │ Review / Validation     │
                       │ Warnings / Suggestions  │
                       └───────────┬────────────┘
                                   │
                                   ▼
                       ┌────────────────────────┐
                       │ Filing Support Layer    │
                       └───────────┬────────────┘
                                   │
                         ┌─────────┴─────────┐
                         ▼                   ▼
                   Taxpayer / CA/CS      Tax Portal
                         │
                         ▼
                  Final Documentation
```

The platform's documented workflow begins with client input collection, proceeds through processing/corrections and payment, and ends with filing and document delivery.

---

# 2. Architectural Layers

## Layer 1 — Presentation / Input

### Google Forms

Responsibilities:

* Client onboarding
* Taxpayer profile
* Income-source declaration
* Financial-year information
* Document upload
* Agreement acceptance
* Corrections
* Additional information requests

The form acts as the current lightweight user interface.

---

## Layer 2 — Workflow Orchestration

### Google Apps Script

This is the central application layer.

Responsibilities:

* Trigger processing
* Identify taxpayer/workflow
* Create/update processing state
* Invoke parsers
* Invoke computation engines
* Update computation sheets
* Generate documents
* Send emails
* Create folders
* Handle retries
* Maintain processing logs

The architecture should keep orchestration separate from financial calculation logic.

```text
Trigger
  ↓
Controller
  ↓
Workflow Service
  ↓
Processing Modules
```

---

# 3. Data Storage Layer

## Google Sheets

Google Sheets currently serves as the structured application datastore and computation workspace.

Suggested logical areas:

```text
Configuration
Customer Master
FY Master
Document Registry
Processing Status
Source Data
Normalised Data
Reconciliation
Computation
Books of Accounts
Tax Calculation
Audit / Logs
```

The system should avoid treating every sheet as an independent source of truth.

Recommended hierarchy:

```text
Source Documents
       ↓
Normalised Data
       ↓
Computation Model
       ↓
Presentation Sheets
```

---

# 4. Document Storage Layer

## Google Drive

Drive acts as the document repository.

```text
Root
│
├── Customers
│   └── Customer-ID
│       └── FY-YYYY-YY
│
├── Templates
│
├── Configuration
│
└── Archive
```

Each customer/FY should have isolated document storage.

---

# 5. Document Ingestion Architecture

```text
                   Uploaded Documents
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
       PDF              Excel            Other
        │                 │                 │
        ▼                 ▼                 ▼
   PDF Parser         Spreadsheet       File Parser
        │              Parser               │
        └─────────────────┼─────────────────┘
                          ▼
                    Normalisation
                          │
                          ▼
                  Canonical Data Model
```

Each source parser should implement a common contract.

Example:

```javascript
parse(document)
    ↓
{
    sourceType,
    financialYear,
    assessmentYear,
    taxpayer,
    records,
    metadata,
    warnings
}
```

This allows new bank/broker/document formats to be added without changing the computation engine.

---

# 6. Canonical Financial Data Model

The platform should convert all external sources into common internal representations.

Example:

```text
Form16
 └── SalaryRecord[]

Bank
 └── InterestRecord[]

Broker
 ├── EquityTransaction[]
 ├── CapitalGainRecord[]
 ├── IntradayRecord[]
 └── FNORecord[]

AIS/TIS
 └── ReportedIncomeRecord[]

Assets
 └── AssetRecord[]
```

The computation engine should operate on these canonical models rather than directly reading vendor-specific files.

---

# 7. Reconciliation Architecture

```text
             ┌──────────────┐
             │ Client Input  │
             └──────┬───────┘
                    │
                    ▼
            ┌───────────────┐
            │ Normalisation │
            └───────┬───────┘
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
      AIS          TIS          Bank
       │            │            │
       └────────────┼────────────┘
                    ▼
             Reconciliation
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        Match    Mismatch   Missing
                    │
                    ▼
                 Warning
                    │
                    ▼
               User Review
```

### Reconciliation Rule

A reconciliation decision should contain:

```text
Source A value
Source B value
Difference
Selected value
Selection reason
Rule version
Review status
```

Never discard the original values.

---

# 8. Income Processing Modules

The computation engine should be modular.

```text
Income Engine
│
├── Salary
├── House Property
├── Capital Gains
│   ├── STCG
│   └── LTCG
├── Other Sources
│   ├── Interest
│   ├── Dividend
│   └── Buyback
├── PGBP
│   ├── Normal Business
│   ├── Speculative
│   └── F&O
└── Exempt Income
```

Each module should expose a consistent interface.

Example:

```javascript
calculate(context)
    ↓
{
    income,
    deductions,
    losses,
    taxTreatment,
    sourceReferences,
    warnings
}
```

---

# 9. PGBP / Books of Accounts Architecture

For ITR-3 cases:

```text
PGBP
 │
 ├── Trading Account
 │      ├── Opening Stock
 │      ├── Purchases
 │      ├── Sales
 │      ├── Closing Stock
 │      └── Direct Expenses
 │
 ├── Profit & Loss
 │      ├── Trading Result
 │      ├── Speculative Result
 │      ├── F&O Result
 │      ├── Other Business Income
 │      └── Indirect Expenses
 │
 ├── Capital Account
 │      ├── Opening Capital
 │      ├── Capital Introduced
 │      ├── Drawings
 │      └── Net Profit/Loss
 │
 ├── Balance Sheet
 │
 ├── Asset Depreciation
 │
 └── Carry Forward Losses
```

The presentation specifically identifies Trading Statement, P&L, Capital Account, Balance Sheet, and Carry Forward Losses as supporting outputs for ITR-3/business cases.

---

# 10. Asset & Depreciation Engine

```text
Asset Register
     │
     ▼
Opening WDV
     │
     ├── Additions
     │
     ├── Disposals
     │
     ├── Adjustments
     │
     └── Business-use validation
     │
     ▼
Applicable Block
     │
     ▼
Depreciation Rule
     │
     ▼
Current-Year Depreciation
     │
     ▼
Closing WDV
```

The presentation describes block-wise WDV tracking, additions, sale/disposal handling and depreciation-rate application.

---

# 11. Tax Rules Architecture

Tax rules should be externalised from processing code.

```text
Rule Registry
│
├── FY / AY
├── Tax Regime
├── Income Type
├── Rate
├── Threshold
├── Deduction
├── Exemption
├── Loss Set-off
├── Carry Forward
├── Filing Condition
└── Validation Rule
```

Every rule should contain:

```text
ruleId
fy
ay
effectiveFrom
effectiveTo
description
value
sourceReference
version
status
```

This is essential because tax provisions change over time.

---

# 12. Tax Computation Flow

```text
Normalised Income
       │
       ▼
Income Classification
       │
       ▼
Gross Income
       │
       ▼
Eligible Adjustments
       │
       ▼
Set-off / Loss Processing
       │
       ▼
Total Income
       │
       ├───────────────┐
       ▼               ▼
 Old Regime       New Regime
       │               │
       ▼               ▼
 Tax Calculation   Tax Calculation
       │               │
       └───────┬───────┘
               ▼
        Regime Comparison
               │
               ▼
        Final Tax Position
```

---

# 13. Books-to-Tax Integration

Books of Accounts should not be independently maintained from tax computation.

The relationship should be:

```text
Source Transactions
        │
        ▼
Financial Computation
        │
        ├── Trading Account
        ├── P&L
        ├── Capital Account
        └── Balance Sheet
                │
                ▼
          Tax PGBP Values
                │
                ▼
          Tax Computation
                │
                ▼
             ITR Data
```

This prevents inconsistent values between Books of Accounts and the tax return.

---

# 14. Previous-Year Continuity

The platform should treat previous-year data as an input dataset.

```text
Previous FY
    │
    ├── Closing Capital
    ├── Closing Stock
    ├── Closing WDV
    ├── Carry-forward Losses
    └── Historical Information
             │
             ▼
Current FY Opening Data
```

At year end:

```text
Current FY Closing Data
          │
          ▼
Future FY Opening Data
```

This creates an annual accounting chain.

---

# 15. Customer Workflow State Machine

```text
NEW
 │
 ▼
INPUT_REQUESTED
 │
 ▼
INPUT_RECEIVED
 │
 ▼
VALIDATING
 ├───────────────┐
 │               │
 ▼               ▼
VALID           CORRECTION_REQUIRED
 │                     │
 ▼                     └──────► INPUT_RECEIVED
PROCESSING
 │
 ▼
RECONCILIATION
 │
 ▼
COMPUTATION_READY
 │
 ▼
REVIEW
 │
 ▼
CUSTOMER_CONFIRMATION
 │
 ▼
PAYMENT_CONFIRMED
 │
 ▼
FILING_READY
 │
 ▼
FILED
 │
 ▼
DOCUMENT_DELIVERY
 │
 ▼
ARCHIVED
```

---

# 16. Communication Architecture

```text
Workflow Event
      │
      ▼
Notification Engine
      │
      ├── Email
      ├── Note
      ├── Warning
      ├── Error
      ├── Action Required
      └── Suggestion
              │
              ▼
         Gmail Service
              │
              ▼
          Customer
```

All important messages should also be recorded in the customer processing history.

---

# 17. Idempotency & Reprocessing

Every processing operation should be safe to repeat.

Example:

```text
Process Customer FY
       │
       ▼
Check Processing Version
       │
       ├── Already processed
       │       ↓
       │    Reuse / Rebuild
       │
       └── Not processed
               ↓
           Process
```

Generated files should use deterministic identifiers or controlled replacement rules to avoid duplicate reports.

---

# 18. Logging & Audit

Recommended logging levels:

```text
INFO
WARN
ERROR
DEBUG
AUDIT
```

Important events:

* Input received
* Document parsed
* Parser failed
* Value reconciled
* Rule selected
* Computation executed
* User override
* Warning generated
* Filing approved
* Filing completed
* Document generated

---

# 19. Security Architecture

```text
             User
              │
              ▼
         Google Form
              │
              ▼
       Controlled Drive
              │
       ┌──────┴──────┐
       ▼             ▼
 Processing       Documents
 Sheets            / Proofs
       │
       ▼
 Restricted Access
```

Security controls should include:

* Least privilege
* Restricted Drive sharing
* Controlled folder ownership
* No unnecessary public links
* Audit logging
* Sensitive-data minimisation
* Document retention policy
* Controlled service-account/script permissions where applicable

---

# 20. Multi-Client Architecture

The processing engine should be client-independent.

```text
                 Platform
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     Client A    Client B    Client N
        │           │           │
        ▼           ▼           ▼
     FY Data     FY Data     FY Data
        │           │           │
        └───────────┼───────────┘
                    ▼
             Shared Engines
```

Client-specific configuration should be externalised:

```text
Client Configuration
├── Profile
├── Communication
├── Service Package
├── Fee Configuration
├── Document Preferences
└── Processing Options
```

---

# 21. Current Technology Architecture

```text
Google Forms
     │
     ▼
Google Apps Script
     │
 ┌───┼─────────────────────────────┐
 │   │             │               │
 ▼   ▼             ▼               ▼
Sheets Drive      Docs           Gmail
 │     │           │               │
 └─────┴───────────┴───────────────┘
              │
              ▼
       Tax Processing Platform
```

The current platform intentionally avoids a dedicated web application and uses Google Workspace services to minimise infrastructure and operating cost.

---

# 22. Future Migration Architecture

The current implementation can evolve without replacing the core business logic.

### Phase 1 — Current

```text
Google Forms
     ↓
Apps Script
     ↓
Google Sheets / Drive
```

### Phase 2 — Service Layer

```text
Web / Forms
     ↓
API / Service Layer
     ↓
Processing Engines
     ↓
Database + Object Storage
```

### Phase 3 — AI-Assisted

```text
Document
   ↓
AI Extraction
   ↓
Deterministic Validation
   ↓
Tax Engine
   ↓
Human Review
```

### Phase 4 — Agent-Oriented

```text
                    AI Tax Assistant
                           │
       ┌───────────────────┼───────────────────┐
       ▼                   ▼                   ▼
Document Agent       Reconciliation Agent   Client Agent
       │                   │                   │
       └───────────────────┼───────────────────┘
                           ▼
                  Deterministic Tax Engine
                           │
                           ▼
                    Filing Workflow
```

AI should remain outside the authoritative calculation engine.

---

# 23. Core Architectural Principles

### Separation of Concerns

Document processing, financial computation, tax rules, workflow and presentation should remain independent.

### Deterministic Tax Logic

The same input and same rule version must produce the same result.

### Versioned Regulatory Logic

Every tax calculation must be associated with the relevant FY/AY and rule version.

### Source Traceability

Every calculated number should be traceable to its source.

### Reusable Parsers

New bank/broker formats should be implemented as independent adapters.

### Configuration-Driven Processing

Business rules, mappings and document formats should be configurable.

### Human-in-the-Loop

Ambiguous or conflicting data should be flagged for review.

### Extensible Architecture

New income types, forms, sources and regulations should be addable without redesigning the platform.

---

# 24. Target Repository Structure

A recommended source structure:

```text
smart-tax-filing/
│
├── README.md
├── ARCHITECTURE.md
├── CHANGELOG.md
├── LICENSE
│
├── src/
│   │
│   ├── config/
│   │   ├── constants.js
│   │   ├── environment.js
│   │   └── configuration.js
│   │
│   ├── workflow/
│   │   ├── controller.js
│   │   ├── state.js
│   │   └── orchestration.js
│   │
│   ├── ingestion/
│   │   ├── form16/
│   │   ├── ais/
│   │   ├── tis/
│   │   ├── bank/
│   │   ├── broker/
│   │   └── documents/
│   │
│   ├── normalisation/
│   │
│   ├── reconciliation/
│   │
│   ├── validation/
│   │
│   ├── computation/
│   │   ├── salary/
│   │   ├── capital-gains/
│   │   ├── other-sources/
│   │   ├── pgbp/
│   │   ├── exempt-income/
│   │   └── tax/
│   │
│   ├── books/
│   │   ├── trading-account/
│   │   ├── profit-loss/
│   │   ├── capital-account/
│   │   ├── balance-sheet/
│   │   └── depreciation/
│   │
│   ├── rules/
│   │   ├── fy/
│   │   ├── ay/
│   │   ├── regimes/
│   │   └── validations/
│   │
│   ├── reports/
│   │
│   ├── notifications/
│   │
│   ├── documents/
│   │
│   ├── audit/
│   │
│   └── utils/
│
├── templates/
│   ├── forms/
│   ├── sheets/
│   ├── docs/
│   └── emails/
│
├── test/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
│
└── docs/
    ├── architecture/
    ├── business-rules/
    ├── workflows/
    └── filing/
```

---

# 25. Architecture Summary

The SMART Tax Filing Platform can be viewed as five major systems:

```text
┌─────────────────────────────────────────────┐
│              1. DATA INGESTION              │
│ Forms / PDFs / Excel / AIS / TIS / Banks   │
│ Brokers / Form 16 / Supporting Documents   │
└──────────────────────┬──────────────────────┘
                       ▼
┌─────────────────────────────────────────────┐
│           2. DATA INTELLIGENCE              │
│ Parsing / Normalisation / Reconciliation    │
│ Validation / Classification                 │
└──────────────────────┬──────────────────────┘
                       ▼
┌─────────────────────────────────────────────┐
│             3. COMPUTATION                  │
│ Salary / CG / OS / PGBP / Books / Tax      │
│ Depreciation / Losses / Regime Comparison  │
└──────────────────────┬──────────────────────┘
                       ▼
┌─────────────────────────────────────────────┐
│            4. WORKFLOW                      │
│ Review / Corrections / Suggestions / Fee   │
│ Confirmation / Filing / Notifications      │
└──────────────────────┬──────────────────────┘
                       ▼
┌─────────────────────────────────────────────┐
│          5. DOCUMENT & AUDIT                │
│ Computation / Books / ITR / Acknowledgement│
│ Proofs / Logs / Communication / Archive    │
└─────────────────────────────────────────────┘
```

The result is a reusable financial-processing platform rather than a one-off ITR calculation script.

Its most important architectural characteristic is the separation between **source data, normalised financial data, deterministic computation, tax-rule configuration, workflow orchestration, and final filing/document presentation**. This separation gives the platform a practical path from its current Google Workspace implementation toward a scalable web platform and, later, AI-assisted or agent-based automation without rewriting the core financial logic.
