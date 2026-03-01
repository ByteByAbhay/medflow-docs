# 🗄️ Phase 2: IPD Admission - Database Schema

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document defines the **database schema** for the IPD Admission Module, including tables, columns, relationships, indexes, and constraints.

---

## 2. Schema Overview

### 2.1 New Tables

| Table Name | Purpose | Relationships |
|------------|---------|---------------|
| `wards` | Hospital ward master data | → rooms |
| `rooms` | Room configuration within wards | → beds, ward |
| `beds` | Individual bed tracking | → room, admissions |
| `admission_requests` | Admission requests from doctors | → patients, users, visits |
| `admissions` | Active and historical admissions | → patients, beds, users |
| `bed_transfers` | Bed transfer history | → admissions, beds |
| `admission_charges` | Charges during admission | → admissions, services |
| `deposits` | Admission deposits | → admissions, users |
| `discharge_summaries` | Discharge documentation | → admissions, users |

### 2.2 Modified Tables

| Table Name | Modifications | Reason |
|------------|---------------|--------|
| `visits` | Add `admission_request_id` (nullable) | Link visit to admission |
| `invoices` | Add `admission_id` (nullable) | Link invoice to admission |
| `consultations` | Add `admission_decision` (JSONB) | Store admission decision details |

---

## 3. Detailed Table Schemas

### 3.1 Wards Table

```sql
CREATE TABLE wards (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Ward Details
  name VARCHAR(100) NOT NULL UNIQUE,
  code VARCHAR(5) NOT NULL UNIQUE,
  ward_type VARCHAR(50) NOT NULL, -- 'general', 'private', 'icu', 'emergency', 'pediatric', 'maternity'
  
  -- Location
  floor_number INTEGER NOT NULL,
  department VARCHAR(100) NOT NULL,
  
  -- Capacity
  total_capacity INTEGER NOT NULL,
  
  -- Restrictions
  gender_restriction VARCHAR(20) DEFAULT 'mixed', -- 'male', 'female', 'mixed'
  
  -- Status
  is_active BOOLEAN DEFAULT true,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id),
  updated_by UUID REFERENCES users(id)
);

-- Indexes
CREATE INDEX idx_wards_ward_type ON wards(ward_type);
CREATE INDEX idx_wards_is_active ON wards(is_active);
CREATE INDEX idx_wards_floor ON wards(floor_number);

-- Constraints
ALTER TABLE wards ADD CONSTRAINT chk_ward_code_format 
  CHECK (code ~ '^[A-Z0-9]{3,5}$');
ALTER TABLE wards ADD CONSTRAINT chk_ward_type 
  CHECK (ward_type IN ('general', 'private', 'icu', 'emergency', 'pediatric', 'maternity'));
ALTER TABLE wards ADD CONSTRAINT chk_gender_restriction 
  CHECK (gender_restriction IN ('male', 'female', 'mixed'));
```

---

### 3.2 Rooms Table

```sql
CREATE TABLE rooms (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Room Details
  room_number VARCHAR(20) NOT NULL,
  ward_id UUID NOT NULL REFERENCES wards(id) ON DELETE RESTRICT,
  room_type VARCHAR(50) NOT NULL, -- 'single', 'double', 'triple', 'general', 'icu'
  
  -- Capacity
  bed_capacity INTEGER NOT NULL CHECK (bed_capacity BETWEEN 1 AND 10),
  
  -- Pricing
  rate_per_day DECIMAL(10, 2) NOT NULL,
  
  -- Amenities
  amenities JSONB DEFAULT '[]', -- ["ac", "tv", "attached_bath", "wifi"]
  
  -- Status
  is_active BOOLEAN DEFAULT true,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id),
  updated_by UUID REFERENCES users(id),
  
  -- Unique constraint
  UNIQUE(ward_id, room_number)
);

-- Indexes
CREATE INDEX idx_rooms_ward_id ON rooms(ward_id);
CREATE INDEX idx_rooms_room_type ON rooms(room_type);
CREATE INDEX idx_rooms_is_active ON rooms(is_active);

-- Constraints
ALTER TABLE rooms ADD CONSTRAINT chk_room_type 
  CHECK (room_type IN ('single', 'double', 'triple', 'general', 'icu'));
ALTER TABLE rooms ADD CONSTRAINT chk_rate_positive 
  CHECK (rate_per_day >= 0);
```

