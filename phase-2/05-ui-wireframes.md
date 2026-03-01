# 🎨 Phase 2: IPD Admission - UI Wireframes & Design Specifications

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document provides **UI wireframes and design specifications** for the IPD Admission Module, including screen layouts, component specifications, and user flows.

---

## 2. Design Principles

### 2.1 Core Principles

1. **Clarity:** Information hierarchy is clear and intuitive
2. **Efficiency:** Minimize clicks to complete tasks
3. **Consistency:** Follow Phase 1 design patterns
4. **Responsiveness:** Works on desktop and tablet (iPad)
5. **Accessibility:** WCAG 2.1 AA compliant

### 2.2 Design System

**Framework:** Material-UI (MUI) v5  
**Theme:** Extends Phase 1 theme  
**Icons:** Material Icons  
**Colors:**
- Primary: `#1976d2` (Blue)
- Secondary: `#dc004e` (Pink)
- Success: `#4caf50` (Green)
- Warning: `#ff9800` (Orange)
- Error: `#f44336` (Red)
- Info: `#2196f3` (Light Blue)

---

## 3. Screen Layouts

### 3.1 Admission Request Queue (Nurse)

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow                    🏥 Admission Queue              👤 Nurse  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  📋 Admission Request Queue                                          │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🔍 Search patients...          [Filter ▼] [Urgency ▼]       │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🔴 EMERGENCY                                                  │   │
│  │ ┌───────────────────────────────────────────────────────────┐│  │
│  │ │ ADM-REQ-2026-00123                    ⏰ 10 mins ago       ││  │
│  │ │ John Doe (MF-2026-00456) • 45Y • Male                     ││  │
│  │ │ Diagnosis: Acute Appendicitis                             ││  │
│  │ │ Ward: General | Urgency: Emergency                        ││  │
│  │ │ Requested by: Dr. Smith                                   ││  │
│  │ │                                                            ││  │
│  │ │ [View Details]  [Approve]  [Reject]                       ││  │
│  │ └───────────────────────────────────────────────────────────┘│  │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🟡 URGENT                                                     │   │
│  │ ┌───────────────────────────────────────────────────────────┐│  │
│  │ │ ADM-REQ-2026-00124                    ⏰ 2 hours ago       ││  │
│  │ │ Jane Smith (MF-2026-00457) • 32F • Female                 ││  │
│  │ │ Diagnosis: Severe Pneumonia                               ││  │
│  │ │ Ward: General | Urgency: Urgent                           ││  │
│  │ │ Requested by: Dr. Johnson                                 ││  │
│  │ │                                                            ││  │
│  │ │ [View Details]  [Approve]  [Reject]                       ││  │
│  │ └───────────────────────────────────────────────────────────┘│  │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🟢 ROUTINE                                                    │   │
│  │ [3 more requests...]                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  Showing 5 of 15 requests                            [Load More]     │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Color-coded urgency levels
- Real-time updates
- Quick actions (View, Approve, Reject)
- Search and filter
- Patient info at a glance

---

### 3.2 Bed Allocation Screen

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow                  🛏️ Bed Allocation                 👤 Nurse  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ← Back to Queue                                                     │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 👤 Patient Information                                        │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │ John Doe (MF-2026-00456) • 45Y • Male                        │   │
│  │ Diagnosis: Acute Appendicitis                                │   │
│  │ Ward Preference: General | Urgency: Emergency                │   │
│  │ Requested by: Dr. Smith                                      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🛏️ Available Beds                                             │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │ [Ward: General ▼]  [Room Type: All ▼]  [Floor: All ▼]       │   │
│  │                                                               │   │
│  │ General Ward A (8 available)                                 │   │
│  │ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐        │   │
│  │ │ 🟢 201-A │ │ 🟢 201-B │ │ 🔴 202-A │ │ 🟢 203-A │        │   │
│  │ │ Double   │ │ Double   │ │ Double   │ │ Single   │        │   │
│  │ │ ₹1500/day│ │ ₹1500/day│ │ Occupied │ │ ₹2500/day│        │   │
│  │ │ AC, TV   │ │ AC, TV   │ │          │ │ AC, TV   │        │   │
│  │ │ [Select] │ │ [Select] │ │          │ │ [Select] │        │   │
│  │ └──────────┘ └──────────┘ └──────────┘ └──────────┘        │   │
│  │                                                               │   │
│  │ General Ward B (5 available)                                 │   │
│  │ [Show more beds...]                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 📝 Admission Details                                          │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │ Selected Bed: 201-A (General Ward A, Room 201)               │   │
│  │                                                               │   │
│  │ Admission Date/Time: [2026-02-27] [14:00]                    │   │
│  │ Attending Doctor: [Select Doctor ▼]                          │   │
│  │ Admission Notes: [Optional notes...]                         │   │
│  │                                                               │   │
│  │                           [Cancel]  [Confirm Admission]       │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Visual bed cards with status
- Filter by ward, room type, floor
- Real-time availability
- Bed details (room type, rate, amenities)
- Admission details form
- Confirmation step

