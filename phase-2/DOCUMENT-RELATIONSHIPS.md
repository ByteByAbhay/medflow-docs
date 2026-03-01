# 📚 Phase 2 Document Relationships

**Last Updated:** February 27, 2026

---

## 📋 Document Hierarchy

```
Phase 2 IPD Admission Module
│
├── 🎯 IPD Admission Flow.md ⭐ PRIMARY DOCUMENT FOR MEDICAL STAFF
│   └── Complete workflow documentation for doctors, nurses, reception
│
├── 00-PHASE2-SUMMARY.md (Executive Summary)
│   └── High-level overview for stakeholders
│
└── Technical Documentation (13 documents)
    ├── Requirements & Planning (3 docs)
    │   ├── 01-feature-requirements.md
    │   ├── 02-user-stories.md
    │   └── 13-simplified-workflow.md
    │
    ├── Technical Design (4 docs)
    │   ├── 03-database-schema.md
    │   ├── 04-api-specification.md
    │   ├── 05-ui-wireframes.md
    │   └── 06-workflow-diagrams.md
    │
    ├── Security & Integration (2 docs)
    │   ├── 07-role-permissions.md
    │   └── 08-integration-points.md
    │
    └── Implementation (4 docs)
        ├── 09-data-migration.md
        ├── 10-testing-strategy.md
        ├── 11-deployment-plan.md
        └── 12-training-materials.md
```

---

## 🔗 Document Dependencies

### IPD Admission Flow.md
**Purpose:** Complete workflow documentation for medical staff  
**Audience:** Doctors, Nurses, Reception  
**References:**
- Simplified from: 13-simplified-workflow.md
- Screens from: 05-ui-wireframes.md
- Workflows from: 06-workflow-diagrams.md
- Roles from: 07-role-permissions.md

**Referenced By:**
- README.md (Quick Links)
- All other documents derive from this master workflow

---

### 00-PHASE2-SUMMARY.md
**Purpose:** Executive overview  
**Audience:** Management, Stakeholders  
**References:**
- All 13 technical documents
- Consolidated metrics and timelines

**Referenced By:**
- README.md (Document Index)

---

### 01-feature-requirements.md
**Purpose:** Detailed feature specifications  
**Audience:** Product team, Developers  
**References:**
- 02-user-stories.md (User stories)
- 07-role-permissions.md (Roles)

**Referenced By:**
- 03-database-schema.md (Data requirements)
- 04-api-specification.md (API endpoints)
- 05-ui-wireframes.md (UI requirements)

---

### 02-user-stories.md
**Purpose:** User stories with acceptance criteria  
**Audience:** Product team, QA  
**References:**
- 01-feature-requirements.md (Features)
- 07-role-permissions.md (User roles)

**Referenced By:**
- 10-testing-strategy.md (Test cases)
- 12-training-materials.md (Training scenarios)

---

### 03-database-schema.md
**Purpose:** Database design  
**Audience:** Backend developers, DBAs  
**References:**
- 01-feature-requirements.md (Data requirements)
- 08-integration-points.md (Existing tables)

**Referenced By:**
- 04-api-specification.md (Data models)
- 09-data-migration.md (Migration scripts)

---

### 04-api-specification.md
**Purpose:** REST API endpoints  
**Audience:** Backend & Frontend developers  
**References:**
- 03-database-schema.md (Data models)
- 07-role-permissions.md (Auth requirements)

**Referenced By:**
- 05-ui-wireframes.md (API calls)
- 08-integration-points.md (Integration APIs)

---

### 05-ui-wireframes.md
**Purpose:** Screen designs and UI flows  
**Audience:** Frontend developers, Designers  
**References:**
- 01-feature-requirements.md (UI requirements)
- 04-api-specification.md (Data to display)
- 06-workflow-diagrams.md (User flows)

**Referenced By:**
- IPD Admission Flow.md (Screen examples)
- 12-training-materials.md (Training screens)

---

### 06-workflow-diagrams.md
**Purpose:** Process flow diagrams  
**Audience:** All teams  
**References:**
- 01-feature-requirements.md (Processes)
- 13-simplified-workflow.md (Hospital-specific flows)

**Referenced By:**
- IPD Admission Flow.md (Main workflow)
- 10-testing-strategy.md (Test scenarios)

---

### 07-role-permissions.md
**Purpose:** RBAC permission matrix  
**Audience:** Backend developers, Security  
**References:**
- 01-feature-requirements.md (Features)
- 02-user-stories.md (User roles)

**Referenced By:**
- All documents (Role definitions)
- 04-api-specification.md (Auth)

---

