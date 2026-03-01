# 🧪 Phase 2: IPD Admission - Testing Strategy

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document defines the **comprehensive testing strategy** for the IPD Admission Module, including test types, test cases, UAT plan, and acceptance criteria.

---

## 2. Testing Overview

### 2.1 Testing Pyramid

```
        /\
       /  \      E2E Tests (10%)
      /____\     
     /      \    Integration Tests (30%)
    /________\   
   /          \  Unit Tests (60%)
  /____________\ 
```

### 2.2 Test Types

| Test Type | Coverage | Tools | Responsibility |
|-----------|----------|-------|----------------|
| Unit Tests | 80%+ | Jest, Testing Library | Developers |
| Integration Tests | All APIs | Supertest, Jest | Developers |
| E2E Tests | Critical Paths | Playwright | QA Team |
| UAT | All Features | Manual | Business Users |
| Performance Tests | Load Scenarios | k6, Artillery | QA Team |
| Security Tests | Vulnerabilities | OWASP ZAP | Security Team |

---

## 3. Unit Testing

### 3.1 Backend Unit Tests

#### 3.1.1 Service Layer Tests

**Test File:** `admission.service.spec.ts`

```typescript
describe('AdmissionService', () => {
  describe('createAdmission', () => {
    it('should create admission successfully', async () => {
      // Test admission creation
    });
    
    it('should throw error if bed not available', async () => {
      // Test bed availability validation
    });
    
    it('should throw error if admission request not approved', async () => {
      // Test approval validation
    });
    
    it('should update bed status to occupied', async () => {
      // Test bed status update
    });
    
    it('should emit admission:created event', async () => {
      // Test event emission
    });
  });
  
  describe('transferBed', () => {
    it('should transfer patient to new bed', async () => {
      // Test bed transfer
    });
    
    it('should throw error if target bed occupied', async () => {
      // Test bed occupancy validation
    });
    
    it('should log transfer history', async () => {
      // Test transfer logging
    });
    
    it('should update billing for inter-ward transfer', async () => {
      // Test billing update
    });
  });
  
  describe('initiateDischarge', () => {
    it('should create discharge summary', async () => {
      // Test discharge summary creation
    });
    
    it('should update admission status to discharge_pending', async () => {
      // Test status update
    });
    
    it('should generate discharge summary PDF', async () => {
      // Test PDF generation
    });
  });
});
```

**Coverage Target:** 90%+

---

#### 3.1.2 Controller Tests

**Test File:** `admission.controller.spec.ts`

```typescript
describe('AdmissionController', () => {
  describe('POST /admissions', () => {
    it('should return 201 on successful admission', async () => {
      // Test successful creation
    });
    
    it('should return 400 for invalid input', async () => {
      // Test validation
    });
    
    it('should return 403 for unauthorized user', async () => {
      // Test authorization
    });
    
    it('should return 409 for bed conflict', async () => {
      // Test conflict handling
    });
  });
});
```

---

### 3.2 Frontend Unit Tests

#### 3.2.1 Component Tests

**Test File:** `AdmissionForm.test.tsx`

```typescript
describe('AdmissionForm', () => {
  it('should render all form fields', () => {
    // Test form rendering
  });
  
  it('should validate required fields', () => {
    // Test validation
  });
  
  it('should call onSubmit with form data', () => {
    // Test form submission
  });
  
  it('should display error messages', () => {
    // Test error display
  });
});
```

---

## 4. Integration Testing

### 4.1 API Integration Tests

#### 4.1.1 Admission Request Flow

```typescript
describe('Admission Request API Integration', () => {
  let authToken: string;
  let patientId: string;
  let requestId: string;
  
  beforeAll(async () => {
    // Setup: Login as doctor, create test patient
  });
  
  it('should create admission request', async () => {
    const response = await request(app)
      .post('/api/admission-requests')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        patientId,
        admissionReason: 'Test reason for admission...',
        provisionalDiagnosis: 'Test diagnosis',
        wardTypePreference: 'general',
        urgencyLevel: 'routine',
        estimatedStayDays: 5,
        admissionOrders: 'Test admission orders...',
      })
      .expect(201);
    
    requestId = response.body.data.id;
    expect(response.body.data.status).toBe('pending');
  });
  
  it('should approve admission request', async () => {
    // Login as nurse
    const nurseToken = await loginAsNurse();
    
    const response = await request(app)
      .post(`/api/admission-requests/${requestId}/approve`)
      .set('Authorization', `Bearer ${nurseToken}`)
      .send({ comments: 'Approved' })
      .expect(200);
    
    expect(response.body.data.status).toBe('approved');
  });
  
  it('should allocate bed and create admission', async () => {
    // Test bed allocation
  });
});
```