---

### 3.3 Beds Table

```sql
CREATE TABLE beds (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Bed Details
  bed_number VARCHAR(20) NOT NULL,
  room_id UUID NOT NULL REFERENCES rooms(id) ON DELETE RESTRICT,
  bed_type VARCHAR(50) NOT NULL, -- 'standard', 'electric', 'icu', 'pediatric'
  
  -- Status
  status VARCHAR(50) NOT NULL DEFAULT 'available', -- 'available', 'occupied', 'reserved', 'under_maintenance'
  
  -- Current Occupancy (denormalized for quick access)
  current_admission_id UUID REFERENCES admissions(id) ON DELETE SET NULL,
  occupied_since TIMESTAMP,
  
  -- Active Status
  is_active BOOLEAN DEFAULT true,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id),
  updated_by UUID REFERENCES users(id),
  
  -- Unique constraint
  UNIQUE(room_id, bed_number)
);

-- Indexes
CREATE INDEX idx_beds_room_id ON beds(room_id);
CREATE INDEX idx_beds_status ON beds(status);
CREATE INDEX idx_beds_is_active ON beds(is_active);
CREATE INDEX idx_beds_current_admission ON beds(current_admission_id);

-- Constraints
ALTER TABLE beds ADD CONSTRAINT chk_bed_type 
  CHECK (bed_type IN ('standard', 'electric', 'icu', 'pediatric'));
ALTER TABLE beds ADD CONSTRAINT chk_bed_status 
  CHECK (status IN ('available', 'occupied', 'reserved', 'under_maintenance'));
```

---

### 3.4 Admission Requests Table

```sql
CREATE TABLE admission_requests (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Request Details
  request_number VARCHAR(50) NOT NULL UNIQUE, -- ADM-REQ-2026-00001
  patient_id UUID NOT NULL REFERENCES patients(id) ON DELETE RESTRICT,
  visit_id UUID REFERENCES visits(id) ON DELETE SET NULL,
  
  -- Requesting Doctor
  requested_by UUID NOT NULL REFERENCES users(id), -- Doctor
  requested_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  -- Admission Details
  admission_reason TEXT NOT NULL,
  provisional_diagnosis TEXT NOT NULL,
  ward_type_preference VARCHAR(50) NOT NULL, -- 'general', 'private', 'icu', 'emergency'
  urgency_level VARCHAR(50) NOT NULL, -- 'routine', 'urgent', 'emergency'
  estimated_stay_days INTEGER,
  
  -- Clinical Information
  admission_orders TEXT NOT NULL,
  special_requirements TEXT,
  diet_instructions TEXT,
  
  -- Approval
  status VARCHAR(50) NOT NULL DEFAULT 'pending', -- 'pending', 'approved', 'rejected', 'info_requested', 'completed'
  reviewed_by UUID REFERENCES users(id),
  reviewed_at TIMESTAMP,
  rejection_reason TEXT,
  review_comments TEXT,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_admission_requests_patient ON admission_requests(patient_id);
CREATE INDEX idx_admission_requests_visit ON admission_requests(visit_id);
CREATE INDEX idx_admission_requests_requested_by ON admission_requests(requested_by);
CREATE INDEX idx_admission_requests_status ON admission_requests(status);
CREATE INDEX idx_admission_requests_urgency ON admission_requests(urgency_level);
CREATE INDEX idx_admission_requests_requested_at ON admission_requests(requested_at);

-- Constraints
ALTER TABLE admission_requests ADD CONSTRAINT chk_ward_type_preference 
  CHECK (ward_type_preference IN ('general', 'private', 'icu', 'emergency', 'pediatric', 'maternity'));
ALTER TABLE admission_requests ADD CONSTRAINT chk_urgency_level 
  CHECK (urgency_level IN ('routine', 'urgent', 'emergency'));
ALTER TABLE admission_requests ADD CONSTRAINT chk_status 
  CHECK (status IN ('pending', 'approved', 'rejected', 'info_requested', 'completed'));
ALTER TABLE admission_requests ADD CONSTRAINT chk_admission_reason_length 
  CHECK (LENGTH(admission_reason) >= 20);
ALTER TABLE admission_requests ADD CONSTRAINT chk_admission_orders_length 
  CHECK (LENGTH(admission_orders) >= 50);
```

