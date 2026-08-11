Ні, якщо скопіювати попередню відповідь напевно, **все відобразиться некоректно**.

При копіюванні тексту з чату розмітка Markdown (заголовки, списки, форматування) зливається в один суцільний рядок (наприклад, `# Part 1... ## 1. Executive...` ідуть в один рядок), а кодові блоки розриваються.

Ось повністю правильний, відформатований текст без збоїв. Скопіюйте його з блоку нижче:

```markdown
# 🚘 Case Study: Vehicle Import Cost Calculator App

**Author:** Danilo Andrijašević (Business Analyst / Operations)  
**Status:** Complete  
**Project Overview:** End-to-end documentation for an automated vehicle customs clearance and logistics cost calculation system.

---

# Part 1: Business Requirements Document (BRD)

## 1. Executive Summary & Business Context
Currently, import managers rely on manual calculations spread across disconnected Excel spreadsheets and external tariff registries. Calculating total import, customs clearance, and logistics costs takes up to several hours per vehicle, creating operational bottlenecks and introducing human error into financial estimates. 

The **Vehicle Import Cost Calculator App** introduces a centralized, web-based automation tool that delivers standard, accurate, and instant cost calculations.

## 2. Problem Statement & Business Objectives

### Problem Statement
Import managers spend **up to several hours** performing manual calculations for a single vehicle's full import and customs clearance cost. Due to the complexity of excise formulas, age-dependent tax brackets, and fluctuating logistics rates, manual entry frequently results in financial discrepancies and delayed client quotes.

### Business Objectives (SMART)
- **Reduce Calculation Time:** Cut quote generation time from several hours to under 5 seconds.
- **Eliminate Calculation Errors:** Achieve 100% adherence to defined customs, excise, and tax business rules.
- **Standardize Data:** Centralize tariff parameters in a relational database (SQLite) instead of unmanaged spreadsheets.

## 3. Stakeholder Matrix

| Role | Primary Interest | Responsibilities |
| :--- | :--- | :--- |
| **Import Manager** | Quick and accurate quote generation | Enters vehicle parameters, generates customer estimates |
| **Financial Controller** | Profitability and regulatory compliance | Audits calculations, updates base tariff tables and margins |
| **Business Analyst** | Requirements & workflow alignment | Translates business rules into functional/technical specs |
| **System Administrator** | Application availability & stability | Manages application deployment, database backups, and access |

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

## 5. High-Level Scope

### In-Scope
- Web UI input form for vehicle specifications (Year, Fuel Type, Engine Capacity, Auction Price, Location).
- Automated calculation engine applying current customs formulas and logistics tables.
- Calculation summary output screen with line-item breakdown.
- Export / save calculation results as a structured record.

### Out-of-Scope (Phase 1)
- Live API integration with foreign exchange rates.
- External payment gateway integration.

## 6. Business Risks & Mitigation

| Risk | Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| **Legislative changes in customs tariffs** | High | Abstract tariff rates into editable SQLite config tables instead of hardcoding. |
| **Incorrect user input (e.g., negative engine capacity)** | Medium | Implement client-side and server-side input validation with strict constraints. |

---

# Part 2: Functional & Non-Functional Requirements (FRD/NFR)

## 1. Functional Requirements & User Stories

### US-01: Vehicle Parameter Input
**As an** Import Manager,  
**I want to** enter vehicle attributes (Year, Fuel Type, Engine Displacement, Auction Price, Location) into a structured web form,  
**So that** the application receives all required parameters to execute customs and logistics calculations.

#### Acceptance Criteria (Gherkin Format)

**Scenario: Successful input submission with valid parameters**
- **Given** the Import Manager is on the Calculation Form page
- **When** they select "Gasoline" as Fuel Type
- **And** enter "1998" as Engine Displacement (cc)
- **And** enter "2018" as Manufacture Year
- **And** enter "8500" as Auction Base Price (EUR)
- **And** click "Calculate Total Cost"
- **Then** the system validates all fields
- **And** redirects the user to the Calculation Breakdown screen with status "200 OK".

**Scenario: Input validation failure for negative values**
- **Given** the Import Manager is on the Calculation Form page
- **When** they enter "-1500" as Auction Base Price
- **And** click "Calculate Total Cost"
- **Then** the system halts processing
- **And** displays an inline validation error: "Base price must be greater than 0".

### US-02: Automated Customs & Tax Calculation
**As an** Import Manager,  
**I want** the system to automatically apply customs duty, engine/age excise rates, VAT, and logistics fees,  
**So that** I eliminate manual lookup errors and receive a 100% accurate cost breakdown instantly.

#### Acceptance Criteria (Gherkin Format)

**Scenario: Accurate calculation for standard gasoline vehicle**
- **Given** a vehicle with Base Price = 10,000 EUR, Import Duty = 10%, Excise = 500 EUR
- **When** the calculation engine executes
- **Then** the Import Duty is calculated as 1,000 EUR
- **And** the VAT (20%) is calculated on (10,000 + 1,000 + 500) = 2,300 EUR
- **And** Total Customs Fee (T_customs) equals 3,800 EUR.

### US-03: Detailed Calculation Summary & Record Export
**As an** Import Manager / Financial Controller,  
**I want to** view an itemized financial breakdown and export/save the calculation record,  
**So that** I can share an audited breakdown with the client and maintain a record in the database.

#### Acceptance Criteria (Gherkin Format)

**Scenario: Display line-item summary and save calculation**
- **Given** the calculation engine has completed execution
- **Then** the UI displays itemized costs:
  - Vehicle Base Price: €10,000
  - Customs Duty (10%): €1,000
  - Excise Duty: €500
  - VAT (20%): €2,300
  - Freight & Logistics: €1,200
  - Service Fee: €500
  - **Total Landed Cost: €15,500**
- **And** provides a "Save Calculation Record" button that logs the result to SQLite.

## 2. Non-Functional Requirements (NFR)

### 2.1 Performance & Scalability (NFR-PERF)
* **NFR-PERF-01 (Response Time):** The application calculation engine must compute and render total landed cost results within **less than 2 seconds** from form submission.
* **NFR-PERF-02 (Throughput):** The SQLite database and Flask backend must handle up to **50 concurrent calculation requests** without performance degradation.

### 2.2 Security & Data Integrity (NFR-SEC)
* **NFR-SEC-01 (Input Sanitization):** All form inputs must be sanitized against SQL Injection (SQLi) and Cross-Site Scripting (XSS) before hitting the database or calculation engine.
* **NFR-SEC-02 (Auditability):** Every calculation saved to the database must include a timestamp (`created_at`) and a unique calculation ID (`calc_uuid`).

### 2.3 Usability & Reliability (NFR-USE)
* **NFR-USE-01 (Error Handling):** Clear error messages must be displayed for missing parameters, system timeouts, or invalid tariff table configurations without crashing the web session.
* **NFR-USE-02 (Browser Compatibility):** The interface must be responsive and fully functional on modern Chromium-based browsers, Firefox, and Safari.

## 3. Requirements Traceability Matrix (RTM)

| BRD Ref | Business Objective | FRD / User Story | NFR Reference | Target Verification |
| :--- | :--- | :--- | :--- | :--- |
| **BRD-OBJ-01** | Cut calculation time from hours to seconds | **US-01, US-02** | **NFR-PERF-01** | Automated UAT / Performance Test |
| **BRD-OBJ-02** | Eliminate manual formula & tax errors | **US-02** | **NFR-SEC-01** | Unit Test Suite (Calculation Engine) |
| **BRD-OBJ-03** | Centralize tariff parameters in relational DB | **US-03** | **NFR-SEC-02** | DB Integration Test |

---

# Part 3: Process Modeling & Analysis (BPMN Workflow)

## 1. As-Is Process (Manual & Fragmented Workflow)

### Context & Bottlenecks
Prior to automation, import managers performed manual cost estimation across multiple spreadsheets, PDF custom rate schedules, and shipping carrier websites.

- **Cycle Time:** Up to several hours per vehicle calculation.
- **Key Bottlenecks:**
  - Manual search for engine displacement/age excise coefficients.
  - Risk of stale currency conversion rates.
  - Frequent Excel formula overrides causing pricing errors.

### As-Is Workflow Breakdown
1. **Start:** Client requests import quote.
2. **Step 1:** Manager receives vehicle parameters.
3. **Step 2:** Open internal Excel calculation template.
4. **Step 3:** Manually lookup customs duty & excise rates in PDF schedules.
5. **Decision:** Are rates up to date?
   - **No / Uncertain:** Search external customs registry -> Manually override rate cells in Excel.
   - **Yes:** Proceed to calculation.
6. **Step 4:** Calculate shipping costs via logistics partner tables.
7. **Step 5:** Aggregate totals and write quote email.
8. **End:** Send estimate to client.

## 2. To-Be Process (Automated Web Calculator)

### Operational Improvements
With the **Vehicle Import Cost Calculator App**, manual lookup is eliminated. Rates, age brackets, and logistics parameters are queried directly from an updated SQLite relational database.

- **Cycle Time:** Less than 2 seconds.
- **Key Improvements:**
  - Standardized parameter entry via validated web forms.
  - Automatic tax and duty resolution via background calculation engine.
  - Automated audit log generation and record saving.

### To-Be Workflow Breakdown
1. **Start:** Client requests import quote.
2. **Step 1:** Manager opens Web Calculator UI.
3. **Step 2:** Input vehicle specs (Year, Engine CC, Base Price, Location).
4. **Step 3:** Click "Calculate Total Cost".
5. **System Check:** Input Validation.
   - **Invalid Input:** Display inline validation error -> Return to Step 2.
   - **Valid Input:** Proceed to processing.
6. **System Action:** Backend fetches tariff rules from SQLite DB.
7. **System Action:** Calculation Engine computes Duty, Excise, VAT & Freight.
8. **Step 4:** Render line-item breakdown screen.
9. **System Action:** Save record to Database with unique UUID.
10. **End:** Export/Share accurate quote with client.

## 3. As-Is vs To-Be Gap Analysis

| Process Dimension | As-Is State (Manual) | To-Be State (Automated) | Impact / Benefit |
| :--- | :--- | :--- | :--- |
| **Execution Time** | Several hours | Less than 2 seconds | 99% reduction in quote turnaround time |
| **Data Consistency** | Fragmented spreadsheets | Centralized SQLite database | Single source of truth for rates |
| **Error Rate** | High (Human error in formulas) | 0% (Rule-based engine) | Financial risk mitigation |
| **Auditability** | None | Automated timestamps & UUIDs | Full operational transparency |

---

# Part 4: Technical Artifacts & Data Architecture

## 1. Database Architecture (SQLite Schema)

The system relies on a relational database design in SQLite to maintain decoupling between base tariff rates and historic user calculations.

### Schema Definition (DDL)

```sql
-- Table 1: Excise Tariff Configuration
CREATE TABLE IF NOT EXISTS excise_tariffs (
    tariff_id INTEGER PRIMARY KEY AUTOINCREMENT,
    fuel_type TEXT NOT NULL,          -- Gasoline, Diesel, Electric, Hybrid
    min_engine_cc INTEGER NOT NULL,   -- e.g., 0
    max_engine_cc INTEGER NOT NULL,   -- e.g., 2000
    rate_per_cc REAL NOT NULL,        -- Fixed rate multiplier per CC
    effective_year INTEGER NOT NULL   -- Configurable active tax year
);