---

#### 4.1.2 Bed Allocation Flow

```typescript
describe('Bed Allocation API Integration', () => {
  it('should show available beds', async () => {
    const response = await request(app)
      .get('/api/beds/availability')
      .set('Authorization', `Bearer ${authToken}`)
      .expect(200);
    
    expect(response.body.data.availableBeds.length).toBeGreaterThan(0);
  });
  
  it('should prevent double booking', async () => {
    const bedId = 'test-bed-id';
    
    // First allocation should succeed
    await request(app)
      .post('/api/admissions')
      .send({ bedId, /* ... */ })
      .expect(201);
    
    // Second allocation should fail
    await request(app)
      .post('/api/admissions')
      .send({ bedId, /* ... */ })
      .expect(409);
  });
});
```

---

#### 4.1.3 Discharge Flow

```typescript
describe('Discharge API Integration', () => {
  let admissionId: string;
  
  beforeAll(async () => {
    // Create test admission
  });
  
  it('should initiate discharge', async () => {
    const response = await request(app)
      .post(`/api/admissions/${admissionId}/discharge/initiate`)
      .set('Authorization', `Bearer ${doctorToken}`)
      .send({
        dischargeDate: new Date().toISOString(),
        dischargeType: 'regular',
        finalDiagnosis: 'Test diagnosis',
        hospitalCourse: 'Test hospital course...',
        // ... other fields
      })
      .expect(200);
    
    expect(response.body.data.status).toBe('discharge_pending');
  });
  
  it('should not complete discharge without billing clearance', async () => {
    await request(app)
      .post(`/api/admissions/${admissionId}/discharge/complete`)
      .set('Authorization', `Bearer ${doctorToken}`)
      .send({ billingCleared: false })
      .expect(400);
  });
  
  it('should complete discharge after billing clearance', async () => {
    // Clear billing first
    await clearBilling(admissionId);
    
    const response = await request(app)
      .post(`/api/admissions/${admissionId}/discharge/complete`)
      .set('Authorization', `Bearer ${doctorToken}`)
      .send({ billingCleared: true })
      .expect(200);
    
    expect(response.body.data.status).toBe('discharged');
  });
  
  it('should release bed after discharge', async () => {
    // Check bed status
    const bed = await getBedStatus(bedId);
    expect(bed.status).toBe('available');
  });
});
```

---

### 4.2 Database Integration Tests

```typescript
describe('Database Triggers', () => {
  it('should update bed status on admission', async () => {
    // Test trigger: fn_update_bed_on_admission
  });
  
  it('should release bed on discharge', async () => {
    // Test trigger: fn_release_bed_on_discharge
  });
  
  it('should update admission totals on charge', async () => {
    // Test trigger: fn_update_admission_totals
  });
});
```

---

## 5. End-to-End (E2E) Testing

### 5.1 Critical User Journeys

#### 5.1.1 Complete Admission Journey

```typescript
describe('E2E: Complete Admission Journey', () => {
  test('Doctor creates admission request → Clerk approves → Allocates bed → Patient admitted', async ({ page }) => {
    // 1. Login as Doctor
    await page.goto('/login');
    await page.fill('[name="email"]', 'doctor@test.com');
    await page.fill('[name="password"]', 'password');
    await page.click('button[type="submit"]');
    
    // 2. Navigate to patient consultation
    await page.goto('/consultations/test-visit-id');
    
    // 3. Create admission request
    await page.click('text=Admission Required');
    await page.fill('[name="admissionReason"]', 'Severe abdominal pain...');
    await page.fill('[name="provisionalDiagnosis"]', 'Acute Appendicitis');
    await page.selectOption('[name="wardTypePreference"]', 'general');
    await page.selectOption('[name="urgencyLevel"]', 'urgent');
    await page.fill('[name="admissionOrders"]', 'NPO, IV fluids...');
    await page.click('button:has-text("Submit Request")');
    
    // 4. Verify request created
    await expect(page.locator('text=Admission request created')).toBeVisible();
    
    // 5. Logout and login as Nurse
    await page.click('[data-testid="user-menu"]');
    await page.click('text=Logout');
    await page.fill('[name="email"]', 'nurse@test.com');
    await page.fill('[name="password"]', 'password');
    await page.click('button[type="submit"]');
    
    // 6. Navigate to admission queue
    await page.goto('/admissions/queue');
    
    // 7. Approve request
    await page.click('tr:has-text("Test Patient") button:has-text("View")');
    await page.click('button:has-text("Approve")');
    await page.fill('[name="comments"]', 'Approved for general ward');
    await page.click('button:has-text("Confirm Approval")');
    
    // 8. Allocate bed
    await page.click('button:has-text("Allocate Bed")');
    await page.click('.bed-card:has-text("201-A")');
    await page.click('button:has-text("Confirm Allocation")');
    
    // 9. Verify admission created
    await expect(page.locator('text=Patient admitted successfully')).toBeVisible();
    
    // 10. Verify patient in ward dashboard
    await page.goto('/wards/general-ward-a');
    await expect(page.locator('text=Test Patient')).toBeVisible();
  });
});
```

