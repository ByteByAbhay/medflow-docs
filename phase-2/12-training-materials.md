# 📚 Phase 2: IPD Admission - Training Materials

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document outlines the **training strategy and materials** for the IPD Admission Module, including training plans, user guides, and quick reference cards.

---

## 2. Training Overview

### 2.1 Training Objectives

By the end of training, users should be able to:
- Navigate the IPD module confidently
- Perform their role-specific tasks independently
- Understand the complete admission workflow
- Handle common scenarios and edge cases
- Know where to get help when needed

### 2.2 Training Approach

**Method:** Hands-on training with real system

**Format:**
- Role-based training sessions (2 hours each)
- Live demonstrations
- Hands-on practice with test data
- Q&A sessions
- Post-training support

---

## 3. Training Schedule

### 3.1 Training Sessions

| Session | Audience | Duration | Date/Time | Trainer |
|---------|----------|----------|-----------|---------|
| Session 1 | Doctors & Consultants | 2 hours | TBD | Product Owner |
| Session 2 | Admission Clerks | 2 hours | TBD | Tech Lead |
| Session 3 | Ward Nurses | 2 hours | TBD | Clinical Lead |
| Session 4 | Billing Staff | 2 hours | TBD | Billing Manager |
| Session 5 | Administrators | 1 hour | TBD | Tech Lead |

### 3.2 Training Environment

**System:** Staging environment with test data
**Access:** Provided 1 week before training
**Test Accounts:** Role-specific test accounts

---

## 4. Training Materials by Role

### 4.1 Doctors

#### 4.1.1 Training Agenda

**Duration:** 2 hours

**Topics:**
1. Introduction to IPD Module (10 min)
2. Creating Admission Requests (30 min)
   - From OPD consultation
   - Filling admission form
   - Setting urgency levels
   - Writing admission orders
3. Viewing Admitted Patients (20 min)
   - My patients dashboard
   - Patient details view
   - Accessing patient records
4. Initiating Discharge (40 min)
   - Discharge form walkthrough
   - Writing discharge summary
   - Prescribing discharge medications
   - Follow-up instructions
5. Hands-on Practice (15 min)
6. Q&A (5 min)

#### 4.1.2 User Guide: Creating Admission Request

**Step-by-Step Guide:**

```markdown
# How to Create an Admission Request

## Step 1: Complete Consultation
1. Navigate to patient's consultation page
2. Complete clinical notes and diagnosis

## Step 2: Select Admission Required
1. In the "Decision" section, select "Admission Required"
2. The admission request form will appear

## Step 3: Fill Admission Details

**Required Fields:**
- **Admission Reason** (min 20 characters)
  - Describe why the patient needs admission
  - Example: "Severe abdominal pain with fever for 3 days, suspected appendicitis"

- **Provisional Diagnosis**
  - Enter your working diagnosis
  - Example: "Acute Appendicitis"

- **Ward Type Preference**
  - Select: General / Private / ICU / Emergency
  - Choose based on patient's condition and preference

- **Urgency Level**
  - Routine: Can wait for bed availability
  - Urgent: Needs admission within 24 hours
  - Emergency: Needs immediate admission

- **Estimated Stay** (days)
  - Your best estimate
  - Example: 5 days

- **Admission Orders** (min 50 characters)
  - Detailed orders for nursing staff
  - Include: Diet, IV fluids, medications, monitoring, investigations
  - Example: "NPO, IV fluids NS @ 100ml/hr, IV antibiotics Ceftriaxone 1g BD, Pain management PRN, Vitals q4h, Surgical consult"

**Optional Fields:**
- **Special Requirements**
  - Any special needs (e.g., isolation, special equipment)
- **Diet Instructions**
  - Specific diet orders

## Step 4: Review and Submit
1. Review all entered information
2. Click "Submit Request"
3. You'll see a confirmation message
4. The admission clerk will be notified

## Step 5: Track Request Status
1. Go to "My Admission Requests"
2. View status: Pending / Approved / Rejected
3. You'll receive notification when status changes

## Tips:
✓ Be specific in admission reason and orders
✓ Set appropriate urgency level
✓ Include all relevant clinical information
✓ Mention any allergies or special requirements
```

#### 4.1.3 Quick Reference Card

