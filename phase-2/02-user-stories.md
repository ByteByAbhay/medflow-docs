# 👥 Phase 2: IPD Admission - User Stories

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document contains **user stories** for the IPD Admission Module, written from the perspective of different user roles. Each story includes acceptance criteria for testing and validation.

---

## 2. User Story Format

```
As a [role]
I want to [action]
So that [benefit]

Acceptance Criteria:
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3
```

---

## 3. User Stories by Role

### 3.1 OPD Doctor / Consultant

#### US-DOC-001: Request Patient Admission

**Story:**
```
As an OPD Doctor/Consultant
I want to create an admission request during consultation
So that patients requiring inpatient care can be admitted smoothly
```

**Acceptance Criteria:**
- [ ] I can select "Admission Required" from consultation decision options
- [ ] I can specify admission reason (min 20 characters)
- [ ] I can enter provisional diagnosis
- [ ] I can select ward type preference (General/Private/ICU)
- [ ] I can set urgency level (Routine/Urgent/Emergency)
- [ ] I can enter estimated stay duration
- [ ] I can write detailed admission orders (min 50 characters)
- [ ] I can add special requirements and diet instructions
- [ ] System validates all required fields before submission
- [ ] I receive confirmation that admission request is created
- [ ] Nurse is notified of new request

**Priority:** P0  
**Story Points:** 5

---

#### US-DOC-002: View My Admitted Patients

**Story:**
```
As a Doctor
I want to view all patients I'm currently managing in IPD
So that I can track my admitted patients and their status
```

**Acceptance Criteria:**
- [ ] I can see a list of all patients admitted under my care
- [ ] List shows: Patient name, UHID, Ward/Room/Bed, Admission date, Days admitted, Diagnosis
- [ ] I can filter by ward
- [ ] I can search by patient name or UHID
- [ ] I can click on a patient to view full admission details
- [ ] List updates in real-time when new patients are admitted
- [ ] I can see discharge pending patients separately

**Priority:** P0  
**Story Points:** 3

---

#### US-DOC-003: Initiate Patient Discharge

**Story:**
```
As a Doctor
I want to initiate discharge for a patient
So that the patient can be discharged with proper documentation
```

**Acceptance Criteria:**
- [ ] I can select a patient from my admitted patients list
- [ ] I can click "Initiate Discharge" button
- [ ] I can enter discharge date/time (default: current time)
- [ ] I can select discharge type (Regular/LAMA/Absconded/Death/Transfer)
- [ ] I can enter final discharge diagnosis (required)
- [ ] I can write discharge summary (min 100 characters, rich text editor)
- [ ] I can enter follow-up instructions
- [ ] I can set follow-up date (optional)
- [ ] I can prescribe medications on discharge
- [ ] I can add diet advice and activity restrictions
- [ ] I can specify warning signs to watch for
- [ ] System generates printable discharge summary PDF
- [ ] Patient status changes to "Discharge Pending"
- [ ] Reception is notified for final bill settlement

**Priority:** P0  
**Story Points:** 8

---

### 3.2 Nurse

#### US-NUR-001: View Admission Request Queue

**Story:**
```
As a Nurse
I want to view all pending admission requests
So that I can process them in priority order
```

**Acceptance Criteria:**
- [ ] I can see a list of all pending admission requests
- [ ] List shows: Patient name, UHID, Requesting doctor, Ward preference, Urgency, Request time
- [ ] Requests are sorted by urgency (Emergency > Urgent > Routine)
- [ ] I can filter by ward type, urgency, requesting doctor
- [ ] I can search by patient name or UHID
- [ ] Emergency requests are highlighted in red
- [ ] I can click on a request to view full details
- [ ] List updates in real-time when new requests arrive

**Priority:** P0  
**Story Points:** 3

---

#### US-NUR-002: Approve/Reject Admission Request

**Story:**
```
As a Nurse
I want to approve or reject admission requests
So that only valid admissions proceed to bed allocation
```

**Acceptance Criteria:**
- [ ] I can view full admission request details
- [ ] I can see: Patient info, Admission reason, Diagnosis, Ward preference, Admission orders
- [ ] I can click "Approve" button to approve request
- [ ] I can click "Reject" button to reject request
- [ ] If rejecting, I must provide rejection reason (min 20 characters)
- [ ] I can click "Request More Info" to send back to doctor with comments
- [ ] Approved requests move to bed allocation queue
- [ ] Rejected requests notify the requesting doctor
- [ ] All actions are logged with timestamp and my user ID

