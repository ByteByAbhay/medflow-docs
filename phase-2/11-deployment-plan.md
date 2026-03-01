# 🚀 Phase 2: IPD Admission - Deployment Plan

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document outlines the **deployment strategy** for the IPD Admission Module, including environment setup, rollout phases, rollback procedures, and go-live checklist.

---

## 2. Deployment Overview

### 2.1 Deployment Strategy

**Approach:** Phased rollout with blue-green deployment

**Phases:**
1. **Development** → Internal testing
2. **Staging** → UAT with hospital staff
3. **Production (Pilot)** → Single ward for 1 week
4. **Production (Full)** → All wards

### 2.2 Timeline

| Phase | Duration | Start Date | End Date |
|-------|----------|------------|----------|
| Development | 6 weeks | TBD | TBD |
| Staging/UAT | 2 weeks | TBD | TBD |
| Production Pilot | 1 week | TBD | TBD |
| Production Full Rollout | 1 day | TBD | TBD |

---

## 3. Environment Setup

### 3.1 Development Environment

**Purpose:** Developer testing and integration

**Infrastructure:**
- Local Docker containers
- PostgreSQL 14
- Redis 7
- Node.js 18 LTS

**Access:**
- Developers only
- No external access

---

### 3.2 Staging Environment

**Purpose:** UAT and pre-production testing

**Infrastructure:**
```yaml
Server: staging.medflow.com
Database: PostgreSQL 14 (AWS RDS)
Cache: Redis 7 (AWS ElastiCache)
Application: Docker containers (AWS ECS)
Load Balancer: AWS ALB
Storage: AWS S3 (for PDFs)
```

**Specifications:**
- Application Server: 2 vCPUs, 4GB RAM
- Database: db.t3.medium (2 vCPUs, 4GB RAM)
- Redis: cache.t3.micro (2 vCPUs, 0.5GB RAM)

**Access:**
- Development team
- QA team
- Hospital staff (for UAT)
- VPN required

**Data:**
- Anonymized production-like data
- Test patients and users
- Reset weekly

---

### 3.3 Production Environment

**Purpose:** Live hospital operations

**Infrastructure:**
```yaml
Server: api.medflow.com
Database: PostgreSQL 14 (AWS RDS Multi-AZ)
Cache: Redis 7 (AWS ElastiCache with replication)
Application: Docker containers (AWS ECS with auto-scaling)
Load Balancer: AWS ALB
Storage: AWS S3 (with versioning and backup)
CDN: CloudFront (for static assets)
Monitoring: CloudWatch, Sentry
```

**Specifications:**
- Application Server: 4 vCPUs, 8GB RAM (auto-scale 2-4 instances)
- Database: db.t3.large (2 vCPUs, 8GB RAM)
- Redis: cache.t3.small (2 vCPUs, 1.37GB RAM)

**High Availability:**
- Multi-AZ database deployment
- Auto-scaling application servers
- Redis replication (master-replica)
- Daily automated backups
- 99.9% uptime SLA

**Access:**
- Hospital staff only
- Hospital network or VPN
- MFA required for admin access

---

## 4. Database Migration Strategy

### 4.1 Migration Steps

**Step 1: Backup Current Database**
```bash
pg_dump -h production-db.amazonaws.com -U medflow -d medflow_prod > backup_pre_phase2.sql
```

**Step 2: Create Migration Scripts**
```sql
-- migrations/001_create_wards_table.sql
-- migrations/002_create_rooms_table.sql
-- migrations/003_create_beds_table.sql
-- migrations/004_create_admission_requests_table.sql
-- migrations/005_create_admissions_table.sql
-- migrations/006_create_bed_transfers_table.sql
-- migrations/007_create_admission_charges_table.sql
-- migrations/008_create_deposits_table.sql
-- migrations/009_create_discharge_summaries_table.sql
-- migrations/010_modify_existing_tables.sql
-- migrations/011_create_views.sql
-- migrations/012_create_triggers.sql
-- migrations/013_create_indexes.sql
```

**Step 3: Test Migrations on Staging**
```bash
npm run migration:run
```

