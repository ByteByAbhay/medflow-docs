# 🔌 Phase 2: IPD Admission - API Specification

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document defines the **REST API endpoints** for the IPD Admission Module, including request/response formats, authentication, and error handling.

---

## 2. API Overview

### 2.1 Base URL

```
Development: http://localhost:3000/api
Staging: https://staging-api.medflow.com/api
Production: https://api.medflow.com/api
```

### 2.2 Authentication

All endpoints require JWT authentication via Bearer token:

```http
Authorization: Bearer <jwt_token>
```

### 2.3 Common Headers

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <jwt_token>
```

### 2.4 Response Format

**Success Response:**
```json
{
  "success": true,
  "data": { ... },
  "message": "Operation successful"
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": { ... }
  }
}
```

### 2.5 HTTP Status Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input data |
| 401 | Unauthorized | Missing/invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource conflict (e.g., bed already occupied) |
| 422 | Unprocessable Entity | Validation errors |
| 500 | Internal Server Error | Server error |

---

## 3. API Endpoints

### 3.1 Ward Management

#### 3.1.1 List All Wards

```http
GET /wards
```

**Query Parameters:**
- `wardType` (optional): Filter by ward type
- `isActive` (optional): Filter by active status (true/false)
- `floor` (optional): Filter by floor number

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "General Ward A",
      "code": "GWA",
      "wardType": "general",
      "floorNumber": 2,
      "department": "General Medicine",
      "totalCapacity": 40,
      "genderRestriction": "mixed",
      "isActive": true,
      "occupancy": {
        "totalBeds": 40,
        "occupiedBeds": 32,
        "availableBeds": 8,
        "occupancyPercentage": 80
      }
    }
  ]
}
```

**Permissions:** `ward:view`

---

#### 3.1.2 Create Ward

```http
POST /wards
```

**Request Body:**
```json
{
  "name": "ICU Ward",
  "code": "ICU",
  "wardType": "icu",
  "floorNumber": 3,
  "department": "Critical Care",
  "totalCapacity": 10,
  "genderRestriction": "mixed"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "ICU Ward",
    "code": "ICU",
    ...
  },
  "message": "Ward created successfully"
}
```

**Permissions:** `ward:create`

---

#### 3.1.3 Update Ward

```http
PATCH /wards/:id
```

**Request Body:** (partial update)
```json
{
  "totalCapacity": 45,
  "isActive": true
}
```

**Response:** `200 OK`

**Permissions:** `ward:update`

---

### 3.2 Room Management

#### 3.2.1 List Rooms

```http
GET /rooms
```

