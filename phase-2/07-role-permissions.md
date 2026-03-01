# 🔐 Phase 2: IPD Admission - Role Permissions Matrix

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document defines **role-based access control (RBAC)** for the IPD Admission Module, specifying which roles can perform which actions.

---

## 2. Roles Overview

| Role | Code | Description |
|------|------|-------------|
| Super Admin | `SUPER_ADMIN` | Full system access |
| Admin | `ADMIN` | System configuration & management (wards, rooms, beds) |
| Doctor | `DOCTOR` | Create admission requests, manage admitted patients, discharge |
| Nurse | `NURSE` | Approve admissions, allocate beds, manage ward patients, transfers |
| Reception | `RECEPTION` | Collect fees, deposits, payments; generate receipts |

---

## 3. Permission Categories

### 3.1 Ward Management

| Permission Code | Description | Super Admin | Admin | Doctor | Consultant | Admission Clerk | Ward Nurse | Billing Staff | Receptionist |
|----------------|-------------|:-----------:|:-----:|:------:|:----------:|:---------------:|:----------:|:-------------:|:------------:|
| `ward:view` | View wards | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `ward:create` | Create ward | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `ward:update` | Update ward | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `ward:delete` | Delete ward | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

---

### 3.2 Room Management

| Permission Code | Description | Super Admin | Admin | Doctor | Consultant | Admission Clerk | Ward Nurse | Billing Staff | Receptionist |
|----------------|-------------|:-----------:|:-----:|:------:|:----------:|:---------------:|:----------:|:-------------:|:------------:|
| `room:view` | View rooms | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `room:create` | Create room | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `room:update` | Update room | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `room:delete` | Delete room | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

---

### 3.3 Bed Management

| Permission Code | Description | Super Admin | Admin | Doctor | Consultant | Admission Clerk | Ward Nurse | Billing Staff | Receptionist |
|----------------|-------------|:-----------:|:-----:|:------:|:----------:|:---------------:|:----------:|:-------------:|:------------:|
| `bed:view` | View beds | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `bed:availability` | View bed availability | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `bed:create` | Create bed | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `bed:update` | Update bed | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ |
| `bed:status` | Update bed status | ✅ | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |
| `bed:delete` | Delete bed | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

---

### 3.4 Admission Requests

| Permission Code | Description | Super Admin | Admin | Doctor | Nurse | Reception |
|----------------|-------------|:-----------:|:-----:|:------:|:-----:|:---------:|
| `admission-request:view` | View admission requests | ✅ | ✅ | ✅ | ✅ | ✅ |
| `admission-request:view-own` | View own requests | ✅ | ✅ | ✅ | ❌ | ❌ |
| `admission-request:create` | Create admission request | ✅ | ✅ | ✅ | ❌ | ❌ |
| `admission-request:update` | Update request | ✅ | ✅ | ✅ | ❌ | ❌ |
| `admission-request:approve` | Approve/reject request | ✅ | ✅ | ❌ | ✅ | ❌ |
| `admission-request:delete` | Delete request | ✅ | ✅ | ❌ | ❌ | ❌ |

---

### 3.5 Admissions

| Permission Code | Description | Super Admin | Admin | Doctor | Nurse | Reception |
|----------------|-------------|:-----------:|:-----:|:------:|:-----:|:---------:|
| `admission:view` | View admissions | ✅ | ✅ | ✅ | ✅ | ✅ |
| `admission:view-own` | View own patients | ✅ | ✅ | ✅ | ❌ | ❌ |
| `admission:view-ward` | View ward patients | ✅ | ✅ | ✅ | ✅ | ❌ |
| `admission:create` | Create admission (allocate bed) | ✅ | ✅ | ❌ | ✅ | ❌ |
| `admission:emergency` | Create emergency admission | ✅ | ✅ | ❌ | ✅ | ✅ |
| `admission:update` | Update admission details | ✅ | ✅ | ✅ | ✅ | ❌ |
| `admission:transfer` | Transfer patient bed | ✅ | ✅ | ✅ | ✅ | ❌ |
| `admission:discharge` | Initiate/complete discharge | ✅ | ✅ | ✅ | ❌ | ❌ |
| `admission:cancel` | Cancel admission | ✅ | ✅ | ❌ | ✅ | ❌ |

---

### 3.6 Discharge Management

| Permission Code | Description | Super Admin | Admin | Doctor | Consultant | Admission Clerk | Ward Nurse | Billing Staff | Receptionist |
|----------------|-------------|:-----------:|:-----:|:------:|:----------:|:---------------:|:----------:|:-------------:|:------------:|
| `discharge:view` | View discharge summaries | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| `discharge:initiate` | Initiate discharge | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| `discharge:summary` | Create discharge summary | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| `discharge:complete` | Complete discharge | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ |
| `discharge:print` | Print discharge documents | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