---

#### 5.1.2 Emergency Admission Journey

```typescript
describe('E2E: Emergency Admission', () => {
  test('Receptionist creates emergency admission → Patient immediately admitted', async ({ page }) => {
    // Test emergency admission flow
  });
});
```

---

#### 5.1.3 Discharge Journey

```typescript
describe('E2E: Discharge Journey', () => {
  test('Doctor initiates discharge → Billing clears → Patient discharged', async ({ page }) => {
    // Test complete discharge flow
  });
});
```

---

## 6. User Acceptance Testing (UAT)

### 6.1 UAT Test Cases

#### 6.1.1 Admission Request Creation

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-001 | Doctor creates routine admission request | 1. Login as doctor<br>2. Open consultation<br>3. Select "Admission Required"<br>4. Fill form<br>5. Submit | Request created with status "Pending" | ⬜ |
| UAT-ADM-002 | Doctor creates urgent admission request | Similar to above with urgency "Urgent" | Request highlighted in queue | ⬜ |
| UAT-ADM-003 | Doctor creates emergency admission request | Similar to above with urgency "Emergency" | Request at top of queue, red highlight | ⬜ |

---

#### 6.1.2 Admission Request Approval

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-004 | Nurse approves admission request | 1. Login as nurse<br>2. View queue<br>3. Select request<br>4. Click Approve<br>5. Add comments<br>6. Confirm | Status changes to "Approved", doctor notified | ⬜ |
| UAT-ADM-005 | Nurse rejects admission request | Similar with Reject | Status "Rejected", doctor notified with reason | ⬜ |
| UAT-ADM-006 | Nurse requests more info | Similar with Request Info | Status "Info Requested", doctor notified | ⬜ |

---

#### 6.1.3 Bed Allocation

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-007 | Nurse allocates bed to approved admission | 1. Select approved admission<br>2. View available beds<br>3. Select bed<br>4. Confirm | Patient admitted, bed occupied, appears in ward | ⬜ |
| UAT-ADM-008 | System prevents double booking | Try to allocate already occupied bed | Error message, allocation fails | ⬜ |
| UAT-ADM-009 | Filter beds by ward type | Apply ward type filter | Only matching beds shown | ⬜ |

---

#### 6.1.4 Ward Patient Management

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-010 | Nurse views ward patient list | 1. Login as nurse<br>2. Navigate to ward dashboard | All patients in ward visible | ⬜ |
| UAT-ADM-011 | Nurse views patient details | Click on patient | Full patient details displayed | ⬜ |
| UAT-ADM-012 | Nurse searches patient by name | Enter patient name in search | Matching patients shown | ⬜ |

---

#### 6.1.5 Bed Transfer

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-013 | Nurse transfers patient within ward | 1. Select patient<br>2. Click Transfer<br>3. Select new bed<br>4. Enter reason<br>5. Confirm | Patient moved, old bed available, new bed occupied | ⬜ |
| UAT-ADM-014 | Nurse transfers patient to different ward | Similar with different ward | Doctor approval required, transfer logged | ⬜ |

---