**Query Parameters:**
- `wardId` (optional): Filter by ward
- `roomType` (optional): Filter by room type
- `isActive` (optional): Filter by active status

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "roomNumber": "201",
      "wardId": "uuid",
      "wardName": "General Ward A",
      "roomType": "double",
      "bedCapacity": 2,
      "ratePerDay": 1500.00,
      "amenities": ["ac", "tv", "attached_bath"],
      "isActive": true,
      "beds": [
        {
          "id": "uuid",
          "bedNumber": "201-A",
          "status": "occupied"
        },
        {
          "id": "uuid",
          "bedNumber": "201-B",
          "status": "available"
        }
      ]
    }
  ]
}
```

**Permissions:** `room:view`

---

#### 3.2.2 Create Room

```http
POST /rooms
```

**Request Body:**
```json
{
  "roomNumber": "301",
  "wardId": "uuid",
  "roomType": "single",
  "bedCapacity": 1,
  "ratePerDay": 2500.00,
  "amenities": ["ac", "tv", "attached_bath", "wifi"]
}
```

**Response:** `201 Created`

**Permissions:** `room:create`

---

### 3.3 Bed Management

#### 3.3.1 Get Bed Availability

```http
GET /beds/availability
```

**Query Parameters:**
- `wardId` (optional): Filter by ward
- `wardType` (optional): Filter by ward type
- `roomType` (optional): Filter by room type
- `status` (optional): Filter by bed status (available, occupied, reserved)

**Response:**
```json
{
  "success": true,
  "data": {
    "summary": {
      "totalBeds": 200,
      "availableBeds": 45,
      "occupiedBeds": 140,
      "reservedBeds": 10,
      "maintenanceBeds": 5
    },
    "byWard": [
      {
        "wardId": "uuid",
        "wardName": "General Ward A",
        "wardType": "general",
        "totalBeds": 40,
        "availableBeds": 8,
        "occupiedBeds": 30,
        "reservedBeds": 2
      }
    ],
    "availableBeds": [
      {
        "bedId": "uuid",
        "bedNumber": "201-B",
        "roomId": "uuid",
        "roomNumber": "201",
        "roomType": "double",
        "ratePerDay": 1500.00,
        "wardId": "uuid",
        "wardName": "General Ward A",
        "wardType": "general",
        "amenities": ["ac", "tv"]
      }
    ]
  }
}
```

**Permissions:** `bed:view`

---

#### 3.3.2 Create Bed

```http
POST /beds
```

**Request Body:**
```json
{
  "bedNumber": "301-A",
  "roomId": "uuid",
  "bedType": "standard"
}
```

**Response:** `201 Created`

**Permissions:** `bed:create`

---

#### 3.3.3 Update Bed Status

```http
PATCH /beds/:id/status
```

**Request Body:**
```json
{
  "status": "under_maintenance",
  "reason": "Bed frame repair required"
}
```

**Response:** `200 OK`

**Permissions:** `bed:update`

---

### 3.4 Admission Requests

#### 3.4.1 Create Admission Request

```http
POST /admission-requests
```

**Request Body:**
```json
{
  "patientId": "uuid",
  "visitId": "uuid",
  "admissionReason": "Severe abdominal pain with fever for 3 days, suspected appendicitis",
  "provisionalDiagnosis": "Acute Appendicitis",
  "wardTypePreference": "general",
  "urgencyLevel": "urgent",
  "estimatedStayDays": 5,
  "admissionOrders": "NPO, IV fluids, IV antibiotics (Ceftriaxone 1g BD), Pain management, Vitals q4h, Surgical consult",
  "specialRequirements": "Patient allergic to penicillin",
  "dietInstructions": "NPO until surgery"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "requestNumber": "ADM-REQ-2026-00001",
    "patientId": "uuid",
    "patientName": "John Doe",
    "status": "pending",
    "requestedBy": "Dr. Smith",
    "requestedAt": "2026-02-27T10:30:00Z",
    ...
  },
  "message": "Admission request created successfully"
}
```

**Permissions:** `admission:create`

---

#### 3.4.2 List Admission Requests

```http
GET /admission-requests
```

**Query Parameters:**
- `status` (optional): pending, approved, rejected, completed
- `urgencyLevel` (optional): routine, urgent, emergency
- `wardType` (optional): general, private, icu, etc.
- `requestedBy` (optional): Doctor ID
- `fromDate` (optional): Filter from date
- `toDate` (optional): Filter to date

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "requestNumber": "ADM-REQ-2026-00001",
      "patient": {
        "id": "uuid",
        "uhid": "MF-2026-00123",
        "name": "John Doe",
        "age": 45,
        "gender": "male"
      },
      "admissionReason": "Severe abdominal pain...",
      "provisionalDiagnosis": "Acute Appendicitis",
      "wardTypePreference": "general",
      "urgencyLevel": "urgent",
      "status": "pending",
      "requestedBy": {
        "id": "uuid",
        "name": "Dr. Smith"
      },
      "requestedAt": "2026-02-27T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45
  }
}
```

**Permissions:** `admission:view`

---

#### 3.4.3 Get Admission Request Details

```http
GET /admission-requests/:id
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "requestNumber": "ADM-REQ-2026-00001",
    "patient": { ... },
    "visit": { ... },
    "admissionReason": "...",
    "provisionalDiagnosis": "...",
    "wardTypePreference": "general",
    "urgencyLevel": "urgent",
    "estimatedStayDays": 5,
    "admissionOrders": "...",
    "specialRequirements": "...",
    "dietInstructions": "...",
    "status": "pending",
    "requestedBy": { ... },
    "requestedAt": "2026-02-27T10:30:00Z",
    "reviewedBy": null,
    "reviewedAt": null,
    "rejectionReason": null
  }
}
```

**Permissions:** `admission:view`

---

#### 3.4.4 Approve Admission Request

```http
POST /admission-requests/:id/approve
```

**Request Body:**
```json
{
  "comments": "Approved for general ward admission"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "status": "approved",
    "reviewedBy": { ... },
    "reviewedAt": "2026-02-27T11:00:00Z"
  },
  "message": "Admission request approved"
}
```

**Permissions:** `admission:approve`

---

#### 3.4.5 Reject Admission Request

```http
POST /admission-requests/:id/reject
```