---

### 3.5 Admissions Table

```sql
CREATE TABLE admissions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Admission Number
  admission_number VARCHAR(50) NOT NULL UNIQUE, -- ADM-2026-00001
  
  -- Patient & Request
  patient_id UUID NOT NULL REFERENCES patients(id) ON DELETE RESTRICT,
  admission_request_id UUID REFERENCES admission_requests(id) ON DELETE SET NULL,
  visit_id UUID REFERENCES visits(id) ON DELETE SET NULL,
  
  -- Bed Allocation
  bed_id UUID NOT NULL REFERENCES beds(id) ON DELETE RESTRICT,
  
  -- Admission Details
  admission_type VARCHAR(50) NOT NULL, -- 'planned', 'emergency', 'transfer'
  admission_source VARCHAR(50) NOT NULL, -- 'opd', 'emergency', 'direct'
  admission_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expected_discharge_date DATE,
  
  -- Clinical Information
  admission_reason TEXT NOT NULL,
  provisional_diagnosis TEXT NOT NULL,
  admission_orders TEXT NOT NULL,
  special_requirements TEXT,
  diet_instructions TEXT,
  
  -- Vitals at Admission
  vitals_at_admission JSONB, -- { bp, pulse, temp, spo2, weight, height }
  
  -- Medical Information
  current_medications JSONB, -- [{ name, dosage, frequency }]
  allergies JSONB, -- [{ allergen, reaction, severity }]
  
  -- Doctors
  referring_doctor_id UUID REFERENCES users(id),
  admitting_doctor_id UUID NOT NULL REFERENCES users(id),
  attending_doctor_id UUID NOT NULL REFERENCES users(id),
  
  -- Consent
  admission_consent BOOLEAN DEFAULT false,
  treatment_consent BOOLEAN DEFAULT false,
  financial_consent BOOLEAN DEFAULT false,
  consent_documents JSONB, -- [{ type, url, uploaded_at }]
  
  -- Status
  status VARCHAR(50) NOT NULL DEFAULT 'admitted', -- 'admitted', 'discharge_pending', 'discharged', 'transferred', 'absconded', 'death'
  
  -- Discharge Information
  discharge_initiated_at TIMESTAMP,
  discharge_initiated_by UUID REFERENCES users(id),
  actual_discharge_date TIMESTAMP,
  discharge_type VARCHAR(50), -- 'regular', 'lama', 'absconded', 'death', 'transfer'
  
  -- Billing
  total_charges DECIMAL(12, 2) DEFAULT 0,
  total_deposits DECIMAL(12, 2) DEFAULT 0,
  balance DECIMAL(12, 2) DEFAULT 0,
  billing_cleared BOOLEAN DEFAULT false,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id),
  updated_by UUID REFERENCES users(id)
);

-- Indexes
CREATE INDEX idx_admissions_patient ON admissions(patient_id);
CREATE INDEX idx_admissions_bed ON admissions(bed_id);
CREATE INDEX idx_admissions_admission_request ON admissions(admission_request_id);
CREATE INDEX idx_admissions_attending_doctor ON admissions(attending_doctor_id);
CREATE INDEX idx_admissions_status ON admissions(status);
CREATE INDEX idx_admissions_admission_date ON admissions(admission_date);
CREATE INDEX idx_admissions_discharge_date ON admissions(actual_discharge_date);

-- Constraints
ALTER TABLE admissions ADD CONSTRAINT chk_admission_type 
  CHECK (admission_type IN ('planned', 'emergency', 'transfer'));
ALTER TABLE admissions ADD CONSTRAINT chk_admission_source 
  CHECK (admission_source IN ('opd', 'emergency', 'direct'));
ALTER TABLE admissions ADD CONSTRAINT chk_admission_status 
  CHECK (status IN ('admitted', 'discharge_pending', 'discharged', 'transferred', 'absconded', 'death'));
ALTER TABLE admissions ADD CONSTRAINT chk_discharge_type 
  CHECK (discharge_type IN ('regular', 'lama', 'absconded', 'death', 'transfer'));
```

---

### 3.6 Bed Transfers Table