-- Table 2: Logistics Base Rates
CREATE TABLE IF NOT EXISTS logistics_rates (
    rate_id INTEGER PRIMARY KEY AUTOINCREMENT,
    origin_region TEXT NOT NULL,      -- USA East Coast, EU, Asia
    flat_sea_freight REAL NOT NULL,   -- Shipping container cost
    inland_trucking_base REAL NOT NULL
);

-- Table 3: Calculation Audit Records
CREATE TABLE IF NOT EXISTS calculation_records (
    calc_uuid TEXT PRIMARY KEY,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    manufacture_year INTEGER NOT NULL,
    fuel_type TEXT NOT NULL,
    engine_cc INTEGER NOT NULL,
    auction_price REAL NOT NULL,
    calculated_customs_duty REAL NOT NULL,
    calculated_excise REAL NOT NULL,
    calculated_vat REAL NOT NULL,
    calculated_logistics REAL NOT NULL,
    total_landed_cost REAL NOT NULL
);

```

## 2. Core Business Logic (Python Calculation Engine)

The following Python function encapsulates the deterministic calculation rules specified in BRD Section 4:

```python
def calculate_landed_cost(auction_price: float, engine_cc: int, fuel_type: str, age: int, logistics_cost: float) -> dict:
    """
    Executes automated tariff resolution and calculates total import cost.
    """
    # 1. Customs Import Duty (10%)
    import_duty = auction_price * 0.10
    
    # 2. Excise Duty Logic (Base Rule)
    excise_rate_per_cc = 0.50 if fuel_type.lower() == 'gasoline' else 0.75
    excise_duty = engine_cc * excise_rate_per_cc
    
    # Age multiplier adjustment
    if age > 5:
        excise_duty *= 1.25
        
    # 3. VAT (20% on Base + Duty + Excise)
    vatable_base = auction_price + import_duty + excise_duty
    vat = vatable_base * 0.20
    
    # 4. Total Customs Fee
    total_customs = import_duty + excise_duty + vat
    
    # 5. Fixed Management Fee
    service_fee = 500.0
    
    # 6. Total Landed Cost
    total_cost = auction_price + total_customs + logistics_cost + service_fee
    
    return {
        "base_price": round(auction_price, 2),
        "import_duty": round(import_duty, 2),
        "excise_duty": round(excise_duty, 2),
        "vat": round(vat, 2),
        "total_customs": round(total_customs, 2),
        "logistics_cost": round(logistics_cost, 2),
        "service_fee": service_fee,
        "total_landed_cost": round(total_cost, 2)
    }

```

## 3. Data Analytics & Reporting Queries (SQL)

### Query 1: Monthly Calculation Volume & Average Landed Cost

Used by Operations and Management to track demand and average import budgets.

```sql
SELECT 
    STRFTIME('%Y-%m', created_at) AS calc_month,
    COUNT(calc_uuid) AS total_calculations,
    AVG(auction_price) AS avg_auction_price,
    AVG(total_landed_cost) AS avg_total_landed_cost
FROM calculation_records
GROUP BY calc_month
ORDER BY calc_month DESC;

```

### Query 2: Breakdown of Import Calculations by Fuel Type

Identifies shifting market trends toward hybrid or gasoline vehicles.

```sql
SELECT 
    fuel_type,
    COUNT(*) AS calculation_count,
    ROUND(AVG(calculated_excise), 2) AS avg_excise_paid,
    ROUND(AVG(total_landed_cost), 2) AS avg_landed_cost
FROM calculation_records
WHERE created_at >= DATE('now', '-30 days')
GROUP BY fuel_type
ORDER BY calculation_count DESC;

```

```

```