**Priority:** P0  
**Story Points:** 5

---

#### US-NUR-003: Allocate Bed to Patient

**Story:**
```
As a Nurse
I want to allocate a bed to an approved admission
So that the patient can be physically admitted to the ward
```

**Acceptance Criteria:**
- [ ] I can view all approved admissions awaiting bed allocation
- [ ] I can select an admission to process
- [ ] System shows available beds filtered by ward type preference
- [ ] I can see bed details: Ward, Room, Bed number, Room type, Rate
- [ ] I can filter available beds by ward, room type, floor
- [ ] I can see real-time bed availability (green = available)
- [ ] I can select a specific bed
- [ ] I can set expected admission date/time (default: now)
- [ ] I can assign attending doctor
- [ ] I can add admission notes (optional)
- [ ] System prevents double-booking (bed locked during allocation)
- [ ] On confirmation, bed status changes to "Occupied"
- [ ] Admission status changes to "Admitted"
- [ ] Patient appears in ward dashboard
- [ ] Ward nurse is notified of new admission

**Priority:** P0  
**Story Points:** 8

---

#### US-NUR-004: Process Emergency Admission

**Story:**
```
As a Nurse
I want to quickly admit emergency patients
So that critical patients receive immediate care
```

**Acceptance Criteria:**
- [ ] I can click "Emergency Admission" button
- [ ] I can search for existing patient or register new patient
- [ ] I can enter emergency reason (required)
- [ ] I can enter provisional diagnosis (required)
- [ ] System auto-selects Emergency Ward (can override)
- [ ] Urgency is auto-set to "Emergency"
- [ ] I can assign admitting doctor
- [ ] Admission request is auto-approved (skips approval step)
- [ ] I can immediately allocate bed
- [ ] System prioritizes emergency beds
- [ ] Entire process takes < 2 minutes
- [ ] Doctor is notified immediately

**Priority:** P0  
**Story Points:** 5

---

#### US-NUR-005: View Bed Availability Dashboard

**Story:**
```
As a Nurse
I want to see real-time bed availability across all wards
So that I can quickly find available beds for admissions
```

**Acceptance Criteria:**
- [ ] I can see a visual bed status board
- [ ] Board shows all wards with bed counts (Available/Occupied/Total)
- [ ] I can click on a ward to see room-wise breakdown
- [ ] I can click on a room to see individual bed status
- [ ] Beds are color-coded: Green (Available), Red (Occupied), Yellow (Reserved), Gray (Maintenance)
- [ ] Hovering on occupied bed shows patient name and admission date
- [ ] I can filter by ward type, floor, availability
- [ ] Dashboard updates in real-time (< 2 seconds)
- [ ] I can click on available bed to start allocation

**Priority:** P1  
**Story Points:** 5

---

#### US-NUR-006: View Ward Patient List

**Story:**
```
As a Nurse
I want to view all patients in my ward
So that I can manage patient care efficiently
```

**Acceptance Criteria:**
- [ ] I can see a list of all patients in my assigned ward
- [ ] List shows: Bed number, Patient name, Age/Gender, Admission date, Days admitted, Diagnosis, Attending doctor
- [ ] I can filter by room
- [ ] I can search by patient name, UHID, or bed number
- [ ] I can sort by bed number, admission date, patient name
- [ ] Critical patients are highlighted in red
- [ ] I can click on a patient to view full details
- [ ] List updates in real-time when patients are admitted/discharged/transferred
- [ ] I can see patient count at the top (e.g., "32/40 beds occupied")

**Priority:** P0  
**Story Points:** 3

---

#### US-NUR-007: View Patient Details

**Story:**
```
As a Nurse
I want to view detailed information about a patient
So that I can provide appropriate care
```

**Acceptance Criteria:**
- [ ] I can click on a patient to open detail view
- [ ] I can see patient demographics (Name, Age, Gender, UHID)
- [ ] I can see current location (Ward, Room, Bed)
- [ ] I can see admission details (Date, Reason, Diagnosis)
- [ ] I can see current vitals and vital trends
- [ ] I can see current medication orders
- [ ] I can see diet and activity orders
- [ ] I can see attending doctor and contact info
- [ ] I can see allergies (highlighted prominently)
- [ ] I can see recent lab results
- [ ] I can view admission form
- [ ] I can add nursing notes (if permitted)