### 08-integration-points.md
**Purpose:** Integration with existing modules  
**Audience:** Backend developers  
**References:**
- Phase 1 documentation
- 03-database-schema.md (Existing tables)
- 04-api-specification.md (Integration APIs)

**Referenced By:**
- 10-testing-strategy.md (Integration tests)
- 11-deployment-plan.md (Dependencies)

---

### 09-data-migration.md
**Purpose:** Data migration strategy  
**Audience:** DBAs, Backend developers  
**References:**
- 03-database-schema.md (New schema)

**Referenced By:**
- 11-deployment-plan.md (Migration steps)

---

### 10-testing-strategy.md
**Purpose:** Test plan and test cases  
**Audience:** QA team  
**References:**
- 02-user-stories.md (Acceptance criteria)
- 06-workflow-diagrams.md (Test scenarios)

**Referenced By:**
- 11-deployment-plan.md (UAT requirements)

---

### 11-deployment-plan.md
**Purpose:** Rollout strategy  
**Audience:** DevOps, Operations  
**References:**
- 09-data-migration.md (Migration)
- 10-testing-strategy.md (UAT)
- 12-training-materials.md (Training)

**Referenced By:**
- 00-PHASE2-SUMMARY.md (Timeline)

---

### 12-training-materials.md
**Purpose:** Staff training guides  
**Audience:** Hospital staff, Trainers  
**References:**
- IPD Admission Flow.md (Workflows)
- 05-ui-wireframes.md (Screens)
- 13-simplified-workflow.md (Hospital-specific)

**Referenced By:**
- 11-deployment-plan.md (Training schedule)

---

### 13-simplified-workflow.md
**Purpose:** Hospital-specific simplified workflow  
**Audience:** Medical staff, Product team  
**References:**
- 01-feature-requirements.md (Features)
- 06-workflow-diagrams.md (Generic flows)

**Referenced By:**
- IPD Admission Flow.md (Main reference)
- 12-training-materials.md (Training content)

---

## 🎯 Document Usage by Role

### 👨‍⚕️ For Doctors
**Primary:** IPD Admission Flow.md  
**Secondary:** 13-simplified-workflow.md, 12-training-materials.md

### 👩‍⚕️ For Nurses
**Primary:** IPD Admission Flow.md  
**Secondary:** 13-simplified-workflow.md, 12-training-materials.md

### 👤 For Reception
**Primary:** IPD Admission Flow.md  
**Secondary:** 13-simplified-workflow.md, 12-training-materials.md

### 💼 For Management
**Primary:** 00-PHASE2-SUMMARY.md  
**Secondary:** IPD Admission Flow.md, 11-deployment-plan.md

### 👨‍💻 For Developers
**Primary:** 03-database-schema.md, 04-api-specification.md  
**Secondary:** 01-feature-requirements.md, 08-integration-points.md

### 🎨 For Designers
**Primary:** 05-ui-wireframes.md  
**Secondary:** 06-workflow-diagrams.md, IPD Admission Flow.md

### 🧪 For QA
**Primary:** 10-testing-strategy.md  
**Secondary:** 02-user-stories.md, 06-workflow-diagrams.md

### 🚀 For DevOps
**Primary:** 11-deployment-plan.md  
**Secondary:** 09-data-migration.md, 10-testing-strategy.md

---

## ✅ Consistency Checklist

### Role Names (Simplified Structure)
All documents should use these 4 roles:
- ✅ **Doctor** (not "Doctor/Consultant")
- ✅ **Nurse** (not "Admission Clerk" or "Ward Nurse")
- ✅ **Reception** (not "Billing Staff" or "Receptionist")
- ✅ **Admin** (for system configuration)

### Key Workflows
All documents should reference:
1. Routine Admission Flow
2. Emergency Admission Flow
3. Patient Care Period
4. Patient Transfer Flow
5. Discharge Process

### Payment Collection Points
All documents should be consistent:
- **At Admission:** Admission fee (₹1,000-₹2,000)
- **After Admission:** Initial deposit (₹5,000-₹20,000)
- **During Stay:** Additional deposits if needed
- **At Discharge:** Final payment or refund

---

## 🔄 Update Process

When updating any document:

1. **Check Dependencies:** Review which documents reference this one
2. **Update Related Docs:** Update all dependent documents
3. **Verify Consistency:** Check role names, workflows, terminology
4. **Update This File:** Document the relationships
5. **Update README.md:** Update status if needed

---

## 📝 Notes

- **IPD Admission Flow.md** is the PRIMARY document for medical staff approval
- All technical documents support the implementation of this workflow
- Role simplification (4 roles) is reflected across all updated documents
- Some older documents may still reference old role names - these need updating

---

**Document Owner:** Product Team  
**Last Review:** February 27, 2026  
**Next Review:** After Phase 2 completion
