# Functional & Non-Functional Requirements (FRD/NFR)

## Document Control
- **Project Name:** Vehicle Import Cost Calculator App
- **Document Version:** 1.0
- **Author:** Danilo Andrijašević (Business Analyst / Operations)
- **Status:** Approved
- **Parent Document:** Business Requirements Document (BRD v1.0)

---

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

---

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

---

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

---

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

---

## 3. Requirements Traceability Matrix (RTM)

| BRD Ref | Business Objective | FRD / User Story | NFR Reference | Target Verification |
| :--- | :--- | :--- | :--- | :--- |
| **BRD-OBJ-01** | Cut calculation time from hours to seconds | **US-01, US-02** | **NFR-PERF-01** | Automated UAT / Performance Test |
| **BRD-OBJ-02** | Eliminate manual formula & tax errors | **US-02** | **NFR-SEC-01** | Unit Test Suite (Calculation Engine) |
| **BRD-OBJ-03** | Centralize tariff parameters in relational DB | **US-03** | **NFR-SEC-02** | DB Integration Test |