**Priority:** P0  
**Story Points:** 5

---

#### US-NUR-008: Transfer Patient to Another Bed

**Story:**
```
As a Nurse
I want to transfer a patient to a different bed
So that bed changes can be managed efficiently
```

**Acceptance Criteria:**
- [ ] I can select a patient from ward list
- [ ] I can click "Transfer Bed" button
- [ ] For intra-ward transfer, I can see available beds in same ward
- [ ] For inter-ward transfer, I can select target ward first
- [ ] I can view available beds in target ward
- [ ] I can select new bed
- [ ] I must provide transfer reason (required)
- [ ] Inter-ward transfer requires doctor approval (notification sent)
- [ ] On confirmation, patient location is updated
- [ ] Old bed becomes available
- [ ] New bed becomes occupied
- [ ] Transfer is logged in patient history
- [ ] Ward dashboards update in real-time

**Priority:** P1  
**Story Points:** 5

---

### 3.3 Reception Staff

#### US-REC-001: View Admitted Patients for Billing

**Story:**
```
As Reception Staff
I want to view all admitted patients with pending bills
So that I can manage patient billing
```

**Acceptance Criteria:**
- [ ] I can see a list of all currently admitted patients
- [ ] List shows: Patient name, UHID, Ward/Room/Bed, Admission date, Days admitted, Current charges, Deposit
- [ ] I can filter by ward, doctor, admission date range
- [ ] I can search by patient name or UHID
- [ ] I can see patients with discharge pending separately
- [ ] I can click on a patient to view detailed billing
- [ ] I can see color indicators: Green (deposit sufficient), Yellow (low deposit), Red (negative balance)

**Priority:** P0  
**Story Points:** 3

---

#### US-REC-002: View Patient Bill Details

**Story:**
```
As Reception Staff
I want to view detailed billing for an admitted patient
So that I can track all charges
```

**Acceptance Criteria:**
- [ ] I can view complete bill breakdown:
  - Admission charges (one-time)
  - Room rent (per day, with dates)
  - Doctor consultation charges
  - Investigation charges (labs, imaging)
  - Procedure charges
  - Medication charges
  - Other service charges
- [ ] I can see total charges to date
- [ ] I can see deposit amount
- [ ] I can see balance (deposit - charges)
- [ ] I can see daily charge breakdown
- [ ] Room rent is auto-calculated based on days admitted
- [ ] All charges are itemized with date, description, quantity, rate, amount
- [ ] I can add manual charges (with authorization)
- [ ] I can apply discounts (with reason and authorization)

**Priority:** P0  
**Story Points:** 5

---

#### US-REC-003: Collect Deposit

**Story:**
```
As Reception Staff
I want to collect deposit from patient/relatives
So that advance payment is secured
```

**Acceptance Criteria:**
- [ ] I can select a patient
- [ ] I can click "Collect Deposit" button
- [ ] I can enter deposit amount (required)
- [ ] I can select payment mode (Cash/Card/UPI/Cheque)
- [ ] For card/UPI, I can enter transaction reference
- [ ] For cheque, I can enter cheque number, bank, date
- [ ] I can add remarks (optional)
- [ ] System generates deposit receipt (PDF)
- [ ] Receipt includes: Receipt number, Date, Patient details, Amount, Payment mode, Received by
- [ ] Deposit is added to patient's account
- [ ] Balance is updated
- [ ] I can collect multiple deposits during stay

**Priority:** P0  
**Story Points:** 5

---

#### US-REC-004: Process Discharge Billing

**Story:**
```
As Reception Staff
I want to settle final bill at discharge
So that patient can be discharged after payment
```

**Acceptance Criteria:**
- [ ] I can view patients with "Discharge Pending" status
- [ ] I can select a patient to process discharge billing
- [ ] System auto-calculates final bill:
  - All charges up to discharge date/time
  - Room rent (pro-rata for partial days)
  - All services rendered