#### 6.1.6 Discharge Process

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-015 | Doctor initiates regular discharge | 1. Select patient<br>2. Click Discharge<br>3. Fill discharge summary<br>4. Submit | Status "Discharge Pending", summary PDF generated | ⬜ |
| UAT-ADM-016 | Reception clears final bill | 1. View discharge pending patients<br>2. Generate final bill<br>3. Collect payment<br>4. Mark cleared | Billing cleared, ready for discharge | ⬜ |
| UAT-ADM-017 | System completes discharge | Click Complete Discharge | Patient discharged, bed released | ⬜ |
| UAT-ADM-018 | Doctor initiates LAMA discharge | Select LAMA type, upload signed form | LAMA form attached, discharge processed | ⬜ |

---

#### 6.1.7 Billing Integration

| Test Case ID | Description | Steps | Expected Result | Status |
|-------------|-------------|-------|-----------------|--------|
| UAT-ADM-019 | Reception adds admission fee | Patient admitted | Admission fee added to charges | ⬜ |
| UAT-ADM-020 | System auto-adds daily room rent | Midnight trigger | Room rent added for each day | ⬜ |
| UAT-ADM-021 | Reception adds service charge | Add investigation charge | Charge added, total updated | ⬜ |
| UAT-ADM-022 | Reception collects deposit | Collect deposit | Deposit recorded, receipt generated, balance updated | ⬜ |
| UAT-ADM-023 | System calculates final bill | Generate final bill | All charges included, deposits adjusted, balance calculated | ⬜ |

---

### 6.2 UAT Execution Plan

#### Phase 1: Training (Week 1)
- Train UAT team on system features
- Provide test accounts and data
- Walkthrough test scenarios

#### Phase 2: Execution (Week 2-3)
- Execute all test cases
- Document issues/bugs
- Retest after fixes

#### Phase 3: Sign-off (Week 4)
- Review all test results
- Obtain stakeholder approval
- Document acceptance

---

### 6.3 UAT Acceptance Criteria

Module is accepted when:
- [ ] 100% of P0 test cases passed
- [ ] 95%+ of P1 test cases passed
- [ ] No critical or high-priority bugs open
- [ ] All stakeholders sign-off
- [ ] User documentation complete
- [ ] Training completed

---

## 7. Performance Testing

### 7.1 Load Testing Scenarios

#### 7.1.1 Concurrent Bed Allocation

**Scenario:** 10 nurses allocating beds simultaneously

**Test Script (k6):**
```javascript
import http from 'k6/http';
import { check } from 'k6';

export let options = {
  vus: 10, // 10 virtual users
  duration: '5m',
};

export default function () {
  const url = 'https://api.medflow.com/api/admissions';
  const payload = JSON.stringify({
    admissionRequestId: 'test-request-id',
    bedId: 'test-bed-id',
    // ... other fields
  });
  
  const params = {
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${__ENV.AUTH_TOKEN}`,
    },
  };
  
  const res = http.post(url, payload, params);
  
  check(res, {
    'status is 201': (r) => r.status === 201,
    'response time < 2000ms': (r) => r.timings.duration < 2000,
  });
}
```

**Acceptance Criteria:**
- 95th percentile response time < 2 seconds
- 0% error rate
- No bed double-booking

---

#### 7.1.2 Ward Dashboard Load

**Scenario:** 50 nurses viewing ward dashboards simultaneously

**Acceptance Criteria:**
- Page load time < 3 seconds
- Real-time updates within 2 seconds
- No UI freezing

---

#### 7.1.3 Discharge Processing

**Scenario:** 20 doctors initiating discharges simultaneously

**Acceptance Criteria:**
- PDF generation < 5 seconds
- Database updates < 1 second
- No data corruption

---

### 7.2 Stress Testing

**Scenario:** Gradually increase load until system breaks

**Goal:** Identify breaking point and bottlenecks

**Metrics:**
- Maximum concurrent users
- Maximum requests per second
- Resource utilization at breaking point

---

## 8. Security Testing

### 8.1 Authentication & Authorization

| Test Case | Description | Expected Result |
|-----------|-------------|-----------------|
| SEC-001 | Access API without token | 401 Unauthorized |
| SEC-002 | Access with expired token | 401 Unauthorized |
| SEC-003 | Access endpoint without permission | 403 Forbidden |
| SEC-004 | Doctor tries to approve admission request | 403 Forbidden |
| SEC-005 | Nurse tries to view other ward patients | No data returned |

---

### 8.2 Data Security

| Test Case | Description | Expected Result |
|-----------|-------------|-----------------|
| SEC-006 | SQL injection in search | No SQL execution |
| SEC-007 | XSS in admission notes | HTML escaped |
| SEC-008 | CSRF attack on admission creation | Request rejected |
| SEC-009 | Access patient data without authorization | 403 Forbidden |

---

### 8.3 OWASP Top 10 Testing

- [ ] Injection vulnerabilities
- [ ] Broken authentication
- [ ] Sensitive data exposure
- [ ] XML external entities (XXE)
- [ ] Broken access control
- [ ] Security misconfiguration
- [ ] Cross-site scripting (XSS)
- [ ] Insecure deserialization
- [ ] Using components with known vulnerabilities
- [ ] Insufficient logging & monitoring

---

## 9. Regression Testing

### 9.1 Phase 1 Integration

Ensure IPD module doesn't break existing OPD features:

- [ ] Patient registration still works
- [ ] OPD consultations still work
- [ ] Billing still works for OPD
- [ ] Prescriptions still work
- [ ] Lab orders still work

---

## 10. Test Data Management

### 10.1 Test Data Requirements

**Master Data:**
- 5 wards (various types)
- 20 rooms
- 50 beds
- 10 services (for billing)

**Transactional Data:**
- 50 test patients
- 20 test users (various roles)
- 10 active admissions
- 5 discharge pending admissions

---

### 10.2 Test Data Reset

Before each test cycle:
```sql
-- Reset admissions
DELETE FROM discharge_summaries;
DELETE FROM deposits;
DELETE FROM admission_charges;
DELETE FROM bed_transfers;
DELETE FROM admissions;
DELETE FROM admission_requests;

