# 📋 Phase 2: IPD Admission - Feature Requirements

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document provides **detailed functional requirements** for the IPD (Inpatient Department) Admission Module, defining what needs to be built, how it should work, and acceptance criteria.

---

## 2. Feature Overview

### 2.1 Module Scope

The IPD Admission Module enables:
- Admission request creation from OPD/Emergency
- Bed availability management
- Patient admission processing
- Ward-wise patient tracking
- Bed transfers
- Discharge workflow
- Admission billing integration

### 2.2 In-Scope (Phase 2)

| Feature | Description | Priority |
|---------|-------------|----------|
| Admission Request | Create admission from consultation | P0 |
| Bed Management | Real-time bed availability & allocation | P0 |
| Admission Form | Complete admission documentation | P0 |
| Ward Dashboard | View all admitted patients | P0 |
| Patient Transfer | Move between beds/wards | P1 |
| Discharge Process | Structured discharge workflow | P0 |
| Admission Billing | Admission charges & room rent | P0 |
| Admission History | View past admissions | P1 |
| Bed Status Board | Visual bed occupancy | P1 |

### 2.3 Out-of-Scope (Phase 2)

| Feature | Reason | Future Phase |
|---------|--------|--------------|
| Nursing Care Plans | Complex clinical module | Phase 3 |
| ICU Monitoring | Specialized equipment integration | Phase 3 |
| Surgery Scheduling | Separate OT module | Phase 3 |
| Inventory for IPD | Requires full inventory system | Phase 3 |
| Insurance/TPA Claims | Complex integration | Phase 4 |

---

## 3. Detailed Feature Requirements

### 3.1 Admission Request Creation

#### 3.1.1 Functional Requirements

**FR-ADM-001: Create Admission Request from OPD**
- **Description:** Doctor can create admission request during consultation
- **Trigger:** Doctor selects "Admission Required" decision type
- **Input Fields:**
  - Admission reason (required, text)
  - Provisional diagnosis (required, text)
  - Ward type preference (General/Private/ICU) (required)
  - Urgency level (Routine/Urgent/Emergency) (required)
  - Estimated stay duration (days) (required)
  - Admission orders (required, rich text)
  - Special requirements (optional, text)
  - Diet instructions (optional, text)
- **Business Rules:**
  - Must have active visit/consultation
  - Only doctors with `admission:create` permission can create
  - Admission reason must be at least 20 characters
  - Admission orders must be at least 50 characters
- **Output:** Admission request created with status `PENDING`
- **Success Criteria:** Admission request visible in admission queue

**FR-ADM-002: Create Emergency Admission**
- **Description:** Nurse can create direct emergency admission
- **Trigger:** Emergency patient requires immediate admission
- **Input Fields:**
  - Patient selection (existing or new registration)
  - Emergency reason (required)
  - Provisional diagnosis (required)
  - Ward type (default: Emergency Ward)
  - Urgency: Emergency (fixed)
  - Admitting doctor (required)
- **Business Rules:**
  - Can skip OPD consultation
  - Auto-creates visit record
  - Priority: Highest
- **Output:** Admission request with status `APPROVED` (auto-approved)
- **Success Criteria:** Patient immediately available for bed allocation

**FR-ADM-003: Admission Request Approval**
- **Description:** Nurse can approve/reject admission requests
- **Trigger:** Pending admission request review
- **Actions:**
  - Approve: Move to bed allocation queue
  - Reject: Provide rejection reason, notify requesting doctor
  - Request More Info: Send back with comments
- **Business Rules:**
  - Only users with `admission:approve` permission
  - Rejection reason required if rejected
  - Notification sent to requesting doctor
- **Output:** Status changed to `APPROVED`, `REJECTED`, or `INFO_REQUESTED`

---

### 3.2 Bed Management System

#### 3.2.1 Master Data Setup

**FR-BED-001: Ward Configuration**
- **Description:** Admin can configure hospital wards
- **Fields:**
  - Ward name (required, unique)
  - Ward code (required, unique, 3-5 chars)
  - Ward type (General/Private/ICU/Emergency/Pediatric/Maternity)
  - Floor number (required)
  - Department (required)
  - Total capacity (required)
  - Gender restriction (Male/Female/Mixed)
  - Active status (boolean)
- **Business Rules:**
  - Ward code must be uppercase
  - Cannot delete ward with active admissions
- **Success Criteria:** Ward appears in bed allocation dropdown