**Step 4: Verify Data Integrity**
```sql
-- Check table counts
SELECT 'wards' as table_name, COUNT(*) FROM wards
UNION ALL
SELECT 'rooms', COUNT(*) FROM rooms
UNION ALL
SELECT 'beds', COUNT(*) FROM beds;

-- Check constraints
SELECT * FROM information_schema.table_constraints
WHERE table_schema = 'public'
AND table_name IN ('wards', 'rooms', 'beds', 'admissions');

-- Check indexes
SELECT * FROM pg_indexes
WHERE schemaname = 'public'
AND tablename IN ('wards', 'rooms', 'beds', 'admissions');
```

**Step 5: Run Migrations on Production**
```bash
# During maintenance window
npm run migration:run --env=production
```

**Step 6: Verify Production Migration**
```bash
npm run migration:verify
```

### 4.2 Rollback Plan

**If migration fails:**
```bash
# Restore from backup
psql -h production-db.amazonaws.com -U medflow -d medflow_prod < backup_pre_phase2.sql

# Revert application deployment
aws ecs update-service --cluster medflow-prod --service medflow-api --task-definition medflow-api:previous
```

---

## 5. Application Deployment

### 5.1 Build Process

**Backend:**
```bash
# Build Docker image
docker build -t medflow-api:phase2-v1.0.0 .

# Tag for ECR
docker tag medflow-api:phase2-v1.0.0 123456789.dkr.ecr.us-east-1.amazonaws.com/medflow-api:phase2-v1.0.0

# Push to ECR
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/medflow-api:phase2-v1.0.0
```

**Frontend:**
```bash
# Build production bundle
npm run build

# Upload to S3
aws s3 sync dist/ s3://medflow-frontend-prod/

# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id E123456 --paths "/*"
```

### 5.2 Blue-Green Deployment

**Step 1: Deploy to Green Environment**
```bash
# Update ECS task definition with new image
aws ecs register-task-definition --cli-input-json file://task-definition-green.json

# Update green service
aws ecs update-service --cluster medflow-prod --service medflow-api-green --task-definition medflow-api:phase2-v1.0.0
```

**Step 2: Test Green Environment**
```bash
# Run smoke tests
npm run test:smoke -- --env=green

# Manual verification
curl https://green.medflow.com/api/health
```

**Step 3: Switch Traffic to Green**
```bash
# Update load balancer target group
aws elbv2 modify-listener --listener-arn arn:aws:elasticloadbalancing:... --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:.../medflow-api-green
```

**Step 4: Monitor Green Environment**
- Monitor error rates
- Monitor response times
- Monitor database connections
- Monitor memory/CPU usage

**Step 5: Keep Blue as Backup**
- Keep blue environment running for 24 hours
- If issues detected, switch back to blue
- After 24 hours, decommission blue

---

## 6. Data Seeding

### 6.1 Master Data Setup

**Wards:**
```sql
INSERT INTO wards (name, code, ward_type, floor_number, department, total_capacity, gender_restriction) VALUES
('General Ward A', 'GWA', 'general', 2, 'General Medicine', 40, 'mixed'),
('General Ward B', 'GWB', 'general', 2, 'General Medicine', 40, 'mixed'),
('Private Ward', 'PVT', 'private', 3, 'General Medicine', 20, 'mixed'),
('ICU', 'ICU', 'icu', 3, 'Critical Care', 10, 'mixed'),
('Emergency Ward', 'EMG', 'emergency', 1, 'Emergency', 15, 'mixed');
```

**Rooms (Example for General Ward A):**
```sql
INSERT INTO rooms (room_number, ward_id, room_type, bed_capacity, rate_per_day, amenities) VALUES
('201', (SELECT id FROM wards WHERE code = 'GWA'), 'double', 2, 1500, '["ac", "tv"]'),
('202', (SELECT id FROM wards WHERE code = 'GWA'), 'double', 2, 1500, '["ac", "tv"]'),
('203', (SELECT id FROM wards WHERE code = 'GWA'), 'single', 1, 2500, '["ac", "tv", "attached_bath"]');
-- ... more rooms
```

**Beds (Example for Room 201):**
```sql
INSERT INTO beds (bed_number, room_id, bed_type, status) VALUES
('201-A', (SELECT id FROM rooms WHERE room_number = '201' AND ward_id = (SELECT id FROM wards WHERE code = 'GWA')), 'standard', 'available'),
('201-B', (SELECT id FROM rooms WHERE room_number = '201' AND ward_id = (SELECT id FROM wards WHERE code = 'GWA')), 'standard', 'available');
-- ... more beds
```

