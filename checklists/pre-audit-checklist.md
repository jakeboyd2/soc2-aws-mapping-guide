``` bash
aws s3api put-object-tagging \
  --bucket soc2-audit-evidence-2024 \
  --key CC6/CC6.1/iam-credentials/2024-01-15/iam-credential-report.csv \
  --tagging 'TagSet=[{Key=Control,Value=CC6.1},{Key=EvidenceType,Value=Automated},{Key=CollectionDate,Value=2024-01-15}]'
```

---

## Pre-Audit Package Preparation

**2 Weeks Before Audit Kickoff:**

Create these compiled evidence packages:

### Package 1: Control Environment (CC1)
- [ ] Organization chart
- [ ] Board meeting minutes (sanitized)
- [ ] Security policies
- [ ] Training records
- [ ] Background check policy

### Package 2: Access Controls (CC6)
- [ ] IAM credential reports (last 3 months)
- [ ] Access review results (quarterly)
- [ ] Sample onboarding/offboarding tickets
- [ ] MFA enforcement screenshots
- [ ] Encryption configurations

### Package 3: Monitoring & Operations (CC7)
- [ ] Security Hub dashboards
- [ ] GuardDuty findings
- [ ] Sample security incidents
- [ ] Vulnerability scan results
- [ ] Patch compliance reports

### Package 4: Change Management (CC8)
- [ ] Change management policy
- [ ] Sample change tickets (25)
- [ ] Config timeline for sample changes
- [ ] Emergency change procedures

### Package 5: Availability (A1)
- [ ] Backup configurations
- [ ] DR test results
- [ ] RTO/RPO documentation
- [ ] Capacity monitoring dashboards

---

## Common Auditor Requests and Where to Find Evidence

| Auditor Request | Evidence Location | Collection Method |
|-----------------|-------------------|-------------------|
| "Show me your IAM password policy" | `/CC6/CC6.1/iam-config/` | Screenshot + export |
| "Who has Administrator access?" | IAM credential report | Automated |
| "Show me a sample change from last quarter" | `/CC8/CC8.1/change-records/` | Jira ticket + CloudTrail |
| "How do you detect security incidents?" | `/CC7/CC7.2/monitoring-config/` | Screenshots + config exports |
| "Show me a security incident and how you responded" | `/CC7/CC7.3/incident-responses/` | Incident ticket + timeline |
| "How often do you test backups?" | `/A1/A1.3/dr-tests/` | DR test reports |
| "Who can access production data?" | IAM policies + CloudTrail | Policy documents + access logs |
| "How do you ensure data is encrypted?" | `/CC6/CC6.7/encryption-transit/` | KMS config + S3 encryption |

---

## Evidence Collection Calendar

Set these recurring calendar reminders:

**Daily (Automated):**
- Evidence collection script runs at 2 AM

**Weekly:**
- Review evidence collection summary
- Verify no gaps in automated collection

**Monthly (1st of month):**
- Run monthly snapshot script
- Review and organize evidence
- Check storage bucket size

**Quarterly:**
- Capture manual screenshots
- Review and update documentation
- Conduct access reviews
- Test disaster recovery
- Package evidence for review

**Pre-Audit (2 weeks before):**
- Create evidence packages
- Validate completeness
- Prepare sample selections

---

This evidence collection guide ensures you're audit-ready year-round, not just during audit season. The key is automation + consistent organization.


---

Now create the third checklist: `/checklists/pre-audit-checklist.md`:

# SOC 2 Pre-Audit Readiness Checklist

## Purpose
Complete this checklist 2-4 weeks before your SOC 2 audit kickoff to ensure you're fully prepared. This checklist assumes you've been following the evidence collection guide throughout the year.

## Timeline: 4 Weeks Before Audit

### Week 1: Evidence Review and Gap Analysis

#### Day 1-2: Validate Automated Evidence Collection
- [ ] Verify evidence collection scripts ran successfully for entire audit period
- [ ] Check S3 bucket for gaps in daily evidence
- [ ] Review error logs from Lambda functions
- [ ] Re-run any failed collection jobs

