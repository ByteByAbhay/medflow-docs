# 📦 Phase 2: IPD Admission - Data Migration Plan

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document outlines the **data migration strategy** for Phase 2, including master data setup, data validation, and migration procedures.

---

## 2. Migration Overview

### 2.1 Migration Type

**Type:** Fresh Installation (No legacy data migration)

**Reason:**
- Phase 2 introduces completely new functionality (IPD)
- No existing IPD data to migrate
- Only master data setup required

### 2.2 Data Categories

| Category | Type | Source | Destination |
|----------|------|--------|-------------|
| Ward Configuration | Master Data | Manual Entry | `wards` table |
| Room Configuration | Master Data | Manual Entry | `rooms` table |
| Bed Configuration | Master Data | Manual Entry | `beds` table |
| Service Catalog | Master Data | Existing + New | `services` table |
| User Roles | Configuration | Existing + New | `user_roles` table |

---

## 3. Master Data Setup

### 3.1 Ward Configuration

**Data Collection Template:**

```csv
ward_name,ward_code,ward_type,floor_number,department,total_capacity,gender_restriction
General Ward A,GWA,general,2,General Medicine,40,mixed
General Ward B,GWB,general,2,General Medicine,40,mixed
Private Ward,PVT,private,3,General Medicine,20,mixed
ICU,ICU,icu,3,Critical Care,10,mixed
Emergency Ward,EMG,emergency,1,Emergency,15,mixed
Maternity Ward,MAT,maternity,4,Obstetrics & Gynecology,30,female
Pediatric Ward,PED,pediatric,4,Pediatrics,25,mixed
```

**Import Script:**
```sql
COPY wards (name, code, ward_type, floor_number, department, total_capacity, gender_restriction)
FROM '/path/to/wards.csv'
DELIMITER ','
CSV HEADER;
```

**Validation:**
```sql
-- Check ward count
SELECT COUNT(*) FROM wards;
-- Expected: 7

-- Check for duplicates
SELECT code, COUNT(*) 
FROM wards 
GROUP BY code 
HAVING COUNT(*) > 1;
-- Expected: 0 rows

-- Check data types
SELECT * FROM wards WHERE ward_type NOT IN ('general', 'private', 'icu', 'emergency', 'pediatric', 'maternity');
-- Expected: 0 rows
```

---

### 3.2 Room Configuration

**Data Collection Template:**

```csv
room_number,ward_code,room_type,bed_capacity,rate_per_day,amenities
201,GWA,double,2,1500,"[""ac"",""tv""]"
202,GWA,double,2,1500,"[""ac"",""tv""]"
203,GWA,single,1,2500,"[""ac"",""tv"",""attached_bath""]"
204,GWA,triple,3,1200,"[""ac""]"
...
```

**Import Script:**
```sql
-- First, create temporary table
CREATE TEMP TABLE temp_rooms (
  room_number VARCHAR(20),
  ward_code VARCHAR(5),
  room_type VARCHAR(50),
  bed_capacity INTEGER,
  rate_per_day DECIMAL(10,2),
  amenities JSONB
);

-- Import CSV
COPY temp_rooms FROM '/path/to/rooms.csv' DELIMITER ',' CSV HEADER;

-- Insert into rooms table with ward_id lookup
INSERT INTO rooms (room_number, ward_id, room_type, bed_capacity, rate_per_day, amenities)
SELECT 
  tr.room_number,
  w.id,
  tr.room_type,
  tr.bed_capacity,
  tr.rate_per_day,
  tr.amenities
FROM temp_rooms tr
JOIN wards w ON w.code = tr.ward_code;

-- Drop temp table
DROP TABLE temp_rooms;
```

**Validation:**
```sql
-- Check room count per ward
SELECT w.name, COUNT(r.id) as room_count
FROM wards w
LEFT JOIN rooms r ON r.ward_id = w.id
GROUP BY w.name;

-- Check for rooms without ward
SELECT * FROM rooms WHERE ward_id IS NULL;
-- Expected: 0 rows

-- Check room rates are positive
SELECT * FROM rooms WHERE rate_per_day <= 0;
-- Expected: 0 rows
```

---

### 3.3 Bed Configuration

**Data Collection Template:**

```csv
bed_number,ward_code,room_number,bed_type
201-A,GWA,201,standard
201-B,GWA,201,standard
202-A,GWA,202,standard
202-B,GWA,202,standard
203-A,GWA,203,standard
...
ICU-1-A,ICU,ICU-1,icu
ICU-1-B,ICU,ICU-1,icu
...
```