- [ ] I can see total charges
- [ ] I can see total deposits
- [ ] I can see balance due or refund amount
- [ ] I can apply final discounts (with authorization)
- [ ] I can collect balance payment (if due)
- [ ] I can process refund (if excess deposit)
- [ ] System generates final invoice (PDF)
- [ ] System generates discharge receipt (PDF)
- [ ] On payment confirmation, discharge is marked complete
- [ ] Bed is released (after cleaning buffer)
- [ ] Patient is notified that discharge is complete

**Priority:** P0  
**Story Points:** 8

---

### 3.4 Admin

#### US-ADMIN-001: Configure Wards

**Story:**
```
As an Admin
I want to configure hospital wards
So that the system reflects actual hospital structure
```

**Acceptance Criteria:**
- [ ] I can access "Ward Management" in admin settings
- [ ] I can click "Add Ward" button
- [ ] I can enter ward details:
  - Ward name (required, unique)
  - Ward code (required, unique, 3-5 chars)
  - Ward type (General/Private/ICU/Emergency/Pediatric/Maternity)
  - Floor number (required)
  - Department (required)
  - Total capacity (required)
  - Gender restriction (Male/Female/Mixed)
- [ ] I can mark ward as active/inactive
- [ ] I can edit existing ward details
- [ ] I cannot delete ward with active admissions
- [ ] I can view list of all wards
- [ ] Changes reflect immediately in bed allocation screens

**Priority:** P0  
**Story Points:** 3

---

#### US-ADMIN-002: Configure Rooms

**Story:**
```
As an Admin
I want to configure rooms within wards
So that room structure is accurately represented
```

**Acceptance Criteria:**
- [ ] I can access "Room Management" in admin settings
- [ ] I can click "Add Room" button
- [ ] I can enter room details:
  - Room number (required, unique within ward)
  - Ward (required, dropdown)
  - Room type (Single/Double/Triple/General/ICU)
  - Bed capacity (required, 1-10)
  - Room rate per day (required, currency)
  - Amenities (AC, TV, Attached Bath) (multi-select)
- [ ] I can mark room as active/inactive
- [ ] I can edit existing room details
- [ ] I cannot delete room with occupied beds
- [ ] I can view rooms filtered by ward
- [ ] Room rate is used for billing calculations

**Priority:** P0  
**Story Points:** 3

---

#### US-ADMIN-003: Configure Beds

**Story:**
```
As an Admin
I want to configure individual beds
So that each bed can be tracked and allocated
```

**Acceptance Criteria:**
- [ ] I can access "Bed Management" in admin settings
- [ ] I can click "Add Bed" button
- [ ] I can enter bed details:
  - Bed number (required, unique within room)
  - Room (required, dropdown)
  - Bed type (Standard/Electric/ICU/Pediatric)
  - Status (Available/Under Maintenance)
- [ ] I can mark bed as active/inactive
- [ ] I can edit existing bed details
- [ ] I cannot delete occupied bed
- [ ] I can mark bed as "Under Maintenance" (makes it unavailable)
- [ ] I can bulk-add beds for a room
- [ ] Changes reflect immediately in bed availability screens

**Priority:** P0  
**Story Points:** 3

---

#### US-ADMIN-004: View Admission Reports

**Story:**
```
As an Admin
I want to view admission statistics and reports
So that I can monitor hospital operations
```

**Acceptance Criteria:**
- [ ] I can access "Admission Reports" dashboard
- [ ] I can see key metrics:
  - Total admissions today/this week/this month
  - Current occupancy rate (%)
  - Average length of stay (days)
  - Ward-wise occupancy
  - Doctor-wise admissions
- [ ] I can view admission trend chart (last 30 days)
- [ ] I can view discharge trend chart
- [ ] I can filter by date range, ward, doctor
- [ ] I can export reports to PDF/Excel
- [ ] Reports update in real-time

**Priority:** P1  
**Story Points:** 5

---

## 4. Epic Summary