```
┌─────────────────────────────────────────────────────┐
│ DOCTOR QUICK REFERENCE - IPD MODULE                 │
├─────────────────────────────────────────────────────┤
│                                                     │
│ CREATE ADMISSION REQUEST                            │
│ 1. Complete consultation                            │
│ 2. Select "Admission Required"                      │
│ 3. Fill form (reason, diagnosis, ward, urgency)     │
│ 4. Write admission orders                           │
│ 5. Submit                                           │
│                                                     │
│ VIEW MY PATIENTS                                    │
│ Navigation: Dashboard → My Admitted Patients        │
│ • View all patients under your care                 │
│ • Click patient to see details                      │
│                                                     │
│ INITIATE DISCHARGE                                  │
│ 1. Select patient                                   │
│ 2. Click "Initiate Discharge"                       │
│ 3. Fill discharge summary                           │
│ 4. Prescribe discharge medications                  │
│ 5. Add follow-up instructions                       │
│ 6. Submit                                           │
│                                                     │
│ NEED HELP?                                          │
│ • Helpdesk: support@medflow.com                     │
│ • Emergency: +1-XXX-XXX-XXXX                        │
│ • User Guide: medflow.com/docs/doctor               │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### 4.2 Nurses

#### 4.2.1 Training Agenda

**Duration:** 2 hours

**Topics:**
1. Introduction to IPD Module (10 min)
2. Managing Admission Queue (30 min)
   - Viewing pending requests
   - Prioritizing by urgency
   - Reviewing request details
   - Approving/rejecting requests
3. Bed Allocation (30 min)
   - Viewing bed availability
   - Filtering beds by ward/type
   - Selecting appropriate bed
   - Completing admission
4. Ward Patient Management (20 min)
   - Viewing ward patients
   - Patient transfers
   - Updating patient status
5. Hands-on Practice (15 min)
6. Q&A (5 min)

#### 4.2.2 User Guide: Bed Allocation

**Step-by-Step Guide:**

```markdown
# How to Allocate a Bed

## Step 1: Access Bed Allocation Queue
1. Navigate to "Admissions" → "Bed Allocation Queue"
2. You'll see all approved admission requests awaiting beds

## Step 2: Select Admission Request
1. Review the queue (sorted by urgency)
2. Click on a request to view details
3. Review:
   - Patient information
   - Admission reason and diagnosis
   - Ward preference
   - Urgency level
   - Admission orders

## Step 3: View Available Beds
1. Click "Allocate Bed"
2. System shows available beds filtered by ward preference
3. Use filters:
   - Ward: Select specific ward
   - Room Type: Single/Double/Triple
   - Floor: Select floor number

## Step 4: Select Bed
1. Review bed options:
   - Bed number (e.g., 201-A)
   - Room type and rate
   - Amenities (AC, TV, etc.)
2. Click "Select" on chosen bed
3. Bed details will appear in allocation form

## Step 5: Complete Admission Details
**Required Fields:**
- **Admission Date/Time** (default: now)
  - Can schedule for future if needed
- **Attending Doctor**
  - Select from dropdown
  - Usually the requesting doctor

**Optional Fields:**
- **Admission Notes**
  - Any additional notes

## Step 6: Confirm Allocation
1. Review all details
2. Click "Confirm Admission"
3. System will:
   - Create admission record
   - Mark bed as occupied
   - Notify ward nurse
   - Notify attending doctor

## Step 7: Verify
1. Patient should appear in ward dashboard
2. Bed should show as occupied in bed board

## Common Scenarios:

**Scenario 1: No beds in preferred ward**
- Check similar ward types
- Offer alternative to doctor
- Get approval before allocating

**Scenario 2: Bed becomes unavailable**
- System prevents double-booking
- Select another bed
- Refresh availability if needed