**Import Script:**
```sql
-- Create temporary table
CREATE TEMP TABLE temp_beds (
  bed_number VARCHAR(20),
  ward_code VARCHAR(5),
  room_number VARCHAR(20),
  bed_type VARCHAR(50)
);

-- Import CSV
COPY temp_beds FROM '/path/to/beds.csv' DELIMITER ',' CSV HEADER;

-- Insert into beds table
INSERT INTO beds (bed_number, room_id, bed_type, status)
SELECT 
  tb.bed_number,
  r.id,
  tb.bed_type,
  'available'
FROM temp_beds tb
JOIN wards w ON w.code = tb.ward_code
JOIN rooms r ON r.ward_id = w.id AND r.room_number = tb.room_number;

-- Drop temp table
DROP TABLE temp_beds;
```

**Validation:**
```sql
-- Check bed count per ward
SELECT w.name, COUNT(b.id) as bed_count
FROM wards w
JOIN rooms r ON r.ward_id = w.id
JOIN beds b ON b.room_id = r.id
GROUP BY w.name;

-- Verify all beds are available
SELECT * FROM beds WHERE status != 'available';
-- Expected: 0 rows

-- Check bed capacity matches room capacity
SELECT 
  r.room_number,
  r.bed_capacity,
  COUNT(b.id) as actual_beds
FROM rooms r
LEFT JOIN beds b ON b.room_id = r.id
GROUP BY r.id, r.room_number, r.bed_capacity
HAVING COUNT(b.id) != r.bed_capacity;
-- Expected: 0 rows
```

---

### 3.4 Service Catalog Update

**New Services for IPD:**

```sql
INSERT INTO services (name, code, category, price, is_active) VALUES
-- Admission
('Admission Fee - General', 'ADM_GEN', 'admission', 1000.00, true),
('Admission Fee - ICU', 'ADM_ICU', 'admission', 2000.00, true),
('Admission Fee - Private', 'ADM_PVT', 'admission', 1500.00, true),

-- Room Rent (already in template, but for reference)
('Room Rent - General Double', 'ROOM_GEN_DBL', 'room_rent', 1500.00, true),
('Room Rent - General Single', 'ROOM_GEN_SGL', 'room_rent', 2500.00, true),
('Room Rent - General Triple', 'ROOM_GEN_TRP', 'room_rent', 1200.00, true),
('Room Rent - ICU', 'ROOM_ICU', 'room_rent', 5000.00, true),
('Room Rent - Private', 'ROOM_PVT', 'room_rent', 3500.00, true),

-- Nursing
('Nursing Charges - General', 'NURS_GEN', 'nursing', 500.00, true),
('Nursing Charges - ICU', 'NURS_ICU', 'nursing', 1000.00, true),

-- Procedures
('Bed Transfer Fee', 'BED_TRANS', 'procedure', 200.00, true),
('Discharge Summary', 'DISCH_SUM', 'documentation', 100.00, true);
```

**Validation:**
```sql
-- Check new services added
SELECT * FROM services WHERE code LIKE 'ADM_%' OR code LIKE 'ROOM_%' OR code LIKE 'NURS_%';

-- Verify all prices are positive
SELECT * FROM services WHERE price <= 0;
-- Expected: 0 rows
```

---

## 4. User Role Assignment

### 4.1 New Permissions

**Add IPD permissions to existing roles:**

```sql
-- Nurse role permissions
INSERT INTO role_permissions (role, permission) VALUES
('NURSE', 'admission-request:view'),
('NURSE', 'admission-request:approve'),
('ADMISSION_CLERK', 'admission:view'),
('ADMISSION_CLERK', 'admission:create'),
('ADMISSION_CLERK', 'admission:emergency'),
('ADMISSION_CLERK', 'bed:view'),
('ADMISSION_CLERK', 'bed:availability'),
('NURSE', 'bed:update'),
('NURSE', 'billing:deposit'),
('NURSE', 'admission:view-ward'),
('NURSE', 'admission:update'),
('WARD_NURSE', 'admission:transfer'),
('WARD_NURSE', 'bed:view'),
('WARD_NURSE', 'bed:status'),
('WARD_NURSE', 'discharge:view');

-- Doctor role permissions
INSERT INTO role_permissions (role, permission) VALUES
('DOCTOR', 'admission-request:view-own'),
('DOCTOR', 'admission-request:create'),
('DOCTOR', 'admission:view-own'),
('DOCTOR', 'admission:update'),
('DOCTOR', 'admission:discharge'),
('DOCTOR', 'discharge:initiate'),
('DOCTOR', 'discharge:summary');

-- Reception role permissions
INSERT INTO role_permissions (role, permission) VALUES
('RECEPTION', 'admission:view'),
('RECEPTION', 'billing:view'),
('BILLING_STAFF', 'billing:charges-view'),
('BILLING_STAFF', 'billing:charges-create'),
('BILLING_STAFF', 'billing:deposit'),
('BILLING_STAFF', 'billing:invoice'),
('BILLING_STAFF', 'billing:payment'),
('BILLING_STAFF', 'discharge:complete');
```