**Request Body:**
```json
{
  "rejectionReason": "Patient condition improved, can be managed as outpatient"
}
```

**Response:** `200 OK`

**Permissions:** `admission:approve`

---

### 3.5 Admissions

#### 3.5.1 Create Admission (Allocate Bed)

```http
POST /admissions
```

**Request Body:**
```json
{
  "admissionRequestId": "uuid",
  "patientId": "uuid",
  "bedId": "uuid",
  "admissionType": "planned",
  "admissionSource": "opd",
  "attendingDoctorId": "uuid",
  "expectedAdmissionDate": "2026-02-27T14:00:00Z",
  "admissionNotes": "Patient admitted for appendectomy",
  "vitalsAtAdmission": {
    "bp": "120/80",
    "pulse": 78,
    "temp": 98.6,
    "spo2": 98,
    "weight": 70,
    "height": 170
  },
  "currentMedications": [
    {
      "name": "Aspirin",
      "dosage": "75mg",
      "frequency": "Once daily"
    }
  ],
  "allergies": [
    {
      "allergen": "Penicillin",
      "reaction": "Rash",
      "severity": "moderate"
    }
  ],
  "admissionConsent": true,
  "treatmentConsent": true,
  "financialConsent": true
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "admissionNumber": "ADM-2026-00001",
    "patient": { ... },
    "bed": {
      "id": "uuid",
      "bedNumber": "201-A",
      "room": "201",
      "ward": "General Ward A"
    },
    "admissionDate": "2026-02-27T14:00:00Z",
    "status": "admitted",
    ...
  },
  "message": "Patient admitted successfully"
}
```

**Permissions:** `admission:create`

**Business Rules:**
- Bed must be available
- Admission request must be approved (if exists)
- All required consents must be true
- Vitals at admission required

---

#### 3.5.2 List Admissions

```http
GET /admissions
```

