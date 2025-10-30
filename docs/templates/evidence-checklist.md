# Evidence Collection Checklist

A practical checklist for collecting SOC 2 audit evidence throughout the audit period.

## Monthly Tasks

### CloudTrail Evidence
- [ ] Export CloudTrail logs for the month
- [ ] Verify CloudTrail is enabled and logging
- [ ] Check log file validation is enabled
- [ ] Confirm logs are encrypted
- [ ] Review for any security events

**Storage Location**: `/evidence/YYYY-MM/cloudtrail/`

---

### AWS Config Evidence
- [ ] Export Config compliance report
- [ ] Screenshot Config dashboard showing compliance percentage
- [ ] Export list of non-compliant resources (if any)
- [ ] Document remediation of non-compliant items
- [ ] Export Config rule configurations

**Storage Location**: `/evidence/YYYY-MM/config/`

---

### Access Management Evidence
- [ ] Export IAM credential report
- [ ] Verify all users have MFA enabled
- [ ] List any new users added this month
- [ ] List any users removed/disabled this month
- [ ] Screenshot showing password policy

**Storage Location**: `/evidence/YYYY-MM/iam/`

---

### Monitoring Evidence
- [ ] Export CloudWatch alarm history
- [ ] Screenshot key CloudWatch dashboards
- [ ] Export any alert notifications sent
- [ ] Document any incidents and responses
- [ ] Export CloudWatch Logs Insights queries

**Storage Location**: `/evidence/YYYY-MM/monitoring/`

---

### Backup Evidence
- [ ] Export AWS Backup job success logs
- [ ] Screenshot showing backup vault configuration
- [ ] List backup retention settings
- [ ] Verify cross-region replication status
- [ ] Document any backup failures and resolution

**Storage Location**: `/evidence/YYYY-MM/backups/`

---

### Security Findings Evidence
- [ ] Export GuardDuty findings
- [ ] Export Security Hub compliance scores
- [ ] Screenshot showing finding remediation
- [ ] Document any high/critical findings and resolution
- [ ] Export Macie findings (if running scans)

**Storage Location**: `/evidence/YYYY-MM/security/`

---

### Network Security Evidence
- [ ] Export security group configurations
- [ ] Export VPC Flow Logs summaries
- [ ] Screenshot WAF metrics and blocked requests
- [ ] Export Network ACL configurations
- [ ] Document any network changes

**Storage Location**: `/evidence/YYYY-MM/network/`

---

### Encryption Evidence
- [ ] List all KMS keys
- [ ] Verify key rotation is enabled
- [ ] Export S3 bucket encryption settings
- [ ] Verify EBS default encryption is enabled
- [ ] Export RDS encryption status

**Storage Location**: `/evidence/YYYY-MM/encryption/`

---

## Quarterly Tasks

### Access Reviews
- [ ] Generate list of all users and their permissions
- [ ] Review each user's access level
- [ ] Document any access changes made
- [ ] Get sign-off from managers on team access
- [ ] Create access review report with date and signatures

**Storage Location**: `/evidence/YYYY-QX/access-reviews/`

---

### Backup Testing
- [ ] Select resources for restore test
- [ ] Perform restore from backup
- [ ] Validate restored data integrity
- [ ] Measure and document RTO
- [ ] Create restore test report with screenshots
- [ ] Get sign-off on successful test

**Storage Location**: `/evidence/YYYY-QX/backup-tests/`

---

### Vulnerability Scanning
- [ ] Run AWS Inspector assessment
- [ ] Export vulnerability findings
- [ ] Document remediation plan for findings
- [ ] Track remediation progress
- [ ] Re-scan after remediation

**Storage Location**: `/evidence/YYYY-QX/vulnerability-scans/`

---

### Capacity Reviews
- [ ] Review CloudWatch capacity metrics
- [ ] Run AWS Compute Optimizer
- [ ] Review AWS Trusted Advisor recommendations
- [ ] Document capacity planning decisions
- [ ] Create capacity review report

**Storage Location**: `/evidence/YYYY-QX/capacity-reviews/`

---

### Policy Reviews
- [ ] Review all security policies
- [ ] Update policies if needed
- [ ] Track policy version changes
- [ ] Get leadership approval on policies
- [ ] Communicate policy updates to team

**Storage Location**: `/evidence/YYYY-QX/policy-reviews/`

---

## Annual Tasks

### Disaster Recovery Testing
- [ ] Review and update DR plan
- [ ] Schedule DR test with stakeholders
- [ ] Execute DR failover to secondary region
- [ ] Validate all systems functional
- [ ] Measure actual RTO/RPO
- [ ] Switch back to primary region
- [ ] Create comprehensive DR test report
- [ ] Document lessons learned
- [ ] Update DR procedures based on findings

**Storage Location**: `/evidence/YYYY/dr-tests/`

---