---

### 4.2 Ward Assignment

**Assign nurses to wards:**

```sql
-- Example: Assign Nurse 1 to General Ward A
INSERT INTO user_ward_assignments (user_id, ward_id)
SELECT 
  u.id,
  w.id
FROM users u
CROSS JOIN wards w
WHERE u.email = 'nurse1@hospital.com'
AND w.code = 'GWA';

-- Bulk assignment (if needed)
-- Create CSV with user_email, ward_code
-- Import and process similar to rooms/beds
```

---

## 5. Data Validation Checklist

### 5.1 Pre-Migration Validation

- [ ] Ward data collected and validated
- [ ] Room data collected and validated
- [ ] Bed data collected and validated
- [ ] Service catalog updated
- [ ] User roles and permissions defined
- [ ] Ward assignments prepared
- [ ] CSV files formatted correctly
- [ ] Import scripts tested on staging

---

### 5.2 Post-Migration Validation

**Automated Validation Script:**

```sql
-- Run comprehensive validation
DO $$
DECLARE
  ward_count INTEGER;
  room_count INTEGER;
  bed_count INTEGER;
  error_count INTEGER := 0;
BEGIN
  -- Check ward count
  SELECT COUNT(*) INTO ward_count FROM wards;
  IF ward_count = 0 THEN
    RAISE NOTICE 'ERROR: No wards found';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: % wards found', ward_count;
  END IF;
  
  -- Check room count
  SELECT COUNT(*) INTO room_count FROM rooms;
  IF room_count = 0 THEN
    RAISE NOTICE 'ERROR: No rooms found';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: % rooms found', room_count;
  END IF;
  
  -- Check bed count
  SELECT COUNT(*) INTO bed_count FROM beds;
  IF bed_count = 0 THEN
    RAISE NOTICE 'ERROR: No beds found';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: % beds found', bed_count;
  END IF;
  
  -- Check all beds are available
  IF EXISTS (SELECT 1 FROM beds WHERE status != 'available') THEN
    RAISE NOTICE 'ERROR: Some beds are not available';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: All beds are available';
  END IF;
  
  -- Check foreign key integrity
  IF EXISTS (SELECT 1 FROM rooms WHERE ward_id IS NULL) THEN
    RAISE NOTICE 'ERROR: Some rooms have no ward';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: All rooms have ward';
  END IF;
  
  IF EXISTS (SELECT 1 FROM beds WHERE room_id IS NULL) THEN
    RAISE NOTICE 'ERROR: Some beds have no room';
    error_count := error_count + 1;
  ELSE
    RAISE NOTICE 'OK: All beds have room';
  END IF;
  
  -- Summary
  IF error_count = 0 THEN
    RAISE NOTICE '=== VALIDATION PASSED ===';
  ELSE
    RAISE NOTICE '=== VALIDATION FAILED: % errors ===', error_count;
  END IF;
END $$;
```

**Manual Validation:**

- [ ] Login to system
- [ ] Navigate to ward management
- [ ] Verify all wards visible
- [ ] Navigate to bed availability
- [ ] Verify all beds visible and available
- [ ] Create test admission request
- [ ] Approve test admission request
- [ ] Allocate test bed
- [ ] Verify patient appears in ward dashboard
- [ ] Delete test data

---

## 6. Rollback Procedures

### 6.1 Rollback Master Data

**If master data import fails:**

```sql
-- Rollback in reverse order of dependencies

-- Delete beds
DELETE FROM beds;
ALTER SEQUENCE beds_id_seq RESTART WITH 1;

-- Delete rooms
DELETE FROM rooms;
ALTER SEQUENCE rooms_id_seq RESTART WITH 1;

-- Delete wards
DELETE FROM wards;
ALTER SEQUENCE wards_id_seq RESTART WITH 1;

-- Delete new services
DELETE FROM services WHERE code LIKE 'ADM_%' OR code LIKE 'ROOM_%' OR code LIKE 'NURS_%';

-- Delete role permissions
DELETE FROM role_permissions WHERE permission LIKE 'admission%' OR permission LIKE 'discharge%';

-- Delete ward assignments
DELETE FROM user_ward_assignments;
```

---

## 7. Migration Timeline

### 7.1 Staging Environment