---

### 3.3 Ward Patient Dashboard (Nurse)

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow              🏥 General Ward A                     👤 Nurse  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  📊 Ward Overview                                                    │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐               │
│  │ 🛏️ Occupancy  │ │ 👥 Patients   │ │ ⚠️ Alerts     │               │
│  │   32/40       │ │   32 Active   │ │   2 Critical  │               │
│  │   80%         │ │   3 Discharge │ │   5 Review    │               │
│  └──────────────┘ └──────────────┘ └──────────────┘               │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🔍 Search patients...          [Room ▼] [Status ▼] [Sort ▼] │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Bed │ Patient        │ Age/Sex│ Adm Date│ Days│ Diagnosis   │   │
│  ├─────┼────────────────┼────────┼─────────┼─────┼─────────────┤   │
│  │ 201A│ John Doe       │ 45/M   │ Feb 27  │  2  │ Appendicitis│   │
│  │     │ MF-2026-00456  │        │ 14:00   │     │ Dr. Smith   │   │
│  │     │ 🔴 Critical    │        │         │     │ [View] [⋮]  │   │
│  ├─────┼────────────────┼────────┼─────────┼─────┼─────────────┤   │
│  │ 201B│ Jane Smith     │ 32/F   │ Feb 26  │  3  │ Pneumonia   │   │
│  │     │ MF-2026-00457  │        │ 10:00   │     │ Dr. Johnson │   │
│  │     │ 🟡 Review      │        │         │     │ [View] [⋮]  │   │
│  ├─────┼────────────────┼────────┼─────────┼─────┼─────────────┤   │
│  │ 202A│ Bob Wilson     │ 58/M   │ Feb 25  │  4  │ Diabetes    │   │
│  │     │ MF-2026-00458  │        │ 09:00   │     │ Dr. Lee     │   │
│  │     │ 🟢 Stable      │        │         │     │ [View] [⋮]  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  Showing 3 of 32 patients                            [Load More]     │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Ward occupancy summary
- Patient status indicators (Critical, Review, Stable)
- Sortable table
- Quick actions menu (⋮)
- Search and filter

---

### 3.4 Patient Detail View (Nurse)

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow         👤 John Doe (MF-2026-00456)              👤 Nurse   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ← Back to Ward                                                      │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 📋 Patient Header                                             │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │ John Doe • 45Y • Male • MF-2026-00456                        │   │
│  │ 🛏️ General Ward A - Room 201 - Bed 201-A                     │   │
│  │ 📅 Admitted: Feb 27, 2026 14:00 (2 days)                     │   │
│  │ 👨‍⚕️ Dr. Smith (Attending)                                      │   │
│  │ 🔴 Critical                                                   │   │
│  │                                                               │   │
│  │ [Transfer Bed]  [View History]  [Print Summary]              │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ [Overview] [Clinical] [Medications] [Investigations] [Billing]│  │
│  ├─────────────────────────────────────────────────────────────┤   │
│  │                                                               │   │
│  │ 🩺 Clinical Information                                       │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │ Diagnosis: Acute Appendicitis                                │   │
│  │ Admission Reason: Severe abdominal pain with fever...        │   │
│  │                                                               │   │
│  │ 📊 Current Vitals (Last updated: 2 hours ago)                │   │
│  │ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐        │   │
│  │ │ BP       │ │ Pulse    │ │ Temp     │ │ SpO2     │        │   │
│  │ │ 120/80   │ │ 78 bpm   │ │ 98.6°F   │ │ 98%      │        │   │
│  │ └──────────┘ └──────────┘ └──────────┘ └──────────┘        │   │
│  │                                                               │   │
│  │ ⚠️ Allergies                                                  │   │
│  │ • Penicillin (Rash - Moderate)                               │   │
│  │                                                               │   │
│  │ 💊 Current Medications                                        │   │
│  │ • Ceftriaxone 1g IV BD                                       │   │
│  │ • Paracetamol 500mg PO TDS                                   │   │
│  │                                                               │   │
│  │ 📝 Admission Orders                                           │   │
│  │ • NPO                                                         │   │
│  │ • IV fluids - NS @ 100ml/hr                                  │   │
│  │ • Vitals q4h                                                 │   │
│  │ • Pain management as needed                                  │   │
│  │                                                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Patient header with key info
- Tab navigation
- Vitals display with icons
- Allergies prominently displayed
- Current medications and orders
- Quick actions