**FR-BED-002: Room Configuration**
- **Description:** Admin can configure rooms within wards
- **Fields:**
  - Room number (required, unique within ward)
  - Ward (required, dropdown)
  - Room type (Single/Double/Triple/General/ICU)
  - Bed capacity (required, 1-10)
  - Room rate per day (required, currency)
  - Amenities (AC, TV, Attached Bath) (multi-select)
  - Active status (boolean)
- **Business Rules:**
  - Room number unique within ward
  - Cannot delete room with occupied beds
- **Success Criteria:** Room appears in bed selection

**FR-BED-003: Bed Configuration**
- **Description:** Admin can configure individual beds
- **Fields:**
  - Bed number (required, unique within room)
  - Room (required, dropdown)
  - Bed type (Standard/Electric/ICU/Pediatric)
  - Status (Available/Occupied/Under Maintenance/Reserved)
  - Active status (boolean)
- **Business Rules:**
  - Bed number unique within room
  - Cannot delete occupied bed
- **Success Criteria:** Bed available for allocation

#### 3.2.2 Bed Availability & Allocation

**FR-BED-004: Real-time Bed Availability**
- **Description:** System shows real-time bed availability
- **Display:**
  - Ward-wise bed count (Available/Occupied/Total)
  - Room-wise bed status
  - Visual bed status board
  - Filter by ward type, gender, room type
- **Business Rules:**
  - Updates in real-time when bed allocated/released
  - Shows only active beds
  - Respects gender restrictions
- **Success Criteria:** Accurate bed counts, updates within 2 seconds

**FR-BED-005: Bed Allocation**
- **Description:** Nurse allocates bed to approved admission
- **Trigger:** Approved admission request
- **Process:**
  1. Select admission request from queue
  2. View available beds (filtered by ward type preference)
  3. Select specific bed
  4. Confirm allocation
- **Input Fields:**
  - Expected admission date/time (default: now)
  - Attending doctor (required)
  - Admission notes (optional)
- **Business Rules:**
  - Bed must be available
  - Ward type should match request preference
  - Gender restriction must be respected
  - Bed locked during allocation (prevent double booking)
- **Output:** 
  - Admission status: `ADMITTED`
  - Bed status: `OCCUPIED`
  - Admission record created
- **Success Criteria:** Patient appears in ward dashboard, bed marked occupied

**FR-BED-006: Bed Reservation**
- **Description:** Reserve bed for planned admission
- **Use Case:** Scheduled surgery, planned admission
- **Input Fields:**
  - Admission request
  - Bed selection
  - Reservation from date/time
  - Reservation to date/time
  - Reservation reason
- **Business Rules:**
  - Bed status: `RESERVED`
  - Cannot allocate reserved bed to others during reservation period
  - Auto-release if patient not admitted by reservation end time
- **Success Criteria:** Bed reserved, visible in bed board

---

### 3.3 Admission Form & Documentation

**FR-DOC-001: Admission Form**
- **Description:** Complete admission documentation form
- **Sections:**

**Section 1: Patient Information** (Auto-populated)
- UHID, Name, Age, Gender
- Contact details
- Emergency contact
- Insurance details (if any)

**Section 2: Admission Details**
- Admission date/time (auto)
- Admission type (Planned/Emergency/Transfer)
- Admission source (OPD/Emergency/Direct)
- Referring doctor
- Admitting doctor
- Ward, Room, Bed number (auto)

**Section 3: Clinical Information**
- Chief complaint
- Provisional diagnosis
- Medical history (auto-populated from patient record)
- Current medications
- Allergies (auto-populated, highlight)
- Vitals at admission (BP, Pulse, Temp, SpO2, Weight)

**Section 4: Admission Orders**
- Diet orders (NPO/Liquid/Soft/Regular/Diabetic/Custom)
- Activity level (Bed rest/Bathroom privileges/Ambulate/As tolerated)
- Monitoring requirements (Vitals frequency, I/O chart, etc.)
- Medication orders
- Investigation orders
- Special instructions

**Section 5: Consent & Documentation**
- Admission consent (checkbox)
- Treatment consent (checkbox)
- Financial consent (checkbox)
- Consent form upload (PDF)
- ID proof upload (optional)

- **Business Rules:**
  - All required fields must be filled
  - Vitals must be recorded
  - At least one admission order required
  - Consent checkboxes mandatory
- **Success Criteria:** Admission form saved, printable