**Services (Billing):**
```sql
INSERT INTO services (name, code, category, price) VALUES
('Admission Fee', 'ADM_FEE', 'admission', 1000),
('Room Rent - General (Double)', 'ROOM_GEN_DBL', 'room_rent', 1500),
('Room Rent - General (Single)', 'ROOM_GEN_SGL', 'room_rent', 2500),
('Room Rent - ICU', 'ROOM_ICU', 'room_rent', 5000);
```

### 6.2 Test Data (Staging Only)

**Test Patients:**
```sql
-- Create 50 test patients
-- (Use faker or similar library to generate realistic data)
```

**Test Users:**
```sql
-- Doctors, nurses, admission clerks, billing staff
INSERT INTO users (email, name, role, password_hash) VALUES
('doctor1@test.com', 'Dr. Test Doctor', 'DOCTOR', '$2b$10$...'),
('nurse1@test.com', 'Test Nurse', 'NURSE', '$2b$10$...'),
('reception1@test.com', 'Test Reception', 'RECEPTION', '$2b$10$...');
```

---

## 7. Phased Rollout Plan

### 7.1 Phase 1: Pilot Ward (Week 1)

**Scope:**
- Single ward (General Ward A)
- Limited users (2 doctors, 2 nurses, 1 reception staff)
- 40 beds

**Objectives:**
- Validate complete workflow
- Identify issues in real environment
- Gather user feedback
- Fine-tune performance

**Success Criteria:**
- Zero critical bugs
- < 2 high-priority bugs
- 90%+ user satisfaction
- All workflows completed successfully

**Monitoring:**
- Daily check-ins with pilot users
- Real-time error monitoring
- Performance metrics tracking
- User feedback collection

**Rollback Trigger:**
- Critical data loss
- System unavailable > 1 hour
- Workflow blocking bugs
- User request to rollback

---

### 7.2 Phase 2: Full Rollout (Week 2)

**Scope:**
- All wards
- All users
- All beds

**Preparation:**
1. Fix all issues from pilot
2. Conduct training for remaining staff
3. Prepare support team
4. Schedule go-live date/time

**Go-Live:**
- **Date:** TBD (Monday, 9:00 AM)
- **Duration:** 1 hour maintenance window
- **Communication:** Notify all users 1 week in advance

**Post Go-Live:**
- On-site support for first 3 days
- Daily status meetings for first week
- Weekly review for first month

---

## 8. Go-Live Checklist

### 8.1 Pre-Deployment (1 Week Before)

- [ ] All UAT test cases passed
- [ ] No critical or high-priority bugs
- [ ] Performance testing completed
- [ ] Security audit completed
- [ ] Backup strategy verified
- [ ] Rollback plan documented
- [ ] Monitoring dashboards configured
- [ ] Alert rules configured
- [ ] User training completed
- [ ] User documentation finalized
- [ ] Support team trained
- [ ] Go-live communication sent to users

---

### 8.2 Deployment Day (D-Day)

**Morning (8:00 AM - 9:00 AM):**
- [ ] Notify users of maintenance window
- [ ] Put system in maintenance mode
- [ ] Take database backup
- [ ] Run database migrations
- [ ] Verify migrations successful
- [ ] Deploy new application version
- [ ] Seed master data (wards, rooms, beds)
- [ ] Run smoke tests
- [ ] Verify all services running
- [ ] Remove maintenance mode
- [ ] Notify users system is live

**Day 1 (9:00 AM - 5:00 PM):**
- [ ] Monitor error rates (target: < 0.1%)
- [ ] Monitor response times (target: < 2s)
- [ ] Monitor database performance
- [ ] Monitor server resources
- [ ] On-site support available
- [ ] Collect user feedback
- [ ] Address urgent issues immediately

**Evening (5:00 PM - 6:00 PM):**
- [ ] Review day's metrics
- [ ] Review error logs
- [ ] Review user feedback
- [ ] Plan fixes for next day
- [ ] Status update to stakeholders

---

### 8.3 Post-Deployment (Week 1)