### Penetration Testing
- [ ] Engage penetration testing firm
- [ ] Define scope of testing
- [ ] Execute penetration test
- [ ] Receive penetration test report
- [ ] Create remediation plan for findings
- [ ] Track remediation to completion
- [ ] Request re-test for critical findings

**Storage Location**: `/evidence/YYYY/penetration-tests/`

---

### Training Records
- [ ] Compile all security awareness training completions
- [ ] Track AWS certifications obtained
- [ ] Document technical training sessions
- [ ] Create training completion report
- [ ] Identify training gaps for next year

**Storage Location**: `/evidence/YYYY/training/`

---

### Third-Party Reviews
- [ ] Review all vendor contracts
- [ ] Obtain SOC 2 reports from critical vendors
- [ ] Conduct vendor risk assessments
- [ ] Update vendor inventory
- [ ] Document Data Processing Agreements

**Storage Location**: `/evidence/YYYY/vendor-management/`

---

### Risk Assessment
- [ ] Conduct annual risk assessment
- [ ] Update risk register
- [ ] Document new risks identified
- [ ] Review and update risk treatment plans
- [ ] Get leadership approval on risk acceptance
- [ ] Create risk assessment report

**Storage Location**: `/evidence/YYYY/risk-assessments/`

---

## One-Time/As-Needed Tasks

### Infrastructure Changes
- [ ] Document the change request
- [ ] Capture change approval
- [ ] Export before/after configurations
- [ ] Screenshot AWS Config timeline
- [ ] Document testing performed
- [ ] Capture deployment logs

**Storage Location**: `/evidence/YYYY-MM/changes/change-XXXX/`

---

### Security Incidents
- [ ] Create incident ticket
- [ ] Document incident timeline
- [ ] Capture all relevant logs
- [ ] Screenshot GuardDuty/Security Hub findings
- [ ] Document investigation steps
- [ ] Document remediation actions
- [ ] Create post-incident review report
- [ ] Update incident response procedures

**Storage Location**: `/evidence/YYYY-MM/incidents/incident-XXXX/`

---

### New Employee Onboarding
- [ ] Access request form (approved)
- [ ] IAM user creation CloudTrail event
- [ ] Screenshot of user's IAM permissions
- [ ] MFA enrollment confirmation
- [ ] Security training completion
- [ ] Signed acceptable use policy
- [ ] Equipment inventory assignment

**Storage Location**: `/evidence/YYYY-MM/onboarding/[employee-name]/`

---

### Employee Offboarding
- [ ] Termination notification (HR)
- [ ] IAM user deactivation timestamp
- [ ] CloudTrail event showing access removal
- [ ] Screenshot showing no active sessions
- [ ] Equipment return confirmation
- [ ] Final access review showing no access

**Storage Location**: `/evidence/YYYY-MM/offboarding/[employee-name]/`

---

## Evidence Quality Checklist

Before submitting evidence, verify:

- [ ] All evidence includes dates/timestamps
- [ ] Screenshots are clear and readable
- [ ] Logs are complete for the period
- [ ] No PII/PHI exposed (redact if needed)
- [ ] Evidence is organized by control
- [ ] File names are descriptive
- [ ] Evidence supports the control objective
- [ ] No gaps in time series evidence

---

## Automation Opportunities

Consider automating:
- ✅ Monthly CloudTrail log exports
- ✅ Config compliance report generation
- ✅ IAM credential report generation
- ✅ Security finding exports
- ✅ Backup success/failure reports
- ✅ Evidence organization and tagging
- ✅ Evidence index generation

**Tools**: AWS Lambda, EventBridge, Systems Manager Automation, AWS Audit Manager

---

## Evidence Retention

**Minimum Retention**: 7 years (per SOC 2 requirements)

**Storage**:
- Primary: S3 bucket with versioning
- Encryption: SSE-KMS
- Access: Limited to compliance team
- Backup: Cross-region replication
- Lifecycle: Transition to Glacier after 1 year

---

## Pre-Audit Checklist

Two weeks before audit:

- [ ] Verify all monthly tasks completed
- [ ] Verify all quarterly tasks completed
- [ ] Verify all annual tasks completed
- [ ] Organize evidence by control
- [ ] Create evidence index
- [ ] Review for completeness
- [ ] Redact sensitive information
- [ ] Prepare evidence package
- [ ] Test evidence access for auditor
- [ ] Brief team on audit process

---

## Notes

**Responsible Party**: [Name]  
**Audit Start Date**: [Date]  
**Audit End Date**: [Date]  
**Evidence Period**: [Start Date] to [End Date]  

**Tips**:
- Collect evidence continuously, not just before audit
- Use consistent naming conventions
- Document everything, even if unsure if needed
- Err on the side of over-documentation
- Keep raw data and processed reports
- Maintain chain of custody for evidence

[← Back to Evidence Collection Guide](../evidence-collection.md) | [Back to Main Guide](../../README.md)