**FR-DOC-002: Admission Summary Print**
- **Description:** Generate printable admission summary
- **Content:**
  - Patient demographics
  - Admission details
  - Clinical summary
  - Admission orders
  - Attending physician signature
- **Format:** PDF, A4 size
- **Success Criteria:** PDF generated within 5 seconds

---

### 3.4 Ward Patient Dashboard

**FR-DASH-001: Ward Patient List**
- **Description:** View all patients in a ward
- **Display:**
  - Table view with columns:
    - Bed Number
    - Patient Name (UHID)
    - Age/Gender
    - Admission Date
    - Days Admitted
    - Provisional Diagnosis
    - Attending Doctor
    - Status (Admitted/Discharge Pending)
  - Filter by: Ward, Room, Doctor, Status
  - Sort by: Bed Number, Admission Date, Patient Name
  - Search by: Patient name, UHID, Bed number
- **Actions:**
  - View patient details
  - View admission form
  - Transfer bed
  - Initiate discharge
- **Business Rules:**
  - Only show patients in selected ward
  - Color code: Red (critical), Yellow (review needed), Green (stable)
- **Success Criteria:** All admitted patients visible, loads < 3 seconds

**FR-DASH-002: Patient Detail View**
- **Description:** Detailed view of admitted patient
- **Tabs:**
  - **Overview:** Demographics, admission details, current location
  - **Clinical:** Diagnosis, vitals trend, current orders
  - **Medications:** Current medications, administration log
  - **Investigations:** Lab orders, reports
  - **Billing:** Charges summary, pending amount
  - **Notes:** Doctor notes, nursing notes
  - **History:** Previous admissions, medical history
- **Actions:**
  - Update clinical notes
  - Add orders
  - View reports
  - Print summary
- **Success Criteria:** All patient data accessible in one view

**FR-DASH-003: Bed Status Board**
- **Description:** Visual bed occupancy board
- **Display:**
  - Grid layout: Wards → Rooms → Beds
  - Color coding:
    - Green: Available
    - Red: Occupied
    - Yellow: Reserved
    - Gray: Under Maintenance
  - Hover: Show patient name, admission date
  - Click: View patient details
- **Filters:**
  - Ward type
  - Floor
  - Availability status
- **Success Criteria:** Visual representation, real-time updates

---

### 3.5 Bed Transfer

**FR-TRANS-001: Intra-Ward Transfer**
- **Description:** Transfer patient to different bed within same ward
- **Trigger:** Bed change needed (patient request, clinical reason)
- **Process:**
  1. Select patient
  2. View available beds in same ward
  3. Select new bed
  4. Provide transfer reason
  5. Confirm transfer
- **Business Rules:**
  - New bed must be available
  - Transfer reason required
  - Transfer logged in patient history
  - Billing: No additional charge
- **Output:** Patient moved to new bed, old bed available
- **Success Criteria:** Patient location updated, visible in dashboard

**FR-TRANS-002: Inter-Ward Transfer**
- **Description:** Transfer patient to different ward
- **Trigger:** Clinical condition change (e.g., ICU to General)
- **Process:**
  1. Select patient
  2. Select target ward
  3. View available beds in target ward
  4. Select new bed
  5. Provide transfer reason (required, detailed)
  6. Confirm transfer
- **Business Rules:**
  - Transfer reason mandatory (min 50 chars)
  - Requires doctor approval
  - Billing: Room rate changes from transfer time
  - Transfer logged with timestamp
- **Output:** Patient moved to new ward/bed
- **Success Criteria:** Patient visible in new ward dashboard

**FR-TRANS-003: Transfer History**
- **Description:** View complete transfer history
- **Display:**
  - Transfer date/time
  - From: Ward, Room, Bed
  - To: Ward, Room, Bed
  - Transfer reason
  - Transferred by (user)
  - Approved by (doctor)
- **Success Criteria:** Complete audit trail of transfers

---

### 3.6 Discharge Process

**FR-DISC-001: Initiate Discharge**
- **Description:** Doctor initiates discharge process
- **Trigger:** Patient ready for discharge
- **Input Fields:**
  - Discharge date/time (default: now)
  - Discharge type (Regular/LAMA/Absconded/Death/Transfer)
  - Discharge diagnosis (required)
  - Discharge summary (required, rich text)
  - Follow-up instructions (required)
  - Follow-up date (optional)
  - Medications on discharge (prescription)
  - Diet advice (text)
  - Activity restrictions (text)
  - Warning signs (text)