| Step | Duration | Responsible | Status |
|------|----------|-------------|--------|
| Collect ward data | 1 day | Hospital Admin | ⬜ |
| Collect room data | 2 days | Hospital Admin | ⬜ |
| Collect bed data | 2 days | Hospital Admin | ⬜ |
| Prepare CSV files | 1 day | Dev Team | ⬜ |
| Test import scripts | 1 day | Dev Team | ⬜ |
| Run migration on staging | 1 hour | Dev Team | ⬜ |
| Validate data | 2 hours | QA Team | ⬜ |
| Fix issues | 1 day | Dev Team | ⬜ |
| Re-validate | 1 hour | QA Team | ⬜ |

**Total:** ~7 days

---

### 7.2 Production Environment

| Step | Duration | Responsible | Status |
|------|----------|-------------|--------|
| Final data review | 1 day | Hospital Admin | ⬜ |
| Backup production DB | 30 min | Ops Team | ⬜ |
| Run database migrations | 15 min | Dev Team | ⬜ |
| Import master data | 30 min | Dev Team | ⬜ |
| Validate data | 30 min | QA Team | ⬜ |
| Smoke testing | 30 min | QA Team | ⬜ |

**Total:** ~2.5 hours (within maintenance window)

---

## 8. Data Quality Assurance

### 8.1 Data Quality Checks

**Ward Data:**
- [ ] Ward codes are unique
- [ ] Ward types are valid
- [ ] Floor numbers are positive
- [ ] Total capacity matches actual beds
- [ ] Gender restrictions are valid

**Room Data:**
- [ ] Room numbers are unique within ward
- [ ] Room types are valid
- [ ] Bed capacity is positive
- [ ] Room rates are positive
- [ ] All rooms have valid ward

**Bed Data:**
- [ ] Bed numbers are unique within room
- [ ] Bed types are valid
- [ ] All beds have valid room
- [ ] Bed count matches room capacity
- [ ] All beds are initially available

---

### 8.2 Data Consistency Checks

```sql
-- Check ward capacity matches bed count
SELECT 
  w.name,
  w.total_capacity as expected_beds,
  COUNT(b.id) as actual_beds,
  w.total_capacity - COUNT(b.id) as difference
FROM wards w
JOIN rooms r ON r.ward_id = w.id
JOIN beds b ON b.room_id = r.id
GROUP BY w.id, w.name, w.total_capacity
HAVING w.total_capacity != COUNT(b.id);
-- Expected: 0 rows

-- Check room capacity matches bed count
SELECT 
  r.room_number,
  r.bed_capacity as expected_beds,
  COUNT(b.id) as actual_beds,
  r.bed_capacity - COUNT(b.id) as difference
FROM rooms r
LEFT JOIN beds b ON b.room_id = r.id
GROUP BY r.id, r.room_number, r.bed_capacity
HAVING r.bed_capacity != COUNT(b.id);
-- Expected: 0 rows
```

---

## 9. Training Data Setup

### 9.1 Test Patients (Staging Only)

**Create realistic test data:**

```sql
-- Use faker library or similar to generate test patients
-- Example:
INSERT INTO patients (uhid, name, age, gender, phone, address) VALUES
('MF-2026-TEST001', 'Test Patient 1', 45, 'male', '1234567890', 'Test Address 1'),
('MF-2026-TEST002', 'Test Patient 2', 32, 'female', '1234567891', 'Test Address 2'),
-- ... 50 test patients
```

---

### 9.2 Test Admissions (Staging Only)

**Create sample admissions for training:**

```sql
-- Create 10 active admissions across different wards
-- This helps users practice with realistic scenarios
```

---

## 10. Documentation

### 10.1 Master Data Documentation

**Create documentation:**
- Ward layout diagram (floor plan)
- Room numbering convention
- Bed numbering convention
- Service catalog with prices
- User role matrix

**Store in:**
- `/docs/master-data/ward-layout.pdf`
- `/docs/master-data/room-numbering.md`
- `/docs/master-data/service-catalog.xlsx`

---

### 10.2 Migration Log

**Maintain migration log:**

```markdown
# Phase 2 Migration Log

## Staging Migration
- Date: 2026-XX-XX
- Duration: 2 hours
- Status: Success
- Wards: 7
- Rooms: 85
- Beds: 200
- Issues: None

## Production Migration
- Date: 2026-XX-XX
- Duration: 2.5 hours
- Status: Success
- Wards: 7
- Rooms: 85
- Beds: 200
- Issues: None
```

---

> **Note:** This migration plan assumes fresh installation. If legacy IPD data exists, a separate data migration strategy will be required.