| Epic | User Stories | Total Story Points | Priority |
|------|--------------|-------------------|----------|
| Admission Request & Approval | US-DOC-001, US-ADM-001, US-ADM-002 | 13 | P0 |
| Bed Management | US-ADM-003, US-ADM-004, US-ADM-005 | 18 | P0 |
| Ward Patient Management | US-DOC-002, US-NUR-001, US-NUR-002 | 11 | P0 |
| Patient Transfer | US-NUR-003 | 5 | P1 |
| Discharge Process | US-DOC-003 | 8 | P0 |
| Billing Integration | US-BILL-001, US-BILL-002, US-BILL-003, US-BILL-004 | 21 | P0 |
| Admin Configuration | US-ADMIN-001, US-ADMIN-002, US-ADMIN-003 | 9 | P0 |
| Reporting | US-ADMIN-004 | 5 | P1 |
| **Total** | **22 User Stories** | **90 Story Points** | |

---

## 5. Sprint Planning Recommendation

### Sprint 1 (Week 1-2): Foundation - 20 points
- US-ADMIN-001: Configure Wards (3 pts)
- US-ADMIN-002: Configure Rooms (3 pts)
- US-ADMIN-003: Configure Beds (3 pts)
- US-DOC-001: Request Patient Admission (5 pts)
- US-ADM-001: View Admission Request Queue (3 pts)
- US-ADM-002: Approve/Reject Admission Request (5 pts)

### Sprint 2 (Week 3-4): Bed Allocation - 22 points
- US-ADM-003: Allocate Bed to Patient (8 pts)
- US-ADM-004: Process Emergency Admission (5 pts)
- US-ADM-005: View Bed Availability Dashboard (5 pts)
- US-DOC-002: View My Admitted Patients (3 pts)

### Sprint 3 (Week 5-6): Ward Management & Billing - 24 points
- US-NUR-001: View Ward Patient List (3 pts)
- US-NUR-002: View Patient Details (5 pts)
- US-BILL-001: View Admitted Patients for Billing (3 pts)
- US-BILL-002: View Patient Bill Details (5 pts)
- US-BILL-003: Collect Deposit (5 pts)
- US-DOC-003: Initiate Patient Discharge (8 pts) - Start

### Sprint 4 (Week 7-8): Discharge & Polish - 19 points
- US-DOC-003: Initiate Patient Discharge (remaining)
- US-BILL-004: Process Discharge Billing (8 pts)
- US-NUR-003: Transfer Patient to Another Bed (5 pts)
- US-ADMIN-004: View Admission Reports (5 pts)
- Bug fixes & refinements

---

## 6. Definition of Done

A user story is considered "Done" when:

- [ ] All acceptance criteria are met
- [ ] Code is written and peer-reviewed
- [ ] Unit tests written and passing (>80% coverage)
- [ ] Integration tests passing
- [ ] UI matches design specifications
- [ ] Responsive design tested (desktop + tablet)
- [ ] No critical or high-priority bugs
- [ ] Documentation updated
- [ ] Deployed to staging environment
- [ ] Product Owner acceptance obtained

---

## 7. Testing Notes

### 7.1 Test Users Required

| Role | Count | Purpose |
|------|-------|---------|
| OPD Doctor | 2 | Create admission requests |
| Consultant | 1 | Approve complex admissions |
| Admission Clerk | 2 | Process admissions, allocate beds |
| Ward Nurse | 3 | Manage different wards |
| Billing Staff | 2 | Handle billing & deposits |
| Admin | 1 | Configure master data |

### 7.2 Test Data Required

- 5 wards (General, Private, ICU, Emergency, Maternity)
- 20 rooms across wards
- 50 beds across rooms
- 30 test patients
- 10 active admissions
- Sample admission requests (various urgency levels)

---

## 8. Dependencies & Risks

### 8.1 Dependencies

| Story | Depends On | Reason |
|-------|-----------|--------|
| US-ADM-003 | US-ADMIN-001, US-ADMIN-002, US-ADMIN-003 | Need ward/room/bed data |
| US-BILL-002 | US-ADM-003 | Need admitted patients |
| US-DOC-003 | US-ADM-003 | Need admitted patients |
| US-BILL-004 | US-DOC-003 | Need discharge initiated |

### 8.2 Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| Complex bed allocation logic | High | Early prototyping, stakeholder demos |
| Real-time sync issues | Medium | Use WebSocket, thorough testing |
| Billing calculation errors | High | Extensive test cases, manual verification |
| User adoption resistance | Medium | Training, phased rollout |

---

> **Note:** This document will be updated as user stories are refined during sprint planning and backlog grooming sessions.