-- Reset bed status
UPDATE beds SET status = 'available', current_admission_id = NULL;

-- Reset sequences
ALTER SEQUENCE seq_admission_number RESTART WITH 1;
```

---

## 11. Bug Tracking

### 11.1 Bug Severity Levels

| Severity | Description | Example | SLA |
|----------|-------------|---------|-----|
| Critical | System crash, data loss | Bed double-booked | 4 hours |
| High | Feature broken, no workaround | Cannot create admission | 24 hours |
| Medium | Feature broken, workaround exists | Filter not working | 3 days |
| Low | Minor UI issue | Button alignment | 1 week |

---

### 11.2 Bug Report Template

```markdown
**Bug ID:** BUG-ADM-001
**Title:** Bed double-booking possible
**Severity:** Critical
**Priority:** P0

**Description:**
Two clerks can allocate the same bed simultaneously.

**Steps to Reproduce:**
1. Login as clerk 1 and clerk 2
2. Both select same admission request
3. Both select same bed
4. Both click confirm
5. Both admissions created with same bed

**Expected Result:**
Second allocation should fail with error.

**Actual Result:**
Both allocations succeed, bed double-booked.

**Environment:**
- Browser: Chrome 120
- OS: Windows 11
- Server: Staging

**Screenshots:**
[Attach screenshots]

**Logs:**
[Attach relevant logs]
```

---

## 12. Test Automation

### 12.1 CI/CD Integration

```yaml
# .github/workflows/test.yml
name: Test Suite

on: [push, pull_request]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run unit tests
        run: npm run test:unit
      - name: Upload coverage
        uses: codecov/codecov-action@v2
  
  integration-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Start services
        run: docker-compose up -d
      - name: Run integration tests
        run: npm run test:integration
  
  e2e-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run E2E tests
        run: npm run test:e2e
```

---

## 13. Test Metrics & Reporting

### 13.1 Key Metrics

- **Test Coverage:** 80%+ code coverage
- **Pass Rate:** 95%+ tests passing
- **Defect Density:** < 5 defects per 1000 LOC
- **Mean Time to Detect (MTTD):** < 24 hours
- **Mean Time to Resolve (MTTR):** < 48 hours

---

### 13.2 Test Report Template

```markdown
# Test Execution Report - Phase 2 IPD Admission

**Date:** 2026-03-15
**Sprint:** Sprint 4
**Test Cycle:** UAT Cycle 1

## Summary
- Total Test Cases: 150
- Passed: 142 (94.7%)
- Failed: 5 (3.3%)
- Blocked: 3 (2%)

## Test Coverage
- Unit Tests: 85%
- Integration Tests: 100% APIs covered
- E2E Tests: All critical paths covered

## Defects
- Critical: 0
- High: 2
- Medium: 3
- Low: 5

## Recommendations
- Fix high-priority bugs before release
- Increase unit test coverage to 90%
- Add more E2E tests for edge cases
```

---

> **Note:** This testing strategy should be reviewed and updated throughout the development cycle.