```sql
CREATE TABLE bed_transfers (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Transfer Details
  admission_id UUID NOT NULL REFERENCES admissions(id) ON DELETE RESTRICT,
  
  -- From Location
  from_bed_id UUID NOT NULL REFERENCES beds(id) ON DELETE RESTRICT,
  
  -- To Location
  to_bed_id UUID NOT NULL REFERENCES beds(id) ON DELETE RESTRICT,
  
  -- Transfer Information
  transfer_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  transfer_reason TEXT NOT NULL,
  transfer_type VARCHAR(50) NOT NULL, -- 'intra_ward', 'inter_ward'
  
  -- Approval
  requested_by UUID NOT NULL REFERENCES users(id),
  approved_by UUID REFERENCES users(id),
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_bed_transfers_admission ON bed_transfers(admission_id);
CREATE INDEX idx_bed_transfers_from_bed ON bed_transfers(from_bed_id);
CREATE INDEX idx_bed_transfers_to_bed ON bed_transfers(to_bed_id);
CREATE INDEX idx_bed_transfers_transfer_date ON bed_transfers(transfer_date);

-- Constraints
ALTER TABLE bed_transfers ADD CONSTRAINT chk_transfer_type 
  CHECK (transfer_type IN ('intra_ward', 'inter_ward'));
ALTER TABLE bed_transfers ADD CONSTRAINT chk_different_beds 
  CHECK (from_bed_id != to_bed_id);
```

---

### 3.7 Admission Charges Table

```sql
CREATE TABLE admission_charges (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Admission Reference
  admission_id UUID NOT NULL REFERENCES admissions(id) ON DELETE RESTRICT,
  
  -- Charge Details
  charge_type VARCHAR(50) NOT NULL, -- 'admission_fee', 'room_rent', 'consultation', 'investigation', 'procedure', 'medication', 'other'
  service_id UUID REFERENCES services(id),
  
  -- Description
  description TEXT NOT NULL,
  charge_date DATE NOT NULL,
  
  -- Quantity & Pricing
  quantity DECIMAL(10, 2) DEFAULT 1,
  unit_price DECIMAL(10, 2) NOT NULL,
  total_amount DECIMAL(10, 2) NOT NULL,
  
  -- Discount
  discount_percentage DECIMAL(5, 2) DEFAULT 0,
  discount_amount DECIMAL(10, 2) DEFAULT 0,
  discount_reason TEXT,
  
  -- Net Amount
  net_amount DECIMAL(10, 2) NOT NULL,
  
  -- Status
  is_billed BOOLEAN DEFAULT false,
  invoice_id UUID REFERENCES invoices(id),
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id)
);

-- Indexes
CREATE INDEX idx_admission_charges_admission ON admission_charges(admission_id);
CREATE INDEX idx_admission_charges_charge_type ON admission_charges(charge_type);
CREATE INDEX idx_admission_charges_charge_date ON admission_charges(charge_date);
CREATE INDEX idx_admission_charges_is_billed ON admission_charges(is_billed);
CREATE INDEX idx_admission_charges_invoice ON admission_charges(invoice_id);

-- Constraints
ALTER TABLE admission_charges ADD CONSTRAINT chk_charge_type 
  CHECK (charge_type IN ('admission_fee', 'room_rent', 'consultation', 'investigation', 'procedure', 'medication', 'other'));
ALTER TABLE admission_charges ADD CONSTRAINT chk_amounts_positive 
  CHECK (unit_price >= 0 AND total_amount >= 0 AND net_amount >= 0);
```

---

### 3.8 Deposits Table

