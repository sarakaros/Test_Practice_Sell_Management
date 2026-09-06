
# Manual Testing Practice — Sales Management Application (Ứng dụng Quản lý bán hàng)

A complete manual testing deliverable set for a desktop sales-management application: test plan,
test scenarios, and 38 executed test cases with results.

Produced for **Software Quality Assurance and Software Testing (CT243HM01)**, College of Information
and Communication Technology, Can Tho University — November 2025.
Instructor: Ph.D. Nguyen Cong Danh.
Team: Thạch Thanh Nhi (B2203572), Phạm Lưu Khánh Vân (B2203592).

> **My contribution (Thạch Thanh Nhi):** I owned the **receipt / invoice catalogue module** —
> scenarios `TS_QLBH_REC_01`–`REC_04` and all 16 test cases under them (`TC_REC_ADD_01`–`07`,
> `TC_REC_EDIT_01`–`08`, `TC_REC_SYS_03`), executed against Build 1.0. Seven of them failed, and
> they account for every date-validation defect listed below. My teammate owned the city and
> customer modules.

---

## What's in this repository

| File                    | Description                                                                                               |
| ----------------------- | --------------------------------------------------------------------------------------------------------- |
| `Test Plan.docx`      | Test plan v1.0 — scope, test types, environment, schedule, resources, risk register, entry/exit criteria |
| `Test Scenarios.xlsx` | 10 scenarios derived from the requirement spec, each with importance (P1–P3) and a planned case count    |
| `Test Cases.xlsx`     | Test cases with steps, test data, expected vs. actual results and status — sheet`Build 1.0`            |

The Office files do not preview on GitHub, so a worked test case and a worked defect report are
reproduced in full below.

## System under test