**Scenario 3: Emergency admission**
- Use "Emergency Admission" button
- Skip approval step
- Allocate bed immediately
```

#### 4.2.3 Quick Reference Card

```
┌─────────────────────────────────────────────────────┐
│ NURSE QUICK REFERENCE                               │
├─────────────────────────────────────────────────────┤
│                                                     │
│ APPROVE ADMISSION REQUEST                           │
│ 1. Go to Admission Queue                            │
│ 2. Select request                                   │
│ 3. Review details                                   │
│ 4. Click "Approve" or "Reject"                      │
│ 5. Add comments                                     │
│ 6. Confirm                                          │
│                                                     │
│ ALLOCATE BED                                        │
│ 1. Go to Bed Allocation Queue                       │
│ 2. Select approved admission                        │
│ 3. Click "Allocate Bed"                             │
│ 4. Filter and select bed                            │
│ 5. Set admission date/time                          │
│ 6. Assign attending doctor                          │
│ 7. Confirm allocation                               │
│                                                     │
│ EMERGENCY ADMISSION                                 │
│ 1. Click "Emergency Admission"                      │
│ 2. Search/register patient                          │
│ 3. Enter emergency details                          │
│ 4. Allocate bed immediately                         │
│                                                     │
│ PRIORITY ORDER                                      │
│ 🔴 Emergency → 🟡 Urgent → 🟢 Routine               │
│                                                     │
│ NEED HELP?                                          │
│ • Helpdesk: support@medflow.com                     │
│ • Emergency: +1-XXX-XXX-XXXX                        │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### 4.3 Reception Staff

#### 4.3.1 Training Agenda

**Duration:** 2 hours

**Topics:**
1. Introduction to IPD Billing (10 min)
2. Admission Fee Collection (30 min)
   - Collecting admission fees
   - Generating receipts
   - Payment modes
3. Deposit Management (30 min)
   - Collecting deposits
   - Tracking deposits
   - Generating deposit receipts
4. Discharge Billing (40 min)
   - Viewing final bill
   - Collecting payment
   - Processing refunds
   - Generating final receipt
5. Hands-on Practice (5 min)
6. Q&A (5 min)

#### 4.3.2 User Guide: Ward Patient Management

```markdown
# Ward Patient Management Guide

## Viewing Ward Patients

### Access Ward Dashboard
1. Navigate to "Wards" → "My Ward" (or select ward)
2. Dashboard shows:
   - Ward occupancy summary
   - List of all patients in ward
   - Patient status indicators

### Understanding Status Indicators
- 🔴 **Critical:** Requires immediate attention
- 🟡 **Review:** Needs review/monitoring
- 🟢 **Stable:** Stable condition
- 🟣 **Discharge Pending:** Awaiting discharge

### Patient List Columns
- **Bed Number:** Patient's current bed
- **Patient Name & UHID:** Patient identification
- **Age/Sex:** Demographics
- **Admission Date:** When admitted
- **Days Admitted:** Length of stay
- **Diagnosis:** Current diagnosis
- **Attending Doctor:** Responsible doctor

### Search and Filter
- **Search:** Enter patient name, UHID, or bed number
- **Filter by Room:** Show patients in specific room
- **Filter by Status:** Show only critical/stable patients
- **Sort:** Click column headers to sort

## Viewing Patient Details

### Access Patient Details
1. Click on patient row in ward dashboard
2. Patient detail page opens

### Patient Detail Tabs
1. **Overview:**
   - Patient demographics
   - Current location (ward, room, bed)
   - Admission details
   - Attending doctor

2. **Clinical:**
   - Diagnosis
   - Current vitals
   - Allergies (highlighted)
   - Current medications
   - Admission orders

3. **Medications:**
   - All prescribed medications
   - Dosage and frequency
   - Administration schedule

4. **Investigations:**
   - Lab orders
   - Lab reports
   - Imaging results

5. **Billing:**
   - Current charges
   - Deposits
   - Balance

### Quick Actions
- **Transfer Bed:** Move patient to different bed
- **View History:** See admission history
- **Print Summary:** Print patient summary

## Tips for Nurses
✓ Check critical patients first
✓ Review admission orders for new admissions
✓ Update patient status as needed
✓ Report issues to attending doctor
✓ Keep patient information confidential
```

#### 4.3.3 Quick Reference Card

