# Business Requirements Document (BRD)

## Document Control
- **Project Name:** Vehicle Import Cost Calculator App
- **Document Version:** 1.0
- **Author:** Danilo Andrijašević (Business Analyst / Operations)
- **Status:** Approved

---

## 1. Executive Summary & Business Context
Currently, import managers rely on manual calculations spread across disconnected Excel spreadsheets and external tariff registries. Calculating total import, customs clearance, and logistics costs takes up to several hours per vehicle, creating operational bottlenecks and introducing human error into financial estimates. 

The **Vehicle Import Cost Calculator App** introduces a centralized, web-based automation tool that delivers standard, accurate, and instant cost calculations.

---

## 2. Problem Statement & Business Objectives

### Problem Statement
Import managers spend **up to several hours** performing manual calculations for a single vehicle's full import and customs clearance cost. Due to the complexity of excise formulas, age-dependent tax brackets, and fluctuating logistics rates, manual entry frequently results in financial discrepancies and delayed client quotes.

### Business Objectives (SMART)
- **Reduce Calculation Time:** Cut quote generation time from several hours to under 5 seconds.
- **Eliminate Calculation Errors:** Achieve 100% adherence to defined customs, excise, and tax business rules.
- **Standardize Data:** Centralize tariff parameters in a relational database (SQLite) instead of unmanaged spreadsheets.

---

## 3. Stakeholder Matrix

| Role | Primary Interest | Responsibilities |
| :--- | :--- | :--- |
| **Import Manager** | Quick and accurate quote generation | Enters vehicle parameters, generates customer estimates |
| **Financial Controller** | Profitability and regulatory compliance | Audits calculations, updates base tariff tables and margins |
| **Business Analyst** | Requirements & workflow alignment | Translates business rules into functional/technical specs |
| **System Administrator** | Application availability & stability | Manages application deployment, database backups, and access |

---

## 4. Business Rules & Calculation Engine

The total landed cost ($V_{total}$) is calculated using the following deterministic formula:

$$V_{total} = V_{base} + T_{customs} + T_{logistics} + F_{service}$$

### 1. Customs Fees ($T_{customs}$)
* **Import Duty ($D_{import}$):** 10% of Invoice Cost ($V_{base}$).
* **Excise Duty ($E_{excise}$):** Calculated based on engine displacement (CC), fuel type, and vehicle age bracket.
* **Value Added Tax ($VAT$):** 20% applied to the sum of ($V_{base} + D_{import} + E_{excise}$).
* **Total Customs:** $T_{customs} = D_{import} + E_{excise} + VAT$.

### 2. Logistics & Fees ($T_{logistics}$)
* **Auction Fee:** Tiered percentage based on auction platform rules (5–8%).
* **Inland + Maritime Freight:** Fixed base rate depending on location region.

### 3. Service Fee ($F_{service}$)
* Fixed company management fee (500 EUR).

---

## 5. High-Level Scope

### In-Scope
- Web UI input form for vehicle specifications (Year, Fuel Type, Engine Capacity, Auction Price, Location).
- Automated calculation engine applying current customs formulas and logistics tables.
- Calculation summary output screen with line-item breakdown.
- Export / save calculation results as a structured record.

### Out-of-Scope (Phase 1)
- Live API integration with foreign exchange rates.
- External payment gateway integration.

---

## 6. Business Risks & Mitigation

| Risk | Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| **Legislative changes in customs tariffs** | High | Abstract tariff rates into editable SQLite config tables instead of hardcoding. |
| **Incorrect user input (e.g., negative engine capacity)** | Medium | Implement client-side and server-side input validation with strict constraints. |