A Windows desktop sales-management application (C#/.NET + SQL Server) with three catalogue modules:

- **Danh mục thành phố** — city catalogue (add / edit)
- **Danh mục khách hàng** — customer catalogue (add / edit)
- **Danh mục hóa đơn** — receipt / invoice catalogue (add / edit)

Test environment: Windows 10 Pro, SQL Server Management Studio, Visual Studio 2022. Testing was
fully manual — the test plan sets automation strategy to N/A.

## Approach

The work follows a standard document chain: **requirements → test plan → scenarios → test cases → execution log**.

1. **Test plan** fixes the scope (the three catalogue sections, nothing else), the test types in
   use, the two build iterations with dates, human and technical resources, a risk register with
   mitigations, and the entry/exit criteria — notably *"no P1/P2 functional defect from any
   iteration"* as the exit gate.
2. **Scenarios** decompose each module into add / edit / character-support / data-display checks,
   each with an importance rating and an estimated case count, so effort is planned before cases
   are written.
3. **Test cases** expand each scenario into numbered steps with concrete test data and an expected
   result recorded *before* execution, against which the observed actual result is compared. Every
   case carries a Priority (P1–P3), Category, Type (Functional / GUI) and a named owner.

### Where the cases were aimed

Coverage was deliberately weighted toward **negative cases rather than happy paths** — 1 of the 7
receipt-add cases is a valid-data path; the other 6 omit a mandatory field or supply an invalid
date. That weighting is why the failure rate is high: the defects were in validation, and validation
is only exercised by invalid input.

| Focus                        | Examples                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------- |
| Missing mandatory fields     | Add a receipt with no`MaHD` / `MaKH` / `MaNV`; add a city with only an ID       |
| Business-rule validation     | Receiving date earlier than invoice date; invoice date edited past the receiving date |
| Invalid input formats        | `11/34/2025` as a date; malformed customer phone number                             |
| Uniqueness / data integrity  | Duplicate receipt ID, duplicate customer record, existing city name                   |
| Default-value behaviour      | What the system writes when a date field is left empty                                |
| Vietnamese character support | UTF-8 rendering in each of the three catalogue forms                                  |

### ID conventions

```
TS_QLBH_<MODULE>_<NN>          Test scenario     e.g. TS_QLBH_REC_01
TC_<MODULE>_<ACTION>_<NN>      Test case         e.g. TC_REC_ADD_05
```

Modules: `CITY`, `CUS` (customer), `REC` (receipt). Actions: `ADD`, `EDIT`, `SYS` (system/data handling).

---

## Worked example — a test case

| Field                                | Value                                                                                                                             |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| **Test case ID**               | `TC_REC_ADD_05`                                                                                                                 |
| **Scenario**                   | `TS_QLBH_REC_01` — Check if the user is able to add new receipts                                                               |
| **Title**                      | Check the adding receipts with the receiving date before the invoice date                                                         |
| **Priority / Category / Type** | P1 · Progression · Functional                                                                                                   |
| **Owner**                      | Thạch Thanh Nhi                                                                                                                  |
| **Test data**                  | `MaHD` = BUBUBU · Customer = Nhi Entertainment · Staff = Hùng · `NgayLapHD` = 11/09/2025 · `NgayNhanHang` = 10/09/2025 |

**Steps**

1. Log in to the application.
2. Open *Quản lý Danh mục Hoá đơn*.
3. Click **Thêm**.
4. Enter valid information, but set `NgayNhanHang` (receiving date) earlier than `NgayLapHD` (invoice date).
5. Click **Lưu**.

**Expected result** — The save is rejected with `"Ngày không hợp lệ!"`; no receipt row is written.

**Actual result** — The save succeeds and the application displays `"Đã thêm xong!"`. A receipt is
stored with goods received the day before the invoice exists.

**Status:** ❌ Fail → `BUG-REC-002`

---

## Worked example — a defect report

Two failing cases, `TC_REC_ADD_06` and `TC_REC_ADD_07`, share one root cause, so they are reported
as a single defect rather than two.

| Field                         | Value                                                                          |
| ----------------------------- | ------------------------------------------------------------------------------ |
| **Defect ID**           | `BUG-REC-001`                                                                |
| **Title**               | Omitting a date on a new receipt stores 01/01/1900 instead of the current date |
| **Module**              | Danh mục hóa đơn (receipt catalogue) — Add                                |
| **Found in**            | Build 1.0 (02–09 Nov 2025)                                                    |
| **Severity / Priority** | Major / P1                                                                     |
| **Found by**            | Thạch Thanh Nhi                                                               |
| **Environment**         | Windows 10 Pro · SQL Server · Visual Studio 2022                             |
| **Related cases**       | `TC_REC_ADD_06`, `TC_REC_ADD_07`                                           |

**Steps to reproduce**

1. Log in to the application.
2. Open *Quản lý Danh mục Hoá đơn*.
3. Click **Thêm**.
4. Enter valid values for `MaHD`, `MaKH` and `MaNV`, and leave `NgayLapHD` (invoice date) empty.
5. Click **Lưu**.
6. Repeat steps 1–5 leaving `NgayNhanHang` (receiving date) empty instead.

**Expected:** the empty date field defaults to today's date, per the requirement spec.

**Actual:** the field is stored as **01/01/1900** — the SQL Server `smalldatetime` floor value —
and the receipt is saved without warning.

**Impact:** every receipt saved with an omitted date carries a date 125 years in the past. Any
report, sort or date-range filter over the receipt table silently misplaces these rows, and the
error is invisible on the add screen because the save reports success.

**Reproducibility:** consistent — occurs on both date fields, on every attempt.

**Suggested fix:** validate both date fields before commit; if a default is intended, set it in the
application layer rather than letting the database supply its type minimum.

**Status:** Open in Build 1.0.

---

## Execution results — Build 1.0

**38 test cases executed · 20 passed · 18 failed (47% failure rate)**

| Module             |        Cases |       Passed |       Failed | Owner       |
| ------------------ | -----------: | -----------: | -----------: | ----------- |
| City catalogue     |            8 |            3 |            5 | Khánh Vân |
| Customer catalogue |           14 |            8 |            6 | Khánh Vân |
| Receipt catalogue  |           16 |            9 |            7 | Thanh Nhi   |
| **Total**    | **38** | **20** | **18** |             |

35 of the 38 cases are rated P1, so under the plan's own exit criteria — *no P1/P2 functional defect
from any iteration* — Build 1.0 does not pass.

### Defect themes

- **Mandatory-field validation is absent.** Records save with required fields left empty: the app
  returns `"Đã thêm xong"` where `"Thiếu thông tin"` was expected. Affects city ID, city name,
  customer ID, customer phone, company name and address.
- **Date handling is unvalidated in the receipt module.** A receipt saves with a receiving date
  earlier than its invoice date; an omitted date is stored as 01/01/1900; an invalid date such as
  `11/34/2025` is silently replaced with today's date instead of being rejected.
- **Uniqueness rules are inconsistent.** A duplicate receipt ID is accepted on edit; duplicate city
  names are rejected on some paths and not others.
- **Vietnamese character handling differs by module** — correct in the city and receipt forms,
  incorrect in customer details.

## Known gaps in these documents

Listed here rather than quietly fixed, because they are what a review would catch:

- Scenario `TS_QLBH_REC_04` (data display) is planned in the scenario sheet but has no test cases.
- Three city cases reference `TS_CITY_01` rather than the conventional `TS_QLBH_CITY_01`, and one
  customer case is numbered `TC_CITY_SYS_02` under scenario `TS_QLBH_CUS_03`.
- The "Requirement — Reference document index" column in the scenario sheet is unpopulated, so
  scenario-to-requirement traceability is incomplete.
- Defects are recorded inside the test-case sheet; there is no separate defect log with its own IDs.
  The worked example above shows the format that would be used.
- The test plan leaves the Review/Approval table unsigned, and section 7 refers to a performance
  test plan that was not in scope for this course.