**Validation Script:**
```bash
#!/bin/bash
# Check for gaps in daily evidence collection

EVIDENCE_BUCKET="s3://soc2-audit-evidence-2024"
START_DATE="2023-04-01"  # Audit period start
END_DATE="2024-03-31"    # Audit period end

current=$START_DATE
while [ "$current" != "$END_DATE" ]; do
  # Check if daily evidence exists
  aws s3 ls ${EVIDENCE_BUCKET}/daily-evidence/${current}/ > /dev/null 2>&1
  if [ $? -ne 0 ]; then
    echo "MISSING: Evidence for ${current}"
  fi
  current=$(date -I -d "$current + 1 day")
done
```

---

#### Day 3-4: Control-by-Control Evidence Validation

**CC1: Control Environment**
- [ ] Organization chart is current (within 3 months)
- [ ] All required policies are current and approved
- [ ] Board meeting minutes cover security oversight
- [ ] Training records are complete for all employees
- [ ] Background check documentation for all hires during audit period

**CC6: Logical and Physical Access Controls**
- [ ] IAM credential report for last day of audit period
- [ ] Quarterly access reviews completed and documented
- [ ] Sample 25 new hires: onboarding tickets + IAM creation events
- [ ] Sample 25 terminations: offboarding tickets + IAM deletion events
- [ ] MFA enforcement evidenced in screenshots
- [ ] Root account access logs (should be minimal/zero)

**CC7: System Operations**
- [ ] Security Hub dashboard screenshots (monthly)
- [ ] GuardDuty findings documented and remediated
- [ ] Vulnerability scan results (weekly for audit period)
- [ ] Sample 5 security incidents: tickets + response + resolution
- [ ] Patch compliance reports (monthly)

**CC8: Change Management**
- [ ] Sample 25 changes: tickets + approvals + CloudTrail events
- [ ] Emergency change procedures documented with examples
- [ ] Config timeline for major infrastructure changes

**A1: Availability**
- [ ] Backup success rate reports (should be >99%)
- [ ] DR test results (quarterly requirement)
- [ ] RTO/RPO documentation is current
- [ ] Capacity monitoring dashboards

---

#### Day 5: Create Missing Evidence

If you found gaps, collect missing evidence now:
```bash
# Re-generate missing IAM credential reports
for month in {1..12}; do
  # Note: You can only get current state, not historical
  # Document why historical data is unavailable
  echo "IAM credential reports cannot be regenerated for past dates"
done

# Re-run Config queries for historical compliance
aws configservice get-compliance-details-by-config-rule \
  --config-rule-name root-account-mfa-enabled \
  --compliance-types NON_COMPLIANT \
  --output json
```

**For truly missing evidence:**
- Document why it's missing
- Explain compensating controls
- Commit to fixing for next audit

---

### Week 2: Documentation and Narrative Preparation

#### System Description Document
Create or update `/documentation/system-description.md`:

- [ ] Infrastructure overview diagram (current architecture)
- [ ] Service boundaries (what's in scope for SOC 2)
- [ ] Data flows (how data moves through your system)
- [ ] Third-party services list
- [ ] Technologies and tools inventory

#### Control Narratives
For each control, prepare a narrative describing:
1. What the control is
2. How you implement it
3. Who owns it
4. How you know it's working

**Template:**
````
Control: CC6.1 - Logical Access Security

Description: 
We restrict logical access to AWS resources through AWS IAM, enforcing multi-factor authentication and least-privilege access principles.

Implementation:
- All users authenticate via IAM Identity Center (SSO) with mandatory MFA
- Access is granted through IAM roles, not long-term credentials
- IAM Access Analyzer continuously monitors for overly permissive policies
- Access is reviewed quarterly by resource owners

Ownership:
- Security Team: IAM policy standards and monitoring
- Engineering Managers: Access approvals for their teams
- HR: Notification of terminations

Evidence of Operation:
- IAM credential reports showing MFA enforcement
- Quarterly access review sign-offs
- IAM Access Analyzer findings (remediated)
- CloudTrail logs of access activities
