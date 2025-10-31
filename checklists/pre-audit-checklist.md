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

### Control: CC6.1 - Logical Access Security

**Description:**
We restrict logical access to AWS resources through AWS IAM, enforcing multi-factor authentication and least-privilege access principles.

**Implementation:**
- [ ] All users authenticate via IAM Identity Center (SSO) with mandatory MFA
- [ ] Access is granted through IAM roles, not long-term credentials
- [ ] IAM Access Analyzer continuously monitors for overly permissive policies
- [ ] Access is reviewed quarterly by resource owners

**Ownership:**
- [ ] Security Team: IAM policy standards and monitoring
- [ ] Engineering Managers: Access approvals for their teams
- [ ] HR: Notification of terminations

**Evidence of Operation:**
- [ ] IAM credential reports showing MFA enforcement
- [ ] Quarterly access review sign-offs
- [ ] IAM Access Analyzer findings (remediated)
- [ ] CloudTrail logs of access activities

### Third-Party Risk Documentation

- [ ] List of all vendors with access to systems/data
- [ ] SOC 2 reports from critical vendors (AWS, payment processors, etc.)
- [ ] Vendor assessment questionnaires completed
- [ ] Contracts with security clauses
- [ ] Vendor access review documentation

### Critical Vendors Checklist:

- [ ] AWS (get latest SOC 2 report from Artifact)
- [ ] GitHub/GitLab (development)
- [ ] Datadog/monitoring tools
- [ ] Payment processors
- [ ] Email service (SendGrid, SES)
- [ ] Auth provider (Auth0, Okta)


### Incident Response Documentation

- [ ] Incident response plan is current
- [ ] Incident response team roster
- [ ] Tabletop exercise results (annual requirement)
- [ ] Runbooks for common scenarios


### Week 3: Sample Selection and Organization
Prepare Population Lists for Sampling
Auditors will sample from these populations:
### Access Management (CC6.2):
```bash
#Generate list of all IAM user/role creations during audit period
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser \
  --start-time 2023-04-01 \
  --end-time 2024-03-31 \
  --max-results 100 \
  --output json > iam-user-creations-audit-period.json

# Generate list of terminations
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=DeleteUser \
  --start-time 2023-04-01 \
  --end-time 2024-03-31 \
  --max-results 100 \
  --output json > iam-user-deletions-audit-period.json
```
Expected samples: 25 new hires, 25 terminations

## For each sampled individual, prepare:

 - **HR ticket requesting access**
 - **Manager approval**
 - **CloudTrail event showing IAM creation**
 - **Assigned permissions (IAM groups/roles)**
 - **Most recent access review showing this user**


### Change Management (CC8.1):
```bash
# List all CloudFormation stack changes
aws cloudformation describe-stacks --output json | \
  jq -r '.Stacks[] | [.StackName, .LastUpdatedTime] | @csv'

# List Config timeline events
aws configservice describe-configuration-recorder-status
```
Expected samples: 25 changes

## For each sampled change, prepare:

  - **Change ticket (Jira/ServiceNow)**
  - **Approval from change advisory board**
  - **CloudTrail event or deployment log**
  - **Config timeline before/after**
  - **Test results (if applicable)**


### Week 4: Final Preparation and Stakeholder Readiness


#### Audit Logistics

**Tasks:**
- [ ] Schedule audit kickoff meeting  
- [ ] Reserve conference rooms (for onsite portion)  
- [ ] Create dedicated audit team channel (Slack/Teams)  
- [ ] Set up shared folder for auditor requests  
- [ ] Provide auditors with VPN access (if required)  
- [ ] Create audit calendar with key dates and milestones  

---

#### Stakeholder Briefings

**Brief These Teams:**
- [ ] Engineering leadership — expectations and communication protocol  
- [ ] HR — employee interview preparation  
- [ ] Finance — payment processing and related controls  
- [ ] Customer Support — data handling and process awareness (if in scope)  

**Key Messages to Emphasize:**
- Auditors may request interviews at any time  
- It’s okay to say “I don’t know” — honesty is key  
- Direct technical questions to the security team  
- The audit is a normal business function, not an investigation  

---

#### Prepare Audit Response Team

**Primary Contact (You):**
- [ ] Block calendar for entire audit period  
- [ ] Establish rapid-response workflow for auditor requests  
- [ ] Set up a “war room” with all documentation ready and accessible  

**Subject Matter Experts (SMEs):**

