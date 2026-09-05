# Manual Testing Practice — Sales Management Application (Ứng dụng Quản lý bán hàng)

A complete manual testing deliverable set for a desktop sales-management application: test plan,
test scenarios, and executed test cases across two build iterations.

Produced for **Software Quality Assurance and Software Testing (CT243HM01)**, College of Information
and Communication Technology, Can Tho University — November 2025.
Instructor: Ph.D. Nguyen Cong Danh.
Team: Thạch Thanh Nhi (B2203572), Phạm Lưu Khánh Vân (B2203592).

---

## What's in this repository

| File                    | Description                                                                                                                                      |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Test Plan.docx`      | Test plan v1.0 — scope, test types, environment, schedule, resources, risk register, entry/exit criteria                                        |
| `Test Scenarios.xlsx` | 10 high-level scenarios derived from the requirement spec, each with priority and planned case count                                             |
| `Test Cases.xlsx`     | Detailed test cases with steps, test data, expected vs. actual results, and execution status. One sheet per build (`Build 1.0`, `Build 2.0`) |

## System under test

A Windows desktop sales-management application (C#/.NET + SQL Server) with three catalogue modules:

- **Danh mục thành phố** — city catalogue (add / edit)
- **Danh mục khách hàng** — customer catalogue (add / edit)
- **Danh mục hóa đơn** — receipt / invoice catalogue (add / edit)

Test environment: Windows 10 Pro, SQL Server Management Studio, Visual Studio 2022. Testing was
fully manual — no automation was in scope.

## Approach

Testing followed a standard document chain: **requirements → test plan → scenarios → test cases → execution log**.

1. **Test plan** defines what is in and out of scope, the test types used (progression, negative /
   corner, manual regression), the two build iterations, human and technical resources, four
   identified risks with mitigations, and the entry/exit criteria — notably *"no P1/P2 functional
   defect from any iteration"* as an exit gate.
2. **Scenarios** decompose each module into add / edit / data-integrity checks, with a rough case
   estimate per scenario so effort can be planned before cases are written.
3. **Test cases** expand each scenario into numbered steps with concrete test data, an expected
   result stated *before* execution, and the observed actual result recorded alongside it. Every
   case carries Priority (P1–P3), Category (Sanity / Progression / Regression), Type (Functional /
   GUI / Database) and an owner.

Design emphasis was on **negative and boundary cases** rather than happy paths: empty mandatory
fields, duplicate primary keys, invalid phone formats, out-of-order dates, and Vietnamese (UTF-8)
character handling.

### ID conventions

```
TS_QLBH_<MODULE>_<NN>          Test scenario     e.g. TS_QLBH_REC_01
TC_<MODULE>_<ACTION>_<NN>      Test case         e.g. TC_REC_ADD_05
```

Modules: `CITY`, `CUS` (customer), `REC` (receipt). Actions: `ADD`, `EDIT`, `SYS` (system/data handling).

## Execution results — Build 1.0

**38 test cases executed · 20 passed · 18 failed (47% failure rate)**

| Module             |        Cases |       Passed |       Failed |
| ------------------ | -----------: | -----------: | -----------: |
| City catalogue     |            8 |            3 |            5 |
| Customer catalogue |           14 |            8 |            6 |
| Receipt catalogue  |           16 |            9 |            7 |
| **Total**    | **38** | **20** | **18** |

35 of 38 cases were rated P1, so under the plan's own exit criteria Build 1.0 does not pass.

### Defect themes

- **Mandatory-field validation is absent.** Records save successfully with required fields left
  empty — the app returns `"Đã thêm xong"` where `"Thiếu thông tin"` was expected. Affects city ID,
  city name, customer phone, company name and address.
- **Date logic is unvalidated.** A receipt can be saved with a receiving date earlier than its
  invoice date, and with invoice or receiving date omitted entirely.
- **Uniqueness rules are inconsistent.** Duplicate customer records are accepted; duplicate city
  names are rejected in some paths but not others.
- **Vietnamese character handling differs by module** — correct in the city and receipt forms,
  incorrect in customer details.

## Build 2.0

The second iteration (09–28 Nov 2025) extends coverage to authentication and a personal-details
module: valid/invalid credential combinations, GUI element presence and behaviour, and profile
photo upload with format and file-size boundaries (< 1 MB / > 1 MB).

**Status: partially executed.** 13 cases are authored; 3 have recorded results, and 3 placeholder
rows (`TC_UDQLBH_HT_CHHT_*`) have IDs only. The sheet is included as-is rather than trimmed, since
the in-progress state is part of the record.

## Known gaps

Flagged here rather than quietly fixed, because they are the kind of thing a review would catch:

- Scenario `TS_QLBH_REC_04` (data display) is planned in the scenario sheet but has no corresponding
  test cases.
- Three city cases reference `TS_CITY_01` instead of the conventional `TS_QLBH_CITY_01`.
- The "Requirement — Reference document index" column in the scenario sheet is unpopulated, so
  scenario-to-requirement traceability is incomplete.
- The test plan carries a boilerplate confidentiality header inherited from the course template.