- **Business Rules:**
  - Only attending doctor or consultant can initiate
  - Discharge summary min 100 characters
  - Status changes to `DISCHARGE_PENDING`
  - Patient still occupies bed until billing cleared
- **Output:** Discharge request created
- **Success Criteria:** Discharge summary saved, printable

**FR-DISC-002: Discharge Summary Print**
- **Description:** Generate discharge summary document
- **Content:**
  - Patient demographics
  - Admission details (date, reason)
  - Hospital course (summary of stay)
  - Final diagnosis
  - Procedures performed
  - Investigations summary
  - Medications on discharge
  - Follow-up instructions
  - Doctor signature
- **Format:** PDF, A4 size, hospital letterhead
- **Success Criteria:** Professional discharge summary PDF

**FR-DISC-003: Billing Clearance**
- **Description:** Clear all pending bills before discharge
- **Process:**
  1. System calculates total charges:
     - Room rent (per day × days admitted)
     - Admission charges (one-time)
     - Consultations
     - Investigations
     - Procedures
     - Medications
     - Other services
  2. Generate final invoice
  3. Collect payment or adjust against deposit
  4. Generate receipt
- **Business Rules:**
  - Cannot discharge with pending bills (unless authorized)
  - Partial payment allowed with approval
  - Deposit adjustment automatic
- **Output:** Final bill settled
- **Success Criteria:** Payment recorded, receipt generated

**FR-DISC-004: Complete Discharge**
- **Description:** Final discharge and bed release
- **Trigger:** Billing cleared
- **Process:**
  1. Verify billing clearance
  2. Verify discharge summary signed
  3. Mark discharge complete
  4. Release bed
  5. Update patient status
- **Business Rules:**
  - Bed status: `AVAILABLE` (after 1-hour cleaning buffer)
  - Admission status: `DISCHARGED`
  - Discharge time recorded
- **Output:** Patient discharged, bed available
- **Success Criteria:** Patient removed from ward dashboard, bed available

**FR-DISC-005: LAMA (Leave Against Medical Advice)**
- **Description:** Handle patient leaving against medical advice
- **Process:**
  1. Doctor initiates LAMA discharge
  2. LAMA form generated
  3. Patient/relative signature required
  4. Billing clearance (mandatory)
  5. Complete discharge
- **Business Rules:**
  - LAMA form mandatory
  - Signature required (digital or scanned)
  - Full billing clearance required
  - Cannot be reversed
- **Success Criteria:** LAMA documented, patient discharged

---

### 3.7 Admission Billing

**FR-BILL-001: Admission Charges**
- **Description:** Auto-generate admission charges
- **Trigger:** Patient admitted
- **Charges:**
  - Admission fee (one-time, configurable)
  - Room rent (daily, based on room type)
  - Nursing charges (daily, optional)
  - Doctor visit charges (per visit)
- **Business Rules:**
  - Admission fee charged once
  - Room rent calculated daily (pro-rata for partial days)
  - Auto-calculate on discharge
- **Success Criteria:** Charges auto-added to patient bill

**FR-BILL-002: Daily Room Rent Calculation**
- **Description:** Calculate room rent for each day
- **Logic:**
  - Day 1: Full day charge (regardless of admission time)
  - Day 2-N: Full day charge
  - Discharge day: Full day charge (regardless of discharge time)
  - Transfer: Calculate based on room rates
- **Business Rules:**
  - Room rate from room master
  - Transfer: Pro-rata calculation
  - Minimum: 1 day charge
- **Success Criteria:** Accurate room rent calculation

**FR-BILL-003: Deposit Management**
- **Description:** Reception manages admission deposits
- **Process:**
  1. Reception collects deposit at admission (optional)
  2. Record deposit amount
  3. Adjust against final bill at discharge
  4. Reception refunds excess or collects balance
- **Business Rules:**
  - Deposit amount configurable by ward type
  - Reception can collect multiple deposits during stay
  - Auto-adjust at discharge
- **Success Criteria:** Deposit tracked, adjusted correctly

**FR-BILL-004: Additional Service Charges**
- **Description:** Add services during admission
- **Services:**
  - Consultations (specialist visits)
  - Investigations (labs, imaging)
  - Procedures (minor procedures, dressings)
  - Medications (if dispensed)
  - Other services
- **Business Rules:**
  - Services added by authorized users
  - Prices from service master
  - Auto-added to patient bill