**Daily:**
- [ ] Morning standup with team
- [ ] Monitor system health
- [ ] Review error logs
- [ ] Address reported issues
- [ ] Evening status update

**End of Week:**
- [ ] Review week's metrics
- [ ] User satisfaction survey
- [ ] Identify improvement areas
- [ ] Plan enhancements
- [ ] Stakeholder presentation

---

## 9. Monitoring & Alerts

### 9.1 Application Monitoring

**Metrics:**
- Request rate (requests/second)
- Response time (p50, p95, p99)
- Error rate (%)
- Active users
- Database connections
- Memory usage
- CPU usage

**Tools:**
- AWS CloudWatch
- Sentry (error tracking)
- New Relic (APM)
- Grafana (dashboards)

---

### 9.2 Alert Rules

| Alert | Condition | Severity | Action |
|-------|-----------|----------|--------|
| High Error Rate | Error rate > 1% for 5 min | Critical | Page on-call engineer |
| Slow Response | p95 > 5s for 5 min | High | Notify team |
| Database Down | DB connection fails | Critical | Page on-call engineer |
| High CPU | CPU > 80% for 10 min | Medium | Notify team |
| Disk Space Low | Disk > 80% full | High | Notify ops team |
| Failed Deployment | Deployment fails | Critical | Rollback automatically |

---

### 9.3 Monitoring Dashboard

**Key Metrics:**
```
┌─────────────────────────────────────────────────────────┐
│ MedFlow IPD Module - Production Dashboard              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ System Health:        🟢 Healthy                        │
│ Uptime:               99.98%                            │
│ Active Users:         45                                │
│                                                         │
│ ┌─────────────────┐ ┌─────────────────┐               │
│ │ Request Rate    │ │ Response Time   │               │
│ │ 120 req/s       │ │ p95: 1.2s       │               │
│ └─────────────────┘ └─────────────────┘               │
│                                                         │
│ ┌─────────────────┐ ┌─────────────────┐               │
│ │ Error Rate      │ │ Active Admissions│               │
│ │ 0.05%           │ │ 85               │               │
│ └─────────────────┘ └─────────────────┘               │
│                                                         │
│ Recent Errors: 2 (last hour)                           │
│ • BED_NOT_AVAILABLE (1)                                │
│ • VALIDATION_ERROR (1)                                 │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. Rollback Procedures

### 10.1 Application Rollback

**Trigger Conditions:**
- Critical bug affecting operations
- Data corruption detected
- System unavailable > 30 minutes
- User request (if pilot)

**Rollback Steps:**
```bash
# 1. Switch load balancer back to blue environment
aws elbv2 modify-listener --listener-arn ... --default-actions Type=forward,TargetGroupArn=...blue

# 2. Verify blue environment healthy
curl https://api.medflow.com/health

# 3. Monitor for 15 minutes
# 4. If stable, decommission green

# 5. Notify users of rollback
```

**Time to Rollback:** < 5 minutes

---

### 10.2 Database Rollback

**Trigger Conditions:**
- Data corruption
- Migration failure
- Critical data loss

**Rollback Steps:**
```bash
# 1. Put system in maintenance mode
# 2. Stop application servers
# 3. Restore database from backup
psql -h production-db.amazonaws.com -U medflow -d medflow_prod < backup_pre_phase2.sql

# 4. Verify data integrity
# 5. Revert application to previous version
# 6. Start application servers
# 7. Remove maintenance mode
# 8. Notify users
```

**Time to Rollback:** 15-30 minutes (depending on database size)

---

## 11. Communication Plan

### 11.1 Pre-Deployment Communication

**1 Week Before:**
```
Subject: MedFlow Phase 2 - IPD Admission Module Go-Live

Dear Team,

We are excited to announce the launch of Phase 2: IPD Admission Module on [DATE] at 9:00 AM.

What's New:
• Complete admission workflow (request → approval → bed allocation)
• Ward patient management dashboard
• Bed status board with real-time updates
• Discharge process with billing integration
• And more...

Training Sessions:
• [Date/Time] - Doctors
• [Date/Time] - Nurses
• [Date/Time] - Reception Staff

Maintenance Window:
• Date: [DATE]
• Time: 8:00 AM - 9:00 AM
• System will be unavailable during this time