---

### 3.7 Billing & Charges

| Permission Code | Description | Super Admin | Admin | Doctor | Nurse | Reception |
|----------------|-------------|:-----------:|:-----:|:------:|:-----:|:---------:|
| `billing:view` | View billing details | ✅ | ✅ | ✅ | ✅ | ✅ |
| `billing:charges-view` | View charges | ✅ | ✅ | ✅ | ✅ | ✅ |
| `billing:charges-create` | Add charges | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:charges-update` | Update charges | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:charges-delete` | Delete charges | ✅ | ✅ | ❌ | ❌ | ❌ |
| `billing:discount` | Apply discounts | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:deposit` | Collect deposits | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:invoice` | Generate invoices | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:payment` | Record payments | ✅ | ✅ | ❌ | ❌ | ✅ |
| `billing:refund` | Process refunds | ✅ | ✅ | ❌ | ❌ | ✅ |

---

### 3.8 Reports & Analytics

| Permission Code | Description | Super Admin | Admin | Doctor | Consultant | Admission Clerk | Ward Nurse | Billing Staff | Receptionist |
|----------------|-------------|:-----------:|:-----:|:------:|:----------:|:---------------:|:----------:|:-------------:|:------------:|
| `reports:view` | View reports | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| `reports:admission-stats` | View admission statistics | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| `reports:occupancy` | View occupancy reports | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| `reports:billing` | View billing reports | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ |
| `reports:export` | Export reports | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ |

---

## 4. Permission Groups

### 4.1 Ward Configuration Group

**Permissions:**
- `ward:view`
- `ward:create`
- `ward:update`
- `room:view`
- `room:create`
- `room:update`
- `bed:view`
- `bed:create`
- `bed:update`

**Assigned To:**
- Super Admin
- Admin

---

### 4.2 Admission Management Group

**Permissions:**
- `admission-request:view`
- `admission-request:approve`
- `admission:view`
- `admission:create`
- `admission:emergency`
- `bed:availability`

**Assigned To:**
- Super Admin
- Admin
- Admission Clerk

---

### 4.3 Clinical Care Group

**Permissions:**
- `admission-request:create`
- `admission:view-own`
- `admission:update`
- `admission:transfer`
- `admission:discharge`
- `discharge:initiate`
- `discharge:summary`

**Assigned To:**
- Super Admin
- Doctor
- Consultant

---

### 4.4 Ward Nursing Group

**Permissions:**
- `admission:view-ward`
- `admission:update`
- `admission:transfer`
- `bed:status`
- `discharge:view`

**Assigned To:**
- Super Admin
- Ward Nurse

---

### 4.5 Billing Operations Group

**Permissions:**
- `billing:view`
- `billing:charges-view`
- `billing:charges-create`
- `billing:deposit`
- `billing:invoice`
- `billing:payment`
- `discharge:complete`

**Assigned To:**
- Super Admin
- Billing Staff

---

## 5. Data Access Rules

### 5.1 Doctor Access

**Rule:** Doctors can only view/manage admissions where they are:
- Referring doctor
- Admitting doctor
- Attending doctor

**Implementation:**
```sql
WHERE 
  referring_doctor_id = :userId 
  OR admitting_doctor_id = :userId 
  OR attending_doctor_id = :userId