```sql
CREATE TABLE deposits (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Deposit Details
  deposit_number VARCHAR(50) NOT NULL UNIQUE, -- DEP-2026-00001
  admission_id UUID NOT NULL REFERENCES admissions(id) ON DELETE RESTRICT,
  
  -- Amount
  amount DECIMAL(10, 2) NOT NULL,
  
  -- Payment Details
  payment_mode VARCHAR(50) NOT NULL, -- 'cash', 'card', 'upi', 'cheque', 'bank_transfer'
  transaction_reference VARCHAR(100),
  cheque_number VARCHAR(50),
  cheque_bank VARCHAR(100),
  cheque_date DATE,
  
  -- Status
  status VARCHAR(50) NOT NULL DEFAULT 'received', -- 'received', 'adjusted', 'refunded'
  adjusted_amount DECIMAL(10, 2) DEFAULT 0,
  refunded_amount DECIMAL(10, 2) DEFAULT 0,
  
  -- Remarks
  remarks TEXT,
  
  -- Metadata
  received_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  received_by UUID NOT NULL REFERENCES users(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_deposits_admission ON deposits(admission_id);
CREATE INDEX idx_deposits_received_date ON deposits(received_date);
CREATE INDEX idx_deposits_status ON deposits(status);

-- Constraints
ALTER TABLE deposits ADD CONSTRAINT chk_payment_mode 
  CHECK (payment_mode IN ('cash', 'card', 'upi', 'cheque', 'bank_transfer'));
ALTER TABLE deposits ADD CONSTRAINT chk_deposit_status 
  CHECK (status IN ('received', 'adjusted', 'refunded'));
ALTER TABLE deposits ADD CONSTRAINT chk_amount_positive 
  CHECK (amount > 0);
```

---

### 3.9 Discharge Summaries Table

```sql
CREATE TABLE discharge_summaries (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  
  -- Admission Reference
  admission_id UUID NOT NULL UNIQUE REFERENCES admissions(id) ON DELETE RESTRICT,
  
  -- Discharge Details
  discharge_date TIMESTAMP NOT NULL,
  discharge_type VARCHAR(50) NOT NULL, -- 'regular', 'lama', 'absconded', 'death', 'transfer'
  
  -- Clinical Summary
  final_diagnosis TEXT NOT NULL,
  hospital_course TEXT NOT NULL, -- Summary of stay
  procedures_performed JSONB, -- [{ procedure, date, notes }]
  investigations_summary TEXT,
  
  -- Discharge Instructions
  medications_on_discharge JSONB, -- [{ name, dosage, frequency, duration }]
  diet_advice TEXT,
  activity_restrictions TEXT,
  warning_signs TEXT,
  follow_up_instructions TEXT,
  follow_up_date DATE,
  
  -- LAMA Specific
  lama_form_url TEXT,
  lama_signature_url TEXT,
  
  -- Death Specific
  death_time TIMESTAMP,
  death_cause TEXT,
  
  -- Documents
  summary_pdf_url TEXT,
  
  -- Metadata
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID NOT NULL REFERENCES users(id),
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_by UUID REFERENCES users(id)
);

-- Indexes
CREATE INDEX idx_discharge_summaries_admission ON discharge_summaries(admission_id);
CREATE INDEX idx_discharge_summaries_discharge_date ON discharge_summaries(discharge_date);
CREATE INDEX idx_discharge_summaries_discharge_type ON discharge_summaries(discharge_type);

-- Constraints
ALTER TABLE discharge_summaries ADD CONSTRAINT chk_discharge_type 
  CHECK (discharge_type IN ('regular', 'lama', 'absconded', 'death', 'transfer'));
ALTER TABLE discharge_summaries ADD CONSTRAINT chk_final_diagnosis_length 
  CHECK (LENGTH(final_diagnosis) >= 10);
ALTER TABLE discharge_summaries ADD CONSTRAINT chk_hospital_course_length 
  CHECK (LENGTH(hospital_course) >= 100);
```

---

## 4. Modified Tables

### 4.1 Visits Table Modification

```sql
-- Add admission request reference
ALTER TABLE visits ADD COLUMN admission_request_id UUID REFERENCES admission_requests(id);
CREATE INDEX idx_visits_admission_request ON visits(admission_request_id);
```

### 4.2 Invoices Table Modification

```sql
-- Add admission reference
ALTER TABLE invoices ADD COLUMN admission_id UUID REFERENCES admissions(id);
CREATE INDEX idx_invoices_admission ON invoices(admission_id);
```

### 4.3 Consultations Table Modification

```sql
-- Add admission decision details
ALTER TABLE consultations ADD COLUMN admission_decision JSONB;
-- Structure: { type: 'admission', reason: '', wardType: '', urgency: '', orders: '' }
```

---

## 5. Views

### 5.1 Current Bed Availability View