Support:
• On-site support available for first 3 days
• Helpdesk: support@medflow.com
• Emergency: +1-XXX-XXX-XXXX

Thank you for your cooperation.

MedFlow Team
```

**1 Day Before:**
```
Subject: Reminder: MedFlow Phase 2 Go-Live Tomorrow

Quick reminder that Phase 2 goes live tomorrow at 9:00 AM.

Please ensure:
✓ You have attended training
✓ You have reviewed user documentation
✓ You have your login credentials ready

See you tomorrow!
```

---

### 11.2 Go-Live Communication

**Morning of Go-Live:**
```
Subject: MedFlow Maintenance - System Unavailable (8:00-9:00 AM)

The system is currently under maintenance for Phase 2 deployment.

Expected completion: 9:00 AM

We will notify you once the system is back online.
```

**After Go-Live:**
```
Subject: MedFlow Phase 2 is Now Live!

Great news! Phase 2 is now live and ready to use.

Access the new IPD module at: https://medflow.com/ipd

Need help? Our support team is on-site today.

Let's make this a success together!
```

---

### 11.3 Post-Deployment Communication

**End of Day 1:**
```
Subject: Phase 2 Day 1 - Thank You!

Thank you for your patience and cooperation on Day 1 of Phase 2.

Today's Highlights:
• 15 admissions processed successfully
• 8 discharges completed
• 3 bed transfers
• Average response time: 1.2s

Known Issues:
• Minor UI issue with bed filter (fix deployed)

Tomorrow's Focus:
• Continue monitoring
• Address any reported issues

Keep the feedback coming!
```

**End of Week 1:**
```
Subject: Phase 2 Week 1 - Success!

We've completed Week 1 of Phase 2. Here's a summary:

Metrics:
• 85 admissions processed
• 78 discharges completed
• 25 bed transfers
• 99.9% uptime
• 95% user satisfaction

Thank you for making this a success!

Next Steps:
• Full rollout to all wards (if pilot)
• Continued improvements based on feedback

Cheers,
MedFlow Team
```

---

## 12. Support Plan

### 12.1 Support Levels

**Level 1: On-site Support (First 3 Days)**
- On-site support staff available
- Immediate issue resolution
- User guidance and training

**Level 2: Remote Support (Week 1)**
- Remote support via helpdesk
- Response time: < 1 hour
- Issue resolution: < 4 hours

**Level 3: Standard Support (After Week 1)**
- Helpdesk support
- Response time: < 4 hours
- Issue resolution: < 24 hours

---

### 12.2 Support Contacts

| Role | Name | Contact | Availability |
|------|------|---------|--------------|
| Technical Lead | TBD | +1-XXX-XXX-XXXX | 24/7 (Week 1) |
| Support Engineer | TBD | support@medflow.com | Business hours |
| Product Owner | TBD | +1-XXX-XXX-XXXX | Business hours |
| On-Call Engineer | TBD | +1-XXX-XXX-XXXX | 24/7 |

---

## 13. Success Metrics

### 13.1 Technical Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Uptime | 99.9% | |
| Response Time (p95) | < 2s | |
| Error Rate | < 0.1% | |
| Database Query Time | < 500ms | |
| Page Load Time | < 3s | |

### 13.2 Business Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Admissions Processed | 100+ (Week 1) | |
| Discharges Completed | 90+ (Week 1) | |
| User Adoption | 100% | |
| User Satisfaction | 90%+ | |
| Support Tickets | < 20 (Week 1) | |

### 13.3 User Feedback

- Conduct user satisfaction survey at end of Week 1
- Collect feedback via helpdesk
- Weekly feedback sessions with key users

---

## 14. Post-Deployment Review

### 14.1 Review Meeting (End of Week 1)

**Agenda:**
1. Review metrics (technical & business)
2. Review user feedback
3. Discuss issues encountered
4. Identify improvements
5. Plan next steps

**Attendees:**
- Product Owner
- Technical Lead
- QA Lead
- Key hospital staff
- Support team

---

### 14.2 Lessons Learned

**Document:**
- What went well
- What didn't go well
- What could be improved
- Action items for future deployments

---

> **Note:** This deployment plan should be reviewed and approved by all stakeholders before execution.