**Query Parameters:**
- `status` (optional): admitted, discharge_pending, discharged
- `wardId` (optional): Filter by ward
- `attendingDoctorId` (optional): Filter by doctor
- `patientId` (optional): Filter by patient
- `fromDate` (optional): Admission from date
- `toDate` (optional): Admission to date

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "admissionNumber": "ADM-2026-00001",
      "patient": {
        "id": "uuid",
        "uhid": "MF-2026-00123",
        "name": "John Doe",
        "age": 45,
        "gender": "male"
      },
      "location": {
        "ward": "General Ward A",
        "room": "201",
        "bed": "201-A"
      },
      "admissionDate": "2026-02-27T14:00:00Z",
      "daysAdmitted": 2,
      "provisionalDiagnosis": "Acute Appendicitis",
      "attendingDoctor": {
        "id": "uuid",
        "name": "Dr. Smith"
      },
      "status": "admitted",
      "billing": {
        "totalCharges": 15000.00,
        "totalDeposits": 10000.00,
        "balance": -5000.00
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 85
  }
}
```

**Permissions:** `admission:view`

---

#### 3.5.3 Get Admission Details

```http
GET /admissions/:id
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "admissionNumber": "ADM-2026-00001",
    "patient": { ... },
    "admissionRequest": { ... },
    "location": {
      "wardId": "uuid",
      "ward": "General Ward A",
      "roomId": "uuid",
      "room": "201",
      "bedId": "uuid",
      "bed": "201-A"
    },
    "admissionDetails": {
      "admissionType": "planned",
      "admissionSource": "opd",
      "admissionDate": "2026-02-27T14:00:00Z",
      "daysAdmitted": 2,
      "expectedDischargeDate": "2026-03-04",
      "admissionReason": "...",
      "provisionalDiagnosis": "...",
      "admissionOrders": "...",
      "specialRequirements": "...",
      "dietInstructions": "..."
    },
    "vitalsAtAdmission": { ... },
    "currentMedications": [ ... ],
    "allergies": [ ... ],
    "doctors": {
      "referring": { ... },
      "admitting": { ... },
      "attending": { ... }
    },
    "consent": {
      "admission": true,
      "treatment": true,
      "financial": true,
      "documents": [ ... ]
    },
    "status": "admitted",
    "billing": {
      "totalCharges": 15000.00,
      "totalDeposits": 10000.00,
      "balance": -5000.00,
      "billingCleared": false
    },
    "transferHistory": [ ... ],
    "createdAt": "2026-02-27T14:00:00Z",
    "updatedAt": "2026-02-27T14:00:00Z"
  }
}
```

**Permissions:** `admission:view`

---

#### 3.5.4 Get Ward Patient List

```http
GET /admissions/ward/:wardId
```

**Response:**
```json
{
  "success": true,
  "data": {
    "ward": {
      "id": "uuid",
      "name": "General Ward A",
      "occupancy": {
        "totalBeds": 40,
        "occupiedBeds": 32,
        "availableBeds": 8
      }
    },
    "patients": [
      {
        "admissionId": "uuid",
        "admissionNumber": "ADM-2026-00001",
        "bedNumber": "201-A",
        "patient": { ... },
        "admissionDate": "2026-02-27T14:00:00Z",
        "daysAdmitted": 2,
        "diagnosis": "Acute Appendicitis",
        "attendingDoctor": "Dr. Smith",
        "status": "admitted"
      }
    ]
  }
}
```

**Permissions:** `admission:view`

---

#### 3.5.5 Get Doctor's Admitted Patients

```http
GET /admissions/doctor/:doctorId
```

**Response:** Similar to ward patient list

**Permissions:** `admission:view`

---

### 3.6 Bed Transfers

#### 3.6.1 Transfer Patient Bed

```http
POST /admissions/:id/transfer
```

**Request Body:**
```json
{
  "toBedId": "uuid",
  "transferReason": "Patient condition improved, moving from ICU to general ward",
  "transferType": "inter_ward"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "transferId": "uuid",
    "admission": { ... },
    "from": {
      "ward": "ICU",
      "room": "ICU-1",
      "bed": "ICU-1-A"
    },
    "to": {
      "ward": "General Ward A",
      "room": "201",
      "bed": "201-B"
    },
    "transferDate": "2026-02-28T10:00:00Z",
    "transferReason": "...",
    "transferType": "inter_ward"
  },
  "message": "Patient transferred successfully"
}
```

**Permissions:** `admission:transfer`

**Business Rules:**
- Target bed must be available
- Inter-ward transfer requires doctor approval
- Transfer reason required (min 20 chars for inter-ward)

---

#### 3.6.2 Get Transfer History

```http
GET /admissions/:id/transfers
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "from": { ... },
      "to": { ... },
      "transferDate": "2026-02-28T10:00:00Z",
      "transferReason": "...",
      "transferType": "inter_ward",
      "requestedBy": { ... },
      "approvedBy": { ... }
    }
  ]
}
```

**Permissions:** `admission:view`

---

### 3.7 Discharge Process

#### 3.7.1 Initiate Discharge

```http
POST /admissions/:id/discharge/initiate
```

**Request Body:**
```json
{
  "dischargeDate": "2026-03-01T10:00:00Z",
  "dischargeType": "regular",
  "finalDiagnosis": "Post-operative Appendectomy, Recovered",
  "hospitalCourse": "Patient admitted with acute appendicitis. Underwent laparoscopic appendectomy on Day 1. Post-operative recovery uneventful. Wound healing well. Patient ambulating, tolerating diet. Vitals stable. Ready for discharge.",
  "proceduresPerformed": [
    {
      "procedure": "Laparoscopic Appendectomy",
      "date": "2026-02-27",
      "notes": "Procedure completed successfully without complications"
    }
  ],
  "investigationsSummary": "Pre-op labs: WBC 14,000, CRP elevated. Post-op: WBC normalizing",
  "medicationsOnDischarge": [
    {
      "name": "Amoxicillin",
      "dosage": "500mg",
      "frequency": "Three times daily",
      "duration": "7 days"
    },
    {
      "name": "Ibuprofen",
      "dosage": "400mg",
      "frequency": "As needed for pain",
      "duration": "5 days"
    }
  ],
  "dietAdvice": "Regular diet, avoid spicy foods for 1 week",
  "activityRestrictions": "Avoid heavy lifting for 2 weeks, no strenuous exercise for 4 weeks",
  "warningSigns": "Fever > 101°F, increasing abdominal pain, wound redness/discharge, persistent vomiting",
  "followUpInstructions": "Follow up in OPD in 1 week for wound check and suture removal",
  "followUpDate": "2026-03-08"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "admission": { ... },
    "dischargeSummary": { ... },
    "status": "discharge_pending",
    "summaryPdfUrl": "https://..."
  },
  "message": "Discharge initiated. Pending billing clearance."
}
```

**Permissions:** `admission:discharge`

---

#### 3.7.2 Complete Discharge

```http
POST /admissions/:id/discharge/complete
```

**Request Body:**
```json
{
  "billingCleared": true,
  "finalInvoiceId": "uuid"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "admission": { ... },
    "status": "discharged",
    "actualDischargeDate": "2026-03-01T11:30:00Z",
    "dischargeSummaryPdfUrl": "https://...",
    "finalInvoicePdfUrl": "https://..."
  },
  "message": "Patient discharged successfully"
}
```

**Permissions:** `admission:discharge`

**Business Rules:**
- Billing must be cleared (or authorized override)
- Discharge summary must exist
- Bed is released (status → available)

---

#### 3.7.3 Get Discharge Summary

```http
GET /admissions/:id/discharge-summary
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "admission": { ... },
    "dischargeDate": "2026-03-01T10:00:00Z",
    "dischargeType": "regular",
    "finalDiagnosis": "...",
    "hospitalCourse": "...",
    "proceduresPerformed": [ ... ],
    "investigationsSummary": "...",
    "medicationsOnDischarge": [ ... ],
    "dietAdvice": "...",
    "activityRestrictions": "...",
    "warningSigns": "...",
    "followUpInstructions": "...",
    "followUpDate": "2026-03-08",
    "summaryPdfUrl": "https://...",
    "createdBy": { ... },
    "createdAt": "2026-03-01T10:00:00Z"
  }
}
```

**Permissions:** `admission:view`

---

#### 3.7.4 Download Discharge Summary PDF

```http
GET /admissions/:id/discharge-summary/pdf
```

**Response:** PDF file download

**Permissions:** `admission:view`

---

### 3.8 Billing Integration

#### 3.8.1 Get Admission Charges

```http
GET /admissions/:id/charges
```

**Query Parameters:**
- `fromDate` (optional): Filter from date
- `toDate` (optional): Filter to date
- `chargeType` (optional): Filter by charge type

**Response:**
```json
{
  "success": true,
  "data": {
    "admission": { ... },
    "charges": [
      {
        "id": "uuid",
        "chargeType": "admission_fee",
        "description": "Admission Fee",
        "chargeDate": "2026-02-27",
        "quantity": 1,
        "unitPrice": 1000.00,
        "totalAmount": 1000.00,
        "discountAmount": 0,
        "netAmount": 1000.00
      },
      {
        "id": "uuid",
        "chargeType": "room_rent",
        "description": "Room Rent - General Ward (Double)",
        "chargeDate": "2026-02-27",
        "quantity": 1,
        "unitPrice": 1500.00,
        "totalAmount": 1500.00,
        "discountAmount": 0,
        "netAmount": 1500.00
      }
    ],
    "summary": {
      "totalCharges": 15000.00,
      "totalDiscount": 500.00,
      "netCharges": 14500.00,
      "byChargeType": {
        "admission_fee": 1000.00,
        "room_rent": 4500.00,
        "consultation": 3000.00,
        "investigation": 4000.00,
        "procedure": 2000.00
      }
    }
  }
}
```

**Permissions:** `billing:view`

---

#### 3.8.2 Add Charge to Admission

```http
POST /admissions/:id/charges
```

**Request Body:**
```json
{
  "chargeType": "investigation",
  "serviceId": "uuid",
  "description": "Complete Blood Count",
  "chargeDate": "2026-02-28",
  "quantity": 1,
  "unitPrice": 500.00,
  "discountPercentage": 0,
  "discountReason": ""
}
```

**Response:** `201 Created`

**Permissions:** `billing:create`

---

#### 3.8.3 Collect Deposit

```http
POST /admissions/:id/deposits
```

**Request Body:**
```json
{
  "amount": 10000.00,
  "paymentMode": "cash",
  "transactionReference": "",
  "remarks": "Initial deposit at admission"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "depositNumber": "DEP-2026-00001",
    "admissionId": "uuid",
    "amount": 10000.00,
    "paymentMode": "cash",
    "status": "received",
    "receivedDate": "2026-02-27T14:30:00Z",
    "receivedBy": { ... },
    "receiptPdfUrl": "https://..."
  },
  "message": "Deposit collected successfully"
}
```

**Permissions:** `billing:deposit`

---

#### 3.8.4 Get Deposits

```http
GET /admissions/:id/deposits
```

**Response:**
```json
{
  "success": true,
  "data": {
    "deposits": [
      {
        "id": "uuid",
        "depositNumber": "DEP-2026-00001",
        "amount": 10000.00,
        "paymentMode": "cash",
        "status": "received",
        "receivedDate": "2026-02-27T14:30:00Z",
        "receivedBy": { ... }
      }
    ],
    "summary": {
      "totalDeposits": 10000.00,
      "adjustedAmount": 0,
      "refundedAmount": 0,
      "availableBalance": 10000.00
    }
  }
}
```

**Permissions:** `billing:view`

---

#### 3.8.5 Generate Final Bill

```http
POST /admissions/:id/billing/final-bill
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "invoice": {
      "id": "uuid",
      "invoiceNumber": "INV-2026-00123",
      "admissionId": "uuid",
      "totalCharges": 14500.00,
      "totalDeposits": 10000.00,
      "balanceDue": 4500.00,
      "items": [ ... ]
    },
    "invoicePdfUrl": "https://..."
  }
}
```

**Permissions:** `billing:create`

---

### 3.9 Reports & Analytics

#### 3.9.1 Get Admission Statistics

```http
GET /admissions/reports/statistics
```

**Query Parameters:**
- `fromDate` (required): Start date
- `toDate` (required): End date
- `wardId` (optional): Filter by ward
- `doctorId` (optional): Filter by doctor

**Response:**
```json
{
  "success": true,
  "data": {
    "period": {
      "from": "2026-02-01",
      "to": "2026-02-28"
    },
    "summary": {
      "totalAdmissions": 145,
      "totalDischarges": 138,
      "currentlyAdmitted": 85,
      "averageLengthOfStay": 4.2,
      "averageOccupancyRate": 82.5
    },
    "byWard": [ ... ],
    "byDoctor": [ ... ],
    "trend": [ ... ]
  }
}
```

**Permissions:** `reports:view`

---

## 4. WebSocket Events

### 4.1 Connection

```javascript
const socket = io('https://api.medflow.com', {
  auth: {
    token: 'jwt_token'
  }
});
```

### 4.2 Subscribe to Channels

```javascript
// Subscribe to ward updates
socket.emit('subscribe', { channel: 'ward:uuid' });