```sql
CREATE VIEW v_bed_availability AS
SELECT 
  w.id AS ward_id,
  w.name AS ward_name,
  w.code AS ward_code,
  w.ward_type,
  r.id AS room_id,
  r.room_number,
  r.room_type,
  b.id AS bed_id,
  b.bed_number,
  b.status AS bed_status,
  b.current_admission_id,
  b.occupied_since,
  CASE 
    WHEN b.status = 'available' THEN 'Available'
    WHEN b.status = 'occupied' THEN p.name
    WHEN b.status = 'reserved' THEN 'Reserved'
    ELSE 'Under Maintenance'
  END AS occupant_info
FROM beds b
JOIN rooms r ON b.room_id = r.id
JOIN wards w ON r.ward_id = w.id
LEFT JOIN admissions a ON b.current_admission_id = a.id
LEFT JOIN patients p ON a.patient_id = p.id
WHERE b.is_active = true AND r.is_active = true AND w.is_active = true
ORDER BY w.name, r.room_number, b.bed_number;
```

### 5.2 Ward Occupancy Summary View

```sql
CREATE VIEW v_ward_occupancy AS
SELECT 
  w.id AS ward_id,
  w.name AS ward_name,
  w.code AS ward_code,
  w.ward_type,
  COUNT(DISTINCT r.id) AS total_rooms,
  COUNT(b.id) AS total_beds,
  COUNT(CASE WHEN b.status = 'occupied' THEN 1 END) AS occupied_beds,
  COUNT(CASE WHEN b.status = 'available' THEN 1 END) AS available_beds,
  COUNT(CASE WHEN b.status = 'reserved' THEN 1 END) AS reserved_beds,
  ROUND(
    (COUNT(CASE WHEN b.status = 'occupied' THEN 1 END)::DECIMAL / COUNT(b.id)) * 100, 
    2
  ) AS occupancy_percentage
FROM wards w
JOIN rooms r ON w.id = r.ward_id AND r.is_active = true
JOIN beds b ON r.id = b.room_id AND b.is_active = true
WHERE w.is_active = true
GROUP BY w.id, w.name, w.code, w.ward_type
ORDER BY w.name;
```

### 5.3 Active Admissions View

```sql
CREATE VIEW v_active_admissions AS
SELECT 
  a.id AS admission_id,
  a.admission_number,
  a.admission_date,
  EXTRACT(DAY FROM (CURRENT_TIMESTAMP - a.admission_date)) AS days_admitted,
  p.id AS patient_id,
  p.uhid,
  p.name AS patient_name,
  p.age,
  p.gender,
  w.name AS ward_name,
  r.room_number,
  b.bed_number,
  CONCAT(w.name, ' - ', r.room_number, ' - ', b.bed_number) AS location,
  a.provisional_diagnosis,
  u.name AS attending_doctor_name,
  a.status,
  a.total_charges,
  a.total_deposits,
  a.balance
FROM admissions a
JOIN patients p ON a.patient_id = p.id
JOIN beds b ON a.bed_id = b.id
JOIN rooms r ON b.room_id = r.id
JOIN wards w ON r.ward_id = w.id
JOIN users u ON a.attending_doctor_id = u.id
WHERE a.status IN ('admitted', 'discharge_pending')
ORDER BY w.name, r.room_number, b.bed_number;
```

---

## 6. Triggers

### 6.1 Update Bed Status on Admission

```sql
CREATE OR REPLACE FUNCTION fn_update_bed_on_admission()
RETURNS TRIGGER AS $$
BEGIN
  -- Update bed status to occupied
  UPDATE beds 
  SET 
    status = 'occupied',
    current_admission_id = NEW.id,
    occupied_since = NEW.admission_date
  WHERE id = NEW.bed_id;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_update_bed_on_admission
AFTER INSERT ON admissions
FOR EACH ROW
EXECUTE FUNCTION fn_update_bed_on_admission();
```

### 6.2 Release Bed on Discharge

```sql
CREATE OR REPLACE FUNCTION fn_release_bed_on_discharge()
RETURNS TRIGGER AS $$
BEGIN
  -- Release bed if status changed to discharged
  IF NEW.status IN ('discharged', 'absconded', 'death') AND OLD.status != NEW.status THEN
    UPDATE beds 
    SET 
      status = 'available',
      current_admission_id = NULL,
      occupied_since = NULL
    WHERE id = NEW.bed_id;
  END IF;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_release_bed_on_discharge
AFTER UPDATE ON admissions
FOR EACH ROW
EXECUTE FUNCTION fn_release_bed_on_discharge();
```