```
┌─────────────────────────────────────────────────────┐
│ WARD NURSE QUICK REFERENCE                          │
├─────────────────────────────────────────────────────┤
│                                                     │
│ VIEW WARD PATIENTS                                  │
│ Navigation: Wards → My Ward                         │
│ • See all patients in your ward                     │
│ • Check status indicators                           │
│ • Use search/filter to find patients                │
│                                                     │
│ VIEW PATIENT DETAILS                                │
│ 1. Click on patient in ward list                    │
│ 2. View tabs: Overview, Clinical, Meds, Labs        │
│ 3. Check allergies (highlighted)                    │
│ 4. Review admission orders                          │
│                                                     │
│ TRANSFER PATIENT                                    │
│ 1. Open patient details                             │
│ 2. Click "Transfer Bed"                             │
│ 3. Select new bed                                   │
│ 4. Enter transfer reason                            │
│ 5. Confirm (inter-ward needs doctor approval)       │
│                                                     │
│ STATUS INDICATORS                                   │
│ 🔴 Critical  🟡 Review  🟢 Stable  🟣 Discharge     │
│                                                     │
│ NEED HELP?                                          │
│ • Helpdesk: support@medflow.com                     │
│ • User Guide: medflow.com/docs/nurse                │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### 4.4 Billing Staff

#### 4.4.1 Training Agenda

**Duration:** 2 hours

**Topics:**
1. Introduction to IPD Billing (10 min)
2. Viewing Admission Charges (30 min)
   - Auto-generated charges
   - Manual charge entry
   - Charge categories
3. Deposit Management (30 min)
   - Collecting deposits
   - Generating receipts
   - Tracking deposits
4. Discharge Billing (40 min)
   - Generating final bill
   - Collecting payment
   - Processing refunds
   - Completing discharge
5. Hands-on Practice (5 min)
6. Q&A (5 min)

#### 4.4.2 User Guide: Discharge Billing

```markdown
# Discharge Billing Process

## Step 1: View Discharge Pending Patients
1. Navigate to "Billing" → "Discharge Pending"
2. List shows all patients awaiting billing clearance

## Step 2: Select Patient
1. Click on patient to view billing details
2. Review:
   - All charges
   - All deposits
   - Current balance

## Step 3: Review Charges
**Auto-Generated Charges:**
- Admission fee (added on admission)
- Room rent (added daily)
- Nursing charges (if applicable)

**Manual Charges:**
- Consultations
- Investigations (labs, imaging)
- Procedures
- Medications

**Verify:**
- All services rendered are billed
- Quantities are correct
- Prices are correct

## Step 4: Generate Final Bill
1. Click "Generate Final Bill"
2. System calculates:
   - Total charges
   - Total deposits
   - Balance due or refund

3. Review final bill:
   - Check all line items
   - Verify calculations
   - Apply discounts if authorized

## Step 5: Collect Payment or Process Refund

**If Balance Due:**
1. Click "Collect Payment"
2. Enter payment details:
   - Amount
   - Payment mode (Cash/Card/UPI/Cheque)
   - Transaction reference (if card/UPI)
3. Click "Record Payment"
4. System generates receipt

**If Refund Due:**
1. Click "Process Refund"
2. Enter refund details:
   - Refund amount
   - Refund mode
   - Reason
3. Get supervisor approval (if required)
4. Click "Process Refund"
5. System generates refund receipt

## Step 6: Complete Discharge
1. Verify billing cleared
2. Click "Complete Discharge"
3. System:
   - Marks discharge complete
   - Releases bed
   - Updates patient status

## Step 7: Print Documents
1. Print final invoice
2. Print receipt/refund receipt
3. Print discharge summary (if needed)
4. Handover to patient

## Common Scenarios:

**Partial Payment:**
- Record partial payment
- Balance remains due
- Cannot complete discharge until full payment

**Discount Application:**
- Requires authorization
- Enter discount reason
- Apply discount
- Recalculate balance

**Payment Dispute:**
- Escalate to supervisor
- Do not complete discharge
- Document issue
```

---

## 5. Training Materials Checklist

### 5.1 Pre-Training

- [ ] Training schedule finalized
- [ ] Training venue booked
- [ ] Projector/screen setup
- [ ] Test accounts created
- [ ] Test data loaded
- [ ] Staging environment verified
- [ ] User guides printed
- [ ] Quick reference cards printed
- [ ] Attendance sheet prepared
- [ ] Feedback forms prepared

### 5.2 During Training

- [ ] Sign-in sheet
- [ ] Distribute user guides
- [ ] Distribute quick reference cards
- [ ] Live demonstrations
- [ ] Hands-on practice
- [ ] Answer questions
- [ ] Collect feedback

### 5.3 Post-Training

- [ ] Collect feedback forms
- [ ] Analyze feedback
- [ ] Address common questions in FAQ
- [ ] Send follow-up email with resources
- [ ] Schedule refresher sessions if needed
- [ ] Provide ongoing support

---

## 6. Additional Resources

### 6.1 Video Tutorials

**Create short video tutorials (5-10 min each):**
1. Creating Admission Request (Doctor)
2. Approving and Allocating Bed (Clerk)
3. Managing Ward Patients (Nurse)
4. Discharge Billing Process (Billing)
5. Emergency Admission Process (Clerk)

**Host on:** Internal training portal or YouTube (unlisted)

---

### 6.2 FAQ Document

```markdown
# Frequently Asked Questions - IPD Module