```

---

### 5.2 Ward Nurse Access

**Rule:** Ward nurses can only view/manage patients in their assigned ward(s)

**Implementation:**
```sql
JOIN beds b ON admissions.bed_id = b.id
JOIN rooms r ON b.room_id = r.id
JOIN wards w ON r.ward_id = w.id
JOIN user_ward_assignments uwa ON w.id = uwa.ward_id
WHERE uwa.user_id = :userId
```

---

### 5.3 Billing Staff Access

**Rule:** Billing staff can view all admissions but can only modify billing-related data

**Implementation:**
- Full read access to admissions
- Write access only to charges, deposits, invoices, payments

---

## 6. Special Permissions

### 6.1 Override Permissions

| Permission Code | Description | Assigned To |
|----------------|-------------|-------------|
| `admission:force-discharge` | Force discharge without billing clearance | Super Admin, Admin |
| `billing:waive-charges` | Waive charges completely | Super Admin, Admin |
| `bed:force-allocate` | Allocate occupied bed (override) | Super Admin |
| `admission:delete` | Delete admission record | Super Admin |

---

### 6.2 Emergency Permissions

| Permission Code | Description | Assigned To |
|----------------|-------------|-------------|
| `admission:emergency-override` | Bypass normal admission process | Super Admin, Admission Clerk |
| `billing:emergency-defer` | Defer billing for emergency cases | Super Admin, Admission Clerk |

---

## 7. Permission Validation

### 7.1 API Endpoint Guards

Each API endpoint must validate permissions using decorators:

```typescript
@UseGuards(JwtAuthGuard, PermissionsGuard)
@RequirePermissions('admission:create')
@Post('/admissions')
async createAdmission() {
  // ...
}
```

---

### 7.2 UI Element Guards

UI elements must be conditionally rendered based on permissions:

```typescript
{hasPermission('admission:create') && (
  <Button onClick={handleCreateAdmission}>
    Create Admission
  </Button>
)}
```

---

## 8. Audit Trail

### 8.1 Logged Actions

All actions with the following permissions must be logged:

- `admission:create`
- `admission:update`
- `admission:transfer`
- `admission:discharge`
- `admission:cancel`
- `admission:delete`
- `billing:charges-create`
- `billing:charges-update`
- `billing:charges-delete`
- `billing:discount`
- `billing:waive-charges`
- `bed:force-allocate`

### 8.2 Audit Log Fields

- User ID
- User Name
- User Role
- Action (permission code)
- Resource Type (admission, bed, charge, etc.)
- Resource ID
- Old Value (for updates)
- New Value (for updates)
- Timestamp
- IP Address
- User Agent

---

## 9. Role Assignment

### 9.1 User-Role Assignment Table

```sql
CREATE TABLE user_roles (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  role VARCHAR(50) NOT NULL,
  assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  assigned_by UUID REFERENCES users(id),
  UNIQUE(user_id, role)
);
```

---

### 9.2 User-Ward Assignment Table

```sql
CREATE TABLE user_ward_assignments (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  ward_id UUID REFERENCES wards(id),
  assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  assigned_by UUID REFERENCES users(id),
  UNIQUE(user_id, ward_id)
);
```

---

## 10. Permission Testing Checklist

### 10.1 Positive Tests

- [ ] Super Admin can perform all actions
- [ ] Admin can perform all configuration actions
- [ ] Doctor can create admission requests
- [ ] Doctor can view own patients
- [ ] Admission Clerk can approve requests
- [ ] Admission Clerk can allocate beds
- [ ] Ward Nurse can view ward patients
- [ ] Ward Nurse can transfer patients within ward
- [ ] Billing Staff can add charges
- [ ] Billing Staff can collect deposits

### 10.2 Negative Tests

- [ ] Doctor cannot approve admission requests
- [ ] Ward Nurse cannot view other ward patients
- [ ] Billing Staff cannot initiate discharge
- [ ] Receptionist cannot view billing details
- [ ] Doctor cannot delete admission records
- [ ] Ward Nurse cannot waive charges
- [ ] Admission Clerk cannot complete discharge without billing clearance

---

## 11. Implementation Notes

### 11.1 Backend (NestJS)

```typescript
// Permission decorator
export const RequirePermissions = (...permissions: string[]) =>
  SetMetadata('permissions', permissions);

// Permission guard
@Injectable()
export class PermissionsGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const requiredPermissions = this.reflector.get<string[]>(
      'permissions',
      context.getHandler(),
    );
    
    if (!requiredPermissions) return true;
    
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    
    return requiredPermissions.every(permission =>
      user.permissions.includes(permission)
    );
  }
}
```

---

### 11.2 Frontend (React)

```typescript
// Permission hook
export const usePermissions = () => {
  const { user } = useAuth();
  
  const hasPermission = (permission: string) => {
    return user?.permissions?.includes(permission) || false;
  };
  
  const hasAnyPermission = (permissions: string[]) => {
    return permissions.some(p => hasPermission(p));
  };
  
  const hasAllPermissions = (permissions: string[]) => {
    return permissions.every(p => hasPermission(p));
  };
  
  return { hasPermission, hasAnyPermission, hasAllPermissions };
};

// Permission component
export const Can = ({ permission, children }) => {
  const { hasPermission } = usePermissions();
  return hasPermission(permission) ? children : null;
};
```

---

## 12. Security Best Practices

1. **Principle of Least Privilege:** Grant minimum permissions required
2. **Role Separation:** Separate duties between roles (e.g., billing cannot discharge)
3. **Audit Everything:** Log all sensitive actions
4. **Regular Reviews:** Review and update permissions quarterly
5. **Session Management:** Enforce session timeouts
6. **Multi-Factor Authentication:** Require MFA for admin roles
7. **IP Whitelisting:** Restrict admin access to specific IPs (optional)

---

> **Note:** This permission matrix must be reviewed and approved by security team before implementation.