### 6.3 Update Admission Totals

```sql
CREATE OR REPLACE FUNCTION fn_update_admission_totals()
RETURNS TRIGGER AS $$
BEGIN
  -- Recalculate total charges
  UPDATE admissions
  SET 
    total_charges = (
      SELECT COALESCE(SUM(net_amount), 0)
      FROM admission_charges
      WHERE admission_id = NEW.admission_id
    ),
    balance = total_deposits - (
      SELECT COALESCE(SUM(net_amount), 0)
      FROM admission_charges
      WHERE admission_id = NEW.admission_id
    )
  WHERE id = NEW.admission_id;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_update_admission_totals_on_charge
AFTER INSERT OR UPDATE ON admission_charges
FOR EACH ROW
EXECUTE FUNCTION fn_update_admission_totals();
```

### 6.4 Update Admission Totals on Deposit

```sql
CREATE OR REPLACE FUNCTION fn_update_admission_totals_on_deposit()
RETURNS TRIGGER AS $$
BEGIN
  -- Recalculate total deposits and balance
  UPDATE admissions
  SET 
    total_deposits = (
      SELECT COALESCE(SUM(amount), 0)
      FROM deposits
      WHERE admission_id = NEW.admission_id AND status = 'received'
    ),
    balance = (
      SELECT COALESCE(SUM(amount), 0)
      FROM deposits
      WHERE admission_id = NEW.admission_id AND status = 'received'
    ) - total_charges
  WHERE id = NEW.admission_id;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_update_admission_totals_on_deposit
AFTER INSERT OR UPDATE ON deposits
FOR EACH ROW
EXECUTE FUNCTION fn_update_admission_totals_on_deposit();
```

---

## 7. Sequence Generators

### 7.1 Admission Request Number Generator

```sql
CREATE SEQUENCE seq_admission_request_number START 1;

CREATE OR REPLACE FUNCTION fn_generate_admission_request_number()
RETURNS VARCHAR(50) AS $$
DECLARE
  year VARCHAR(4);
  seq_num VARCHAR(10);
BEGIN
  year := EXTRACT(YEAR FROM CURRENT_DATE)::VARCHAR;
  seq_num := LPAD(nextval('seq_admission_request_number')::VARCHAR, 5, '0');
  RETURN 'ADM-REQ-' || year || '-' || seq_num;
END;
$$ LANGUAGE plpgsql;
```

### 7.2 Admission Number Generator

```sql
CREATE SEQUENCE seq_admission_number START 1;

CREATE OR REPLACE FUNCTION fn_generate_admission_number()
RETURNS VARCHAR(50) AS $$
DECLARE
  year VARCHAR(4);
  seq_num VARCHAR(10);
BEGIN
  year := EXTRACT(YEAR FROM CURRENT_DATE)::VARCHAR;
  seq_num := LPAD(nextval('seq_admission_number')::VARCHAR, 5, '0');
  RETURN 'ADM-' || year || '-' || seq_num;
END;
$$ LANGUAGE plpgsql;
```

### 7.3 Deposit Number Generator

```sql
CREATE SEQUENCE seq_deposit_number START 1;

CREATE OR REPLACE FUNCTION fn_generate_deposit_number()
RETURNS VARCHAR(50) AS $$
DECLARE
  year VARCHAR(4);
  seq_num VARCHAR(10);
BEGIN
  year := EXTRACT(YEAR FROM CURRENT_DATE)::VARCHAR;
  seq_num := LPAD(nextval('seq_deposit_number')::VARCHAR, 5, '0');
  RETURN 'DEP-' || year || '-' || seq_num;
END;
$$ LANGUAGE plpgsql;
```

---

## 8. Indexes for Performance

```sql
-- Composite indexes for common queries
CREATE INDEX idx_admissions_status_date ON admissions(status, admission_date);
CREATE INDEX idx_admissions_patient_status ON admissions(patient_id, status);
CREATE INDEX idx_beds_room_status ON beds(room_id, status);
CREATE INDEX idx_admission_charges_admission_billed ON admission_charges(admission_id, is_billed);

-- Full-text search indexes
CREATE INDEX idx_admission_requests_reason_fts ON admission_requests USING gin(to_tsvector('english', admission_reason));
CREATE INDEX idx_admissions_diagnosis_fts ON admissions USING gin(to_tsvector('english', provisional_diagnosis));
```