## General

**Q: How do I access the IPD module?**
A: Login to MedFlow → Navigate to "IPD" in main menu

**Q: I forgot my password. What should I do?**
A: Click "Forgot Password" on login page or contact IT support

## For Doctors

**Q: Can I edit an admission request after submission?**
A: Yes, if it's still pending. Once approved, contact admission clerk for changes.

**Q: How do I know if my admission request was approved?**
A: You'll receive a notification. Also check "My Admission Requests" page.

**Q: Can I discharge a patient without billing clearance?**
A: No, billing must be cleared first. Contact billing team if urgent.

## For Admission Clerks

**Q: What if no beds are available in preferred ward?**
A: Check similar wards, contact doctor for approval, or add to waiting list.

**Q: Can I allocate a bed to multiple patients?**
A: No, system prevents double-booking. Each bed can only have one patient.

**Q: How do I handle emergency admissions?**
A: Use "Emergency Admission" button for fast-track process.

## For Ward Nurses

**Q: Can I view patients from other wards?**
A: No, you can only view patients in your assigned ward(s).

**Q: How do I transfer a patient to another ward?**
A: Use "Transfer Bed" option. Inter-ward transfers require doctor approval.

**Q: What do the status colors mean?**
A: 🔴 Critical, 🟡 Review, 🟢 Stable, 🟣 Discharge Pending

## For Billing Staff

**Q: When are room rent charges added?**
A: Automatically at midnight for each day of stay.

**Q: Can I apply discounts?**
A: Yes, with proper authorization and reason.

**Q: What if patient can't pay full amount?**
A: Record partial payment. Discharge requires full payment or management approval.
```

---

## 7. Training Evaluation

### 7.1 Feedback Form

```markdown
# Training Feedback Form - IPD Module

**Name:** ________________  **Role:** ________________  **Date:** ________

**Please rate the following (1 = Poor, 5 = Excellent):**

1. Training content relevance:        1  2  3  4  5
2. Trainer knowledge:                 1  2  3  4  5
3. Hands-on practice usefulness:      1  2  3  4  5
4. Training materials quality:        1  2  3  4  5
5. Training duration:                 1  2  3  4  5
6. Overall training experience:       1  2  3  4  5

**How confident do you feel using the IPD module?**
○ Very confident
○ Confident
○ Somewhat confident
○ Not confident

**What did you find most helpful?**
_________________________________________________________________

**What could be improved?**
_________________________________________________________________

**Do you need additional training?**
○ Yes  ○ No

**If yes, on which topics?**
_________________________________________________________________

**Additional comments:**
_________________________________________________________________
_________________________________________________________________
```

---

## 8. Post-Training Support

### 8.1 Support Channels

**Week 1 (Intensive Support):**
- On-site support staff
- Immediate help available
- Response time: < 5 minutes

**Week 2-4 (Transition):**
- Remote support via helpdesk
- Response time: < 30 minutes
- Daily check-in calls

**Ongoing:**
- Helpdesk support
- Email: support@medflow.com
- Phone: +1-XXX-XXX-XXXX
- Response time: < 4 hours

### 8.2 Refresher Training

**Schedule refresher sessions:**
- 1 month after go-live
- 3 months after go-live
- As needed based on feedback

---

## 9. Training Completion Certificate

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│              CERTIFICATE OF COMPLETION              │
│                                                     │
│              This is to certify that                │
│                                                     │
│              ____________________                   │
│                  (Name)                             │
│                                                     │
│      has successfully completed the training on     │
│                                                     │
│          MedFlow Phase 2: IPD Admission Module      │
│                                                     │
│              Date: ____________                     │
│                                                     │
│              Trainer: ___________                   │
│                                                     │
│              Signature: __________                  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

> **Note:** All training materials should be reviewed and updated based on user feedback and system changes.