// Subscribe to admission updates
socket.emit('subscribe', { channel: 'admissions' });

// Subscribe to bed availability updates
socket.emit('subscribe', { channel: 'beds' });
```

### 4.3 Events

#### 4.3.1 Admission Created

```javascript
socket.on('admission:created', (data) => {
  // data: { admission: {...}, ward: {...} }
});
```

#### 4.3.2 Bed Status Changed

```javascript
socket.on('bed:status-changed', (data) => {
  // data: { bedId, status, ward, room }
});
```

#### 4.3.3 Patient Transferred

```javascript
socket.on('admission:transferred', (data) => {
  // data: { admission, from, to }
});
```

#### 4.3.4 Discharge Initiated

```javascript
socket.on('admission:discharge-initiated', (data) => {
  // data: { admission, dischargeSummary }
});
```

---

## 5. Error Codes

| Code | Message | HTTP Status |
|------|---------|-------------|
| `BED_NOT_AVAILABLE` | Bed is not available for allocation | 409 |
| `BED_ALREADY_OCCUPIED` | Bed is already occupied | 409 |
| `ADMISSION_REQUEST_NOT_APPROVED` | Admission request must be approved first | 400 |
| `INVALID_BED_TRANSFER` | Cannot transfer to same bed | 400 |
| `DISCHARGE_BILLING_NOT_CLEARED` | Cannot complete discharge with pending bills | 400 |
| `ADMISSION_NOT_FOUND` | Admission not found | 404 |
| `WARD_NOT_FOUND` | Ward not found | 404 |
| `INSUFFICIENT_PERMISSIONS` | User does not have required permissions | 403 |
| `VALIDATION_ERROR` | Input validation failed | 422 |

---

## 6. Rate Limiting

- **General endpoints:** 100 requests per minute per user
- **Report endpoints:** 10 requests per minute per user
- **WebSocket connections:** 5 connections per user

---

## 7. Pagination

All list endpoints support pagination:

**Query Parameters:**
- `page` (default: 1)
- `limit` (default: 20, max: 100)

**Response:**
```json
{
  "data": [ ... ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 145,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

---

## 8. API Versioning

API version is included in the base URL:

```
/api/v1/admissions
/api/v2/admissions (future)
```

Current version: `v1`

---

> **Note:** This API specification is subject to change during development. All changes will be documented in the changelog.
