# Process Modeling & Analysis (BPMN Workflow)

## Document Control
- **Project Name:** Vehicle Import Cost Calculator App
- **Document Version:** 1.0
- **Author:** Danilo Andrijašević (Business Analyst / Operations)
- **Status:** Approved
- **Parent Document:** Business Requirements Document (BRD v1.0)

---

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

---

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

---

## 3. As-Is vs To-Be Gap Analysis

| Process Dimension | As-Is State (Manual) | To-Be State (Automated) | Impact / Benefit |
| :--- | :--- | :--- | :--- |
| **Execution Time** | Several hours | Less than 2 seconds | 99% reduction in quote turnaround time |
| **Data Consistency** | Fragmented spreadsheets | Centralized SQLite database | Single source of truth for rates |
| **Error Rate** | High (Human error in formulas) | 0% (Rule-based engine) | Financial risk mitigation |
| **Auditability** | None | Automated timestamps & UUIDs | Full operational transparency |