| Area | SME Name | Role | Slack Handle | Cell | Notes |
|------|-----------|------|---------------|------|-------|
| AWS / Cloud Infrastructure | [Name] | Infrastructure Lead | @handle | ###-###-#### |  |
| Application Development | [Name] | Lead Developer | @handle | ###-###-#### |  |
| Security Operations | [Name] | Security Engineer | @handle | ###-###-#### |  |
| HR / Access Management | [Name] | HR Manager | @handle | ###-###-#### |  |
| Change Management | [Name] | DevOps Lead | @handle | ###-###-#### |  |

**Action Items:**
- [ ] Finalize contact list with all SMEs  
- [ ] Distribute to audit response team  
- [ ] Confirm availability during audit period  

---

#### Evidence Package Assembly

**Objective:**  
Compile all final documentation and evidence in clearly labeled folders for easy auditor access.

**Packages to Assemble:**

##### **Package 1: Company Overview**
- [ ] Organization chart  
- [ ] System description document  
- [ ] Network and data flow diagrams  
- [ ] Technology inventory  
- [ ] Vendor list  

##### **Package 2: Policies and Procedures**
- [ ] Information Security Policy  
- [ ] Access Control Policy  
- [ ] Change Management Policy  
- [ ] Incident Response Policy  
- [ ] Business Continuity Policy  
- [ ] Acceptable Use Policy  
- [ ] Data Classification Policy  

##### **Package 3: Control Matrices**
- [ ] SOC 2 control matrix (criteria → controls → evidence)  
- [ ] Risk assessment documentation  
- [ ] Control test results summary  

##### **Package 4: Access Control Evidence**
- [ ] IAM credential reports (quarterly)  
- [ ] Access review results  
- [ ] Onboarding/offboarding tickets (sampled)  
- [ ] MFA configuration screenshots  

##### **Package 5: System Operations Evidence**
- [ ] Security Hub dashboards  
- [ ] Vulnerability scan results  
- [ ] Patch compliance reports  
- [ ] Sample security incidents (with response documentation)  

##### **Package 6: Change Management Evidence**
- [ ] Change management procedures  
- [ ] Sample change tickets (25)  
- [ ] Configuration timelines  
- [ ] Emergency change documentation  

##### **Package 7: Availability Evidence**
- [ ] Backup configurations  
- [ ] Disaster recovery test results  
- [ ] RTO/RPO documentation  
- [ ] Capacity monitoring dashboards  



#### Create Auditor FAQ Document
**File:** `/documentation/auditor-faq.md`

**Purpose:**  
Anticipate and prepare responses to common auditor questions for consistency and clarity.

**Template:**
```markdown
# Anticipated Auditor Questions - Quick Reference

## Access Control

**Q: How do you ensure MFA is enforced?**
A: IAM Identity Center enforces MFA at authentication. See screenshots in Package 4, files CC6.1-MFA-config-*.png. Additionally, IAM Access Analyzer alerts on any policies that don't require MFA.

**Q: How often do you review access?**
A: Quarterly. See access review results in Package 4, dated Q1-2023, Q2-2023, Q3-2023, Q4-2023, Q1-2024.

**Q: What happens when an employee leaves?**
A: HR notifies security@ via ServiceNow. Security team revokes AWS access within 24 hours. See sample terminations in Package 4, with tickets and CloudTrail deletion events.

## System Operations

**Q: How do you detect security incidents?**
A: Multi-layered approach:
1. GuardDuty for threat detection
2. Security Hub for compliance monitoring
3. CloudWatch alarms for operational issues
4. Config for configuration drift

Evidence: Package 5, CC7.2-detection-architecture.png

**Q: Walk me through a recent security incident**
A: See Package 5, folder "Sample-Incidents". Recommend incident-2024-02-15 as it shows complete lifecycle from detection → response → resolution → lessons learned.

## Change Management

**Q: Do you follow change management for emergency changes?**
A: Yes. Emergency changes require VP approval (vs. CAB approval for standard changes). Changes are still documented, tested (if feasible), and reviewed post-implementation. See emergency change procedure in Package 6.

**Q: How do you prevent unauthorized changes?**
A: AWS Config detects all configuration changes. Changes not preceded by a change ticket trigger alerts to security team. See Config rules in Package 6, CC8.1-change-detection-rules.json.

## Availability

**Q: How do you know your backups work?**
A: We test restores quarterly. See DR test results in Package 7. Most recent test: 2024-01-15, successfully restored production database to test environment, validated data integrity, documented lessons learned.

**Q: What is your RTO/RPO?**
A: 
- Critical systems (payment processing): RTO 4 hours, RPO 1 hour
- Standard systems (customer portal): RTO 24 hours, RPO 24 hours
- Non-critical systems: RTO 48 hours, RPO 48 hours

See RTO/RPO matrix in Package 7.