---

### 3.5 Discharge Initiation Form (Doctor)

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow            📝 Initiate Discharge                  👤 Doctor  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ← Back to Patient                                                   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 👤 Patient: John Doe (MF-2026-00456) • 45Y • Male           │   │
│  │ 📅 Admitted: Feb 27, 2026 (2 days)                           │   │
│  │ 🛏️ General Ward A - Bed 201-A                                │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 📋 Discharge Details                                          │   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │                                                               │   │
│  │ Discharge Date/Time:                                         │   │
│  │ [2026-03-01] [10:00]                                         │   │
│  │                                                               │   │
│  │ Discharge Type: *                                            │   │
│  │ ○ Regular  ○ LAMA  ○ Absconded  ○ Death  ○ Transfer         │   │
│  │                                                               │   │
│  │ Final Diagnosis: *                                           │   │
│  │ [Post-operative Appendectomy, Recovered]                     │   │
│  │                                                               │   │
│  │ Hospital Course: *                                           │   │
│  │ ┌───────────────────────────────────────────────────────┐   │   │
│  │ │ Patient admitted with acute appendicitis. Underwent   │   │   │
│  │ │ laparoscopic appendectomy on Day 1. Post-operative    │   │   │
│  │ │ recovery uneventful. Wound healing well. Patient      │   │   │
│  │ │ ambulating, tolerating diet. Vitals stable. Ready     │   │   │
│  │ │ for discharge.                                        │   │   │
│  │ └───────────────────────────────────────────────────────┘   │   │
│  │                                                               │   │
│  │ Procedures Performed:                                        │   │
│  │ ┌─────────────────────────────────────────────────────┐     │   │
│  │ │ Procedure: Laparoscopic Appendectomy                │     │   │
│  │ │ Date: 2026-02-27                                    │     │   │
│  │ │ Notes: Procedure completed successfully...          │     │   │
│  │ │                                          [Remove]    │     │   │
│  │ └─────────────────────────────────────────────────────┘     │   │
│  │ [+ Add Procedure]                                            │   │
│  │                                                               │   │
│  │ [Continue to Medications →]                                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  [Save Draft]                                     [Cancel] [Continue]│
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Multi-step form
- Rich text editor for hospital course
- Dynamic procedure list
- Save draft functionality
- Clear validation indicators

---

### 3.6 Bed Status Board

```
┌─────────────────────────────────────────────────────────────────────┐
│ MedFlow              🛏️ Bed Status Board                  👤 Nurse   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  [Ward: All ▼]  [Floor: All ▼]  [Status: All ▼]  [🔄 Refresh]      │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🏥 General Ward A - Floor 2                    32/40 Occupied│   │
│  │ ─────────────────────────────────────────────────────────────│   │
│  │                                                               │   │
│  │ Room 201 (Double)                                            │   │
│  │ ┌─────────────┐ ┌─────────────┐                             │   │
│  │ │ 🔴 201-A    │ │ 🟢 201-B    │                             │   │
│  │ │ John Doe    │ │ Available   │                             │   │
│  │ │ Day 2       │ │             │                             │   │
│  │ └─────────────┘ └─────────────┘                             │   │
│  │                                                               │   │
│  │ Room 202 (Double)                                            │   │
│  │ ┌─────────────┐ ┌─────────────┐                             │   │
│  │ │ 🔴 202-A    │ │ 🔴 202-B    │                             │   │
│  │ │ Jane Smith  │ │ Bob Wilson  │                             │   │
│  │ │ Day 3       │ │ Day 4       │                             │   │
│  │ └─────────────┘ └─────────────┘                             │   │
│  │                                                               │   │
│  │ Room 203 (Single)                                            │   │
│  │ ┌─────────────┐                                              │   │
│  │ │ 🟡 203-A    │                                              │   │
│  │ │ Reserved    │                                              │   │
│  │ │ For Surgery │                                              │   │
│  │ └─────────────┘                                              │   │
│  │                                                               │   │
│  │ [View More Rooms...]                                         │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🏥 ICU - Floor 3                                10/10 Full    │   │
│  │ [View Beds...]                                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  Legend: 🟢 Available  🔴 Occupied  🟡 Reserved  ⚫ Maintenance      │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- Visual bed grid
- Color-coded status
- Hover for patient details
- Click to view full details
- Real-time updates
- Ward-wise grouping

---

## 4. Component Specifications

### 4.1 Admission Request Card

**Component:** `AdmissionRequestCard`

**Props:**
```typescript
interface AdmissionRequestCardProps {
  request: AdmissionRequest;
  onView: (id: string) => void;
  onApprove: (id: string) => void;
  onReject: (id: string) => void;
}
```

**Styling:**
- Border color based on urgency (red/orange/green)
- Hover effect: elevation increase
- Responsive: Stack on mobile

---

### 4.2 Bed Card

**Component:** `BedCard`

**Props:**
```typescript
interface BedCardProps {
  bed: Bed;
  selected: boolean;
  onSelect: (bedId: string) => void;
}
```

**States:**
- Available: Green border, clickable
- Occupied: Red border, disabled
- Reserved: Yellow border, disabled
- Selected: Blue border, elevated

---

### 4.3 Patient Status Badge

**Component:** `PatientStatusBadge`

**Props:**
```typescript
interface PatientStatusBadgeProps {
  status: 'critical' | 'review' | 'stable';
}
```

**Styling:**
- Critical: Red background, white text
- Review: Orange background, white text
- Stable: Green background, white text
- Small, rounded badge

---

## 5. Responsive Design

### 5.1 Breakpoints

- **Desktop:** ≥ 1280px
- **Tablet:** 768px - 1279px
- **Mobile:** < 768px

### 5.2 Tablet Adaptations

- Side-by-side layouts become stacked
- Tables become cards
- Filters move to drawer
- Touch-friendly button sizes (min 44x44px)

---

## 6. Accessibility

### 6.1 WCAG 2.1 AA Compliance

- [ ] Color contrast ratio ≥ 4.5:1
- [ ] Keyboard navigation support
- [ ] Screen reader compatible
- [ ] Focus indicators visible
- [ ] ARIA labels on interactive elements
- [ ] Form labels properly associated
- [ ] Error messages descriptive

### 6.2 Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl + K` | Focus search |
| `Ctrl + N` | New admission request |
| `Ctrl + F` | Open filters |
| `Esc` | Close modal/drawer |
| `Tab` | Navigate forward |
| `Shift + Tab` | Navigate backward |