- **Success Criteria:** All services captured, billed correctly

---

### 3.8 Admission History

**FR-HIST-001: Patient Admission History**
- **Description:** View all past admissions of a patient
- **Display:**
  - Table view:
    - Admission date
    - Discharge date
    - Days admitted
    - Ward/Room/Bed
    - Admission diagnosis
    - Discharge diagnosis
    - Attending doctor
    - Status
  - Actions:
    - View admission details
    - View discharge summary
    - Print summary
- **Business Rules:**
  - Show all admissions (current + past)
  - Sort by admission date (latest first)
- **Success Criteria:** Complete admission history visible

**FR-HIST-002: Admission Detail View**
- **Description:** View complete details of past admission
- **Content:**
  - Admission form
  - Clinical notes
  - Investigations
  - Medications
  - Discharge summary
  - Billing summary
- **Success Criteria:** All admission data accessible

---

## 4. Non-Functional Requirements

### 4.1 Performance

| Requirement | Target | Measurement |
|-------------|--------|-------------|
| Page Load Time | < 2 seconds | Dashboard, forms |
| Bed Availability Update | < 2 seconds | Real-time sync |
| Report Generation | < 5 seconds | PDF generation |
| Concurrent Users | 50+ users | No performance degradation |
| Database Query Time | < 500ms | Complex queries |

### 4.2 Security

| Requirement | Description |
|-------------|-------------|
| Role-Based Access | Strict RBAC enforcement |
| Audit Trail | Log all admission actions |
| Data Encryption | Encrypt sensitive data at rest |
| Session Management | Auto-logout after 30 min inactivity |
| Password Policy | Strong password requirements |

### 4.3 Usability

| Requirement | Description |
|-------------|-------------|
| Mobile Responsive | Works on tablets (iPad) |
| Keyboard Navigation | Full keyboard support |
| Error Messages | Clear, actionable error messages |
| Form Validation | Real-time validation |
| Help Text | Contextual help for complex fields |

### 4.4 Reliability

| Requirement | Target |
|-------------|--------|
| System Uptime | 99.5% |
| Data Backup | Daily automated backups |
| Disaster Recovery | RTO: 4 hours, RPO: 1 hour |
| Error Rate | < 0.1% |

---

## 5. Acceptance Criteria

### 5.1 Feature Acceptance

Each feature is accepted when:
- ✅ All functional requirements implemented
- ✅ All business rules enforced
- ✅ Non-functional requirements met
- ✅ UAT test cases passed
- ✅ No critical or high-priority bugs
- ✅ Documentation complete

### 5.2 Module Acceptance

Module is accepted when:
- ✅ All features accepted
- ✅ Integration testing passed
- ✅ Performance testing passed
- ✅ Security audit passed
- ✅ User training completed
- ✅ Go-live checklist completed

---

## 6. Assumptions & Constraints

### 6.1 Assumptions

- Hospital has defined ward/room/bed structure
- Room rates are pre-configured
- Staff trained on basic system usage
- Network connectivity available in all wards
- Printers available for document printing

### 6.2 Constraints

- Must integrate with existing Phase 1 modules
- Cannot modify Phase 1 database schema significantly
- Must support existing user roles
- Limited budget for third-party integrations
- 8-week development timeline

---

## 7. Risks & Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Complex bed allocation logic | High | Medium | Early prototyping, stakeholder review |
| Integration issues with billing | High | Medium | API-first development, early integration testing |
| User adoption resistance | Medium | High | Extensive training, phased rollout |
| Data migration from manual records | Medium | Medium | Parallel run, data validation |
| Performance with large patient volume | High | Low | Load testing, database optimization |

---

## 8. Future Enhancements (Phase 3+)

| Feature | Description | Phase |
|---------|-------------|-------|
| Nursing Care Plans | Detailed nursing documentation | Phase 3 |
| ICU Monitoring | Vital signs integration | Phase 3 |
| Surgery Scheduling | OT booking & management | Phase 3 |
| Inventory for IPD | Ward-wise stock management | Phase 3 |
| Mobile App | Ward rounds on mobile | Phase 4 |
| Analytics Dashboard | Admission trends, occupancy analytics | Phase 4 |

---

## 9. Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Product Owner | TBD | | |
| Clinical Lead | TBD | | |
| Tech Lead | TBD | | |
| QA Lead | TBD | | |

---

> **Document Status:** This is a living document and will be updated as requirements evolve. All changes must be approved through the change control process.