---

## 9. Data Integrity Rules

### 9.1 Business Rules Enforced by Database

1. **Bed Allocation:**
   - A bed can only be occupied by one admission at a time
   - Enforced by: `current_admission_id` unique constraint

2. **Admission Request Completion:**
   - Once admission created, request status → 'completed'
   - Enforced by: Application logic + trigger

3. **Discharge Prerequisites:**
   - Cannot discharge without discharge summary
   - Enforced by: Foreign key + application logic

4. **Billing Clearance:**
   - Cannot complete discharge with pending balance (unless authorized)
   - Enforced by: Application logic

5. **Bed Transfer:**
   - Cannot transfer to same bed
   - Enforced by: Check constraint `chk_different_beds`

---

## 10. Migration Strategy

### 10.1 Phase 1 → Phase 2 Migration

**Step 1: Create New Tables**
```sql
-- Run all CREATE TABLE statements
-- Run all CREATE INDEX statements
-- Run all CREATE VIEW statements
```

**Step 2: Modify Existing Tables**
```sql
-- Add new columns to visits, invoices, consultations
-- Create indexes on new columns
```

**Step 3: Create Triggers & Functions**
```sql
-- Create all trigger functions
-- Create all triggers
-- Create sequence generators
```

**Step 4: Seed Master Data**
```sql
-- Insert wards
-- Insert rooms
-- Insert beds
```

**Step 5: Verify**
```sql
-- Run test queries
-- Verify constraints
-- Test triggers
```

### 10.2 Rollback Plan

```sql
-- Drop triggers
DROP TRIGGER IF EXISTS trg_update_bed_on_admission ON admissions;
DROP TRIGGER IF EXISTS trg_release_bed_on_discharge ON admissions;
-- ... (drop all triggers)

-- Drop functions
DROP FUNCTION IF EXISTS fn_update_bed_on_admission();
-- ... (drop all functions)

-- Drop views
DROP VIEW IF EXISTS v_bed_availability;
-- ... (drop all views)

-- Drop tables (in reverse order of dependencies)
DROP TABLE IF EXISTS discharge_summaries;
DROP TABLE IF EXISTS deposits;
DROP TABLE IF EXISTS admission_charges;
DROP TABLE IF EXISTS bed_transfers;
DROP TABLE IF EXISTS admissions;
DROP TABLE IF EXISTS admission_requests;
DROP TABLE IF EXISTS beds;
DROP TABLE IF EXISTS rooms;
DROP TABLE IF EXISTS wards;

-- Remove columns from existing tables
ALTER TABLE visits DROP COLUMN IF EXISTS admission_request_id;
ALTER TABLE invoices DROP COLUMN IF EXISTS admission_id;
ALTER TABLE consultations DROP COLUMN IF EXISTS admission_decision;
```

---

## 11. Backup & Recovery

### 11.1 Backup Strategy

- **Daily Full Backup:** All IPD tables
- **Hourly Incremental:** Admissions, admission_charges, deposits
- **Real-time Replication:** Critical tables (admissions, beds)

### 11.2 Recovery Point Objective (RPO)

- **Critical Data:** 15 minutes (admissions, bed status)
- **Master Data:** 24 hours (wards, rooms, beds)
- **Historical Data:** 7 days (discharge summaries)

---

## 12. Performance Considerations

### 12.1 Expected Data Volume

| Table | Records/Year | Growth Rate |
|-------|--------------|-------------|
| wards | 10-20 | Stable |
| rooms | 50-100 | Stable |
| beds | 200-500 | Stable |
| admission_requests | 5,000-10,000 | 10% YoY |
| admissions | 5,000-10,000 | 10% YoY |
| bed_transfers | 1,000-2,000 | 10% YoY |
| admission_charges | 50,000-100,000 | 15% YoY |
| deposits | 10,000-20,000 | 10% YoY |
| discharge_summaries | 5,000-10,000 | 10% YoY |

### 12.2 Query Optimization

- Use views for complex queries
- Denormalize bed status for quick lookups
- Cache ward occupancy summary
- Partition large tables by year (future)

---

> **Note:** This schema is designed for PostgreSQL 14+. Adjust syntax for other databases if needed.