---

## 7. Loading States

### 7.1 Skeleton Loaders

Use skeleton loaders for:
- Patient lists
- Bed cards
- Patient details
- Charts/graphs

### 7.2 Spinners

Use spinners for:
- Button actions (inline)
- Form submissions
- PDF generation

---

## 8. Empty States

### 8.1 No Admission Requests

```
┌─────────────────────────────────────┐
│                                     │
│          📋                         │
│                                     │
│    No Admission Requests            │
│                                     │
│  All admission requests have been   │
│  processed. New requests will       │
│  appear here.                       │
│                                     │
└─────────────────────────────────────┘
```

### 8.2 No Beds Available

```
┌─────────────────────────────────────┐
│                                     │
│          🛏️                         │
│                                     │
│    No Beds Available                │
│                                     │
│  All beds in this ward are          │
│  currently occupied. Try a          │
│  different ward or wait for a       │
│  bed to become available.           │
│                                     │
│     [View Other Wards]              │
│                                     │
└─────────────────────────────────────┘
```

---

## 9. Error States

### 9.1 Form Validation Errors

- Inline error messages below fields
- Red border on invalid fields
- Error icon (⚠️) next to message
- Descriptive error text

### 9.2 API Errors

```
┌─────────────────────────────────────┐
│  ⚠️ Error                            │
│  ─────────────────────────────────  │
│  Failed to create admission.        │
│  The selected bed is no longer      │
│  available. Please select another   │
│  bed.                               │
│                                     │
│  [Try Again]  [Cancel]              │
└─────────────────────────────────────┘
```

---

## 10. Success States

### 10.1 Toast Notifications

```
┌─────────────────────────────────────┐
│  ✅ Admission Created Successfully   │
│  Patient admitted to Bed 201-A      │
│                              [View] │
└─────────────────────────────────────┘
```

**Position:** Top-right  
**Duration:** 5 seconds  
**Auto-dismiss:** Yes

---

## 11. Modal Dialogs

### 11.1 Confirmation Dialog

```
┌─────────────────────────────────────┐
│  ⚠️ Confirm Action                   │
│  ─────────────────────────────────  │
│  Are you sure you want to reject    │
│  this admission request?            │
│                                     │
│  This action cannot be undone.      │
│                                     │
│  [Cancel]           [Confirm]       │
└─────────────────────────────────────┘
```

---

## 12. Print Layouts

### 12.1 Discharge Summary PDF

- A4 size (210mm × 297mm)
- Hospital letterhead
- Patient demographics header
- Sections: Admission details, Hospital course, Medications, Instructions
- Doctor signature block
- Footer with page numbers

---

> **Note:** All wireframes are conceptual. Final designs may vary based on user feedback and usability testing.
