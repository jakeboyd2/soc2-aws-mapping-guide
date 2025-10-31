# SOC 2 Audit Evidence Collection Guide for AWS

## Purpose
This guide provides a systematic approach to collecting evidence for SOC 2 Type 2 audits. Collecting evidence continuously (not just during audit prep) reduces stress and demonstrates mature processes.

## Evidence Collection Strategy

### Timing
- **Continuous:** Automated evidence collection runs daily/weekly
- **Quarterly:** Manually review and package evidence
- **Pre-Audit:** Final validation 2 weeks before audit kickoff

### Storage
- Create S3 bucket: `soc2-audit-evidence-[year]`
- Enable versioning and encryption
- Structure: `/{control-family}/{control-id}/{YYYY-MM-DD}/`
- Retention: 7 years minimum

---

## CC1: Control Environment

### CC1.1: Integrity and Ethical Values

**Evidence Type:** Policies and procedures
**Collection Method:** Manual upload
**Frequency:** Annual or when updated

**What to Collect:**
- [ ] Code of conduct document
- [ ] Ethics training completion records
- [ ] Background check policy
- [ ] Board meeting minutes (sanitized) showing ethics discussions

**Storage Location:** `/CC1/CC1.1/policies/`

**Audit Tip:** Auditors want to see that people actually read these, not just that they exist. Include training completion reports.

---

### CC1.2: Board Independence

**Evidence Type:** Governance documents
**Collection Method:** Manual

**What to Collect:**
- [ ] Board composition documentation
- [ ] Board meeting minutes showing security oversight
- [ ] Board charter or bylaws
- [ ] Security steering committee notes

**Storage Location:** `/CC1/CC1.2/governance/`

---

### CC1.3: Organizational Structure

**Evidence Type:** Organizational charts and role definitions
**Collection Method:** Manual

**What to Collect:**
- [ ] Organization chart with security team
- [ ] Security team roles and responsibilities
- [ ] AWS account access matrix
- [ ] Delegation of authority documentation

**Storage Location:** `/CC1/CC1.3/org-structure/`

**AWS-Specific Evidence:**
```bash
# Export IAM users and their group memberships
aws iam get-account-authorization-details > iam-structure-$(date +%Y-%m-%d).json

# Export AWS Organizations structure
aws organizations describe-organization > org-structure-$(date +%Y-%m-%d).json
```

---

### CC1.4: Commitment to Competence

**Evidence Type:** Training records
**Collection Method:** Manual + screenshots

**What to Collect:**
- [ ] AWS training completion certificates
- [ ] Security awareness training records
- [ ] Technical skill matrix for team
- [ ] AWS certification status

**Storage Location:** `/CC1/CC1.4/training/`

---

### CC1.5: Accountability

**Evidence Type:** Performance evaluations and incentive structures
**Collection Method:** Manual (sanitized)

**What to Collect:**
- [ ] Security performance metrics
- [ ] Performance review templates
- [ ] Incident response participation records
- [ ] Security goal achievement documentation

---

## CC6: Logical and Physical Access Controls

### CC6.1: Logical Access Security

**Evidence Type:** Configuration screenshots + exports
**Collection Method:** Automated + manual screenshots
**Frequency:** Monthly

**What to Collect:**

#### IAM Configuration
```bash
# Generate IAM credential report (shows MFA, password age, access key age)
aws iam generate-credential-report
aws iam get-credential-report --output text --query 'Content' | base64 --decode > iam-credential-report-$(date +%Y-%m-%d).csv
```

- [ ] IAM credential report CSV
- [ ] Screenshot of IAM password policy
- [ ] IAM Access Analyzer findings (should be empty or remediated)
- [ ] Screenshot of root account MFA configuration
- [ ] List of IAM users with console access
- [ ] List of IAM roles and their trust policies

#### IAM Identity Center (SSO)
- [ ] Screenshot of SSO user portal
- [ ] MFA enforcement configuration
- [ ] Permission sets list
- [ ] SAML provider configuration (if applicable)
- [ ] SSO access logs sample

**Automated Collection Script:**
```python
import boto3
import json
from datetime import datetime

iam = boto3.client('iam')
date_str = datetime.now().strftime('%Y-%m-%d')

# Get password policy
password_policy = iam.get_account_password_policy()
with open(f'iam-password-policy-{date_str}.json', 'w') as f:
    json.dump(password_policy, f, indent=2, default=str)

# Get all IAM users
users = iam.list_users()
with open(f'iam-users-{date_str}.json', 'w') as f:
    json.dump(users, f, indent=2, default=str)

# Get MFA devices
mfa_devices = iam.list_virtual_mfa_devices()
with open(f'iam-mfa-devices-{date_str}.json', 'w') as f:
    json.dump(mfa_devices, f, indent=2, default=str)
```

**Storage Location:** `/CC6/CC6.1/iam-config/YYYY-MM-DD/`

---

### CC6.2: User Registration and Authorization

**Evidence Type:** User lifecycle documentation
**Collection Method:** Manual + automated reports
**Frequency:** Monthly

**What to Collect:**
- [ ] New user onboarding requests (tickets)
- [ ] Access approval workflows (ServiceNow/Jira screenshots)
- [ ] Termination checklist completed forms
- [ ] Quarterly access review results

**AWS-Specific Evidence:**
```bash
# Get CloudTrail events for IAM user/role creation
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser \
  --start-time $(date -d '30 days ago' +%Y-%m-%d) \
  --max-results 50 > iam-user-creation-events.json
```

**Audit Tip:** Auditors will sample 25 employees. For each, be ready to show:
1. Onboarding ticket with manager approval
2. IAM user/role creation CloudTrail event
3. Assigned permission sets
4. Most recent access review

---

### CC6.3: Authorization (Least Privilege)

**Evidence Type:** Permission analysis
**Collection Method:** Automated analysis
**Frequency:** Monthly

**What to Collect:**
- [ ] IAM Access Analyzer findings
- [ ] IAM policy documents
- [ ] Security group rules export
- [ ] Unused credential report

**Automated Collection:**
```bash
# Find IAM users with Administrator access
aws iam get-account-authorization-details \
  --filter User \
  --query 'UserDetailList[?AttachedManagedPolicies[?PolicyName==`AdministratorAccess`]].[UserName]' \
  --output table

# Get all security groups and their rules
aws ec2 describe-security-groups \
  --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions]' \
  --output json > security-groups-$(date +%Y-%m-%d).json

# Get IAM policies that are too permissive (contain *)
aws iam list-policies --scope Local \
  --query 'Policies[*].[PolicyName,Arn]' \
  --output table
```

**Storage Location:** `/CC6/CC6.3/least-privilege/YYYY-MM-DD/`

---

### CC6.4: Physical Access

**Evidence Type:** Data center documentation
**Collection Method:** Manual from AWS + your office

**What to Collect:**
- [ ] AWS SOC 2 report (covers their data centers)
- [ ] AWS compliance certificates
- [ ] Your office physical access logs
- [ ] Badge access system screenshots (if self-hosted servers)

**Audit Tip:** For AWS workloads, you can rely on AWS's SOC 2 report for physical controls. Focus your evidence on:
- How you verified AWS's controls (reviewed their report)
- Your own office/facility access if you have on-prem components

---

### CC6.6: External Access Protection

**Evidence Type:** Network security configuration
**Collection Method:** Automated exports
**Frequency:** Monthly

**What to Collect:**
```bash
# Get all internet-facing resources
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[?PublicIpAddress!=`null`].[InstanceId,PublicIpAddress,SecurityGroups]' \
  --output table

# Get VPC configuration
aws ec2 describe-vpcs --output json > vpcs-$(date +%Y-%m-%d).json

# Get NACLs
aws ec2 describe-network-acls --output json > nacls-$(date +%Y-%m-%d).json

# Get WAF rules
aws wafv2 list-web-acls --scope REGIONAL --output json > waf-rules-$(date +%Y-%m-%d).json
```

- [ ] Security group rules (especially port 22, 3389, 443)
- [ ] NACL configurations
- [ ] WAF rules and rate limiting
- [ ] VPN configuration (if applicable)
- [ ] GuardDuty findings related to network threats

**Storage Location:** `/CC6/CC6.6/network-security/YYYY-MM-DD/`

---

### CC6.7: Data Transmission Protection

**Evidence Type:** Encryption configuration
**Collection Method:** Automated + screenshots
**Frequency:** Monthly

**What to Collect:**
```bash
# Get SSL/TLS configuration for load balancers
aws elbv2 describe-load-balancers --output json > load-balancers-$(date +%Y-%m-%d).json

aws elbv2 describe-listeners \
  --load-balancer-arn <ARN> \
  --query 'Listeners[*].[Protocol,Port,Certificates]' \
  --output table

# Get S3 bucket encryption status
aws s3api list-buckets --query 'Buckets[*].Name' --output text | \
while read bucket; do
  echo "Bucket: $bucket"
  aws s3api get-bucket-encryption --bucket $bucket 2>/dev/null || echo "  No encryption"
done
```

- [ ] Certificate Manager certificates
- [ ] Load balancer HTTPS listeners
- [ ] S3 bucket encryption settings
- [ ] RDS encryption at rest config
- [ ] VPN encryption settings

**Storage Location:** `/CC6/CC6.7/encryption-transit/YYYY-MM-DD/`

---

### CC6.8: Malware Protection

**Evidence Type:** Scanning configuration and results
**Collection Method:** Automated + manual
**Frequency:** Weekly

**What to Collect:**
- [ ] GuardDuty findings export (malware detections)
- [ ] Inspector scan results
- [ ] Third-party antivirus reports (if used on EC2)
- [ ] ECR image scan results
```bash
# Get GuardDuty findings
aws guardduty list-detectors --output text | while read detector; do
  aws guardduty list-findings --detector-id $detector --output json > guardduty-findings-$(date +%Y-%m-%d).json
done

# Get Inspector findings
aws inspector2 list-findings --output json > inspector-findings-$(date +%Y-%m-%d).json
```

**Storage Location:** `/CC6/CC6.8/malware-protection/YYYY-MM-DD/`

---

## CC7: System Operations

### CC7.1: Vulnerability Management

**Evidence Type:** Scan results and remediation tracking
**Collection Method:** Automated
**Frequency:** Weekly

**What to Collect:**
```bash
# Systems Manager patch compliance
aws ssm describe-instance-patch-states \
  --query 'InstancePatchStates[*].[InstanceId,PatchGroup,InstalledCount,InstalledPendingRebootCount,MissingCount]' \
  --output table > patch-compliance-$(date +%Y-%m-%d).txt

# Inspector vulnerability findings
aws inspector2 list-findings \
  --filter-criteria '{"severity":[{"comparison":"EQUALS","value":"CRITICAL"},{"comparison":"EQUALS","value":"HIGH"}]}' \
  --output json > inspector-vulnerabilities-$(date +%Y-%m-%d).json
```

- [ ] Weekly vulnerability scan results
- [ ] Patch compliance dashboard screenshot
- [ ] Remediation tracking (Jira board screenshot)
- [ ] Systems Manager patch groups configuration

**Storage Location:** `/CC7/CC7.1/vulnerability-scans/YYYY-MM-DD/`

**Audit Tip:** Show a closed-loop process:
1. Scan detects vulnerability → Evidence: scan report
2. Ticket created → Evidence: Jira ticket
3. Patch applied → Evidence: Config timeline showing fix
4. Re-scan confirms fix → Evidence: subsequent scan

---

### CC7.2: Security Monitoring

**Evidence Type:** Monitoring configuration and alert samples
**Collection Method:** Screenshots + config exports
**Frequency:** Monthly

**What to Collect:**
- [ ] Security Hub dashboard screenshot
- [ ] CloudWatch alarms list
- [ ] EventBridge rules for security events
- [ ] SNS topic subscriptions (who gets alerts)
- [ ] Sample security alerts (sanitized)
- [ ] GuardDuty configuration
```bash
# Get CloudWatch alarms
aws cloudwatch describe-alarms --output json > cloudwatch-alarms-$(date +%Y-%m-%d).json

# Get EventBridge rules
aws events list-rules --output json > eventbridge-rules-$(date +%Y-%m-%d).json

# Get Security Hub enabled standards
aws securityhub get-enabled-standards --output json > securityhub-standards-$(date +%Y-%m-%d).json
```

**Storage Location:** `/CC7/CC7.2/monitoring-config/YYYY-MM-DD/`

---

### CC7.3: Incident Detection and Analysis

**Evidence Type:** Incident records and response procedures
**Collection Method:** Manual from incident management system
**Frequency:** As incidents occur

**What to Collect:**
- [ ] Incident response playbooks
- [ ] Security incident tickets (sanitized)
- [ ] Post-incident review reports
- [ ] Timeline of detection → response → resolution

**For Each Security Incident:**
- Ticket number and severity
- Detection method (which alert/tool found it)
- Response timeline
- Root cause analysis
- Remediation actions
- Lessons learned

**Audit Tip:** Auditors will sample 2-5 security incidents. Even "false positives" count as good evidence if you documented why they were false positives.

**Storage Location:** `/CC7/CC7.3/incident-responses/YYYY-MM-DD/`

---

### CC7.4: Incident Response

**Evidence Type:** Response procedures and evidence of execution
**Collection Method:** Manual

**What to Collect:**
- [ ] Incident response plan document
- [ ] Runbooks for common scenarios
- [ ] Contact list for incident response team
- [ ] Tabletop exercise results (quarterly)
- [ ] Incident retrospectives

**Storage Location:** `/CC7/CC7.4/incident-response-procedures/`

---

### CC7.5: Incident Recovery

**Evidence Type:** Recovery procedures and test results
**Collection Method:** Manual

**What to Collect:**
- [ ] Disaster recovery plan
- [ ] Recovery time objective (RTO) documentation
- [ ] Recovery point objective (RPO) documentation
- [ ] DR test results (quarterly)
- [ ] AWS Backup restore test results
```bash
# Document backup configuration
aws backup list-backup-plans --output json > backup-plans-$(date +%Y-%m-%d).json

# Get recent backup jobs
aws backup list-backup-jobs \
  --by-created-after $(date -d '7 days ago' --iso-8601) \
  --output json > recent-backups-$(date +%Y-%m-%d).json
```

**Storage Location:** `/CC7/CC7.5/recovery-evidence/YYYY-MM-DD/`

---

## CC8: Change Management

### CC8.1: Change Management Process

**Evidence Type:** Change records and approvals
**Collection Method:** Automated + manual
**Frequency:** Continuous

**What to Collect:**
- [ ] Change management policy
- [ ] Sample change tickets (Jira/ServiceNow)
- [ ] CloudFormation/Terraform change logs
- [ ] Config timeline showing changes
- [ ] Emergency change procedures
```bash
# Get CloudFormation stack change sets
aws cloudformation list-stacks --output json > cloudformation-stacks-$(date +%Y-%m-%d).json

# Get Config timeline for a resource
aws configservice get-resource-config-history \
  --resource-type AWS::EC2::SecurityGroup \
  --resource-id sg-xxxxxxxxx \
  --later-time $(date -d '30 days ago' --iso-8601) \
  --output json > config-change-history-sg.json
```

**For Each Change (Sample 25):**
- Change ticket with approval
- What changed (CloudTrail event)
- Who made the change
- Config timeline before/after

**Storage Location:** `/CC8/CC8.1/change-records/YYYY-MM-DD/`

**Audit Tip:** Auditors will trace a change from ticket → approval → implementation → verification. Keep your change tickets detailed!

---

## A1: Availability

### A1.1: Capacity Management

**Evidence Type:** Monitoring data and forecasting
**Collection Method:** Automated
**Frequency:** Monthly

**What to Collect:**
```bash
# Get CloudWatch metrics for key resources
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-xxxxxxxxx \
  --start-time $(date -d '30 days ago' --iso-8601) \
  --end-time $(date --iso-8601) \
  --period 3600 \
  --statistics Average,Maximum \
  --output json > cpu-utilization-$(date +%Y-%m-%d).json
```

- [ ] CloudWatch dashboards showing capacity trends
- [ ] Auto-scaling policies and triggers
- [ ] Resource utilization reports
- [ ] Capacity planning documentation

**Storage Location:** `/A1/A1.1/capacity-monitoring/YYYY-MM-DD/`

---

### A1.2: Environmental Protections and Backup

**Evidence Type:** Backup configuration and test results
**Collection Method:** Automated + manual
**Frequency:** Monthly

**What to Collect:**
```bash
# AWS Backup configuration
aws backup list-backup-plans --output json > backup-plans-$(date +%Y-%m-%d).json

# Recent successful backups
aws backup list-backup-jobs \
  --by-state COMPLETED \
  --by-created-after $(date -d '30 days ago' --iso-8601) \
  --output json > completed-backups-$(date +%Y-%m-%d).json
```

- [ ] Backup plans and schedules
- [ ] Backup success/failure reports
- [ ] Cross-region backup configuration
- [ ] Backup retention policies

**Storage Location:** `/A1/A1.2/backups/YYYY-MM-DD/`

---

### A1.3: Recovery Testing

**Evidence Type:** DR test results
**Collection Method:** Manual
**Frequency:** Quarterly

**What to Collect:**
- [ ] DR test plan
- [ ] Test execution checklist
- [ ] RTO/RPO achievement results
- [ ] Restore test results
- [ ] Lessons learned documentation

**Audit Tip:** Even if your DR test doesn't go perfectly, document what went wrong and how you fixed it. This shows continuous improvement.

**Storage Location:** `/A1/A1.3/dr-tests/YYYY-MM-DD/`

---

## C1: Confidentiality

### C1.1: Confidential Information Identification

**Evidence Type:** Data classification documentation
**Collection Method:** Manual
**Frequency:** Annual or when updated

**What to Collect:**
- [ ] Data classification policy
- [ ] Data inventory (what confidential data you store)
- [ ] S3 bucket classifications
- [ ] Database schema showing sensitive fields
- [ ] Tagging standards for confidential data

**Storage Location:** `/C1/C1.1/data-classification/`

---

### C1.2: Confidential Information Disposal

**Evidence Type:** Disposal procedures and records
**Collection Method:** Manual
**Frequency:** As disposals occur

**What to Collect:**
- [ ] Data retention and disposal policy
- [ ] S3 lifecycle policies
- [ ] Records of data disposal (which buckets/databases)
- [ ] Secure delete procedures for EBS volumes
```bash
# Document S3 lifecycle policies
aws s3api get-bucket-lifecycle-configuration --bucket <bucket-name> --output json
```

**Storage Location:** `/C1/C1.2/data-disposal/YYYY-MM-DD/`

---

## PI1: Processing Integrity

### PI1.1-1.5: Processing Controls

**Evidence Type:** Application controls and monitoring
**Collection Method:** Varies by application

**What to Collect:**
- [ ] Input validation rules
- [ ] Processing logic documentation
- [ ] Error handling procedures
- [ ] Data quality monitoring
- [ ] Output verification procedures

**Note:** Processing integrity is highly application-specific. Focus on:
- How you ensure data is processed completely
- How you ensure data is processed accurately
- How you ensure data is processed in a timely manner

---

## P1-P8: Privacy

### Privacy Evidence

**Note:** Privacy controls are complex and often require legal review. Key evidence includes:

- [ ] Privacy policy (external-facing)
- [ ] Cookie consent mechanisms
- [ ] Data subject access request (DSAR) procedures
- [ ] Records of DSAR fulfillment
- [ ] Data processing agreements with vendors
- [ ] Privacy impact assessments

---

## Evidence Collection Automation

### Daily Automated Collection Script

Save this as `scripts/daily-evidence-collection.sh`:
```bash
#!/bin/bash

# Daily AWS Evidence Collection Script
# Run via CloudWatch Events / EventBridge

DATE=$(date +%Y-%m-%d)
EVIDENCE_BUCKET="s3://soc2-audit-evidence-2024"

# Create daily directory structure
aws s3api put-object --bucket soc2-audit-evidence-2024 --key daily-evidence/${DATE}/

# Collect IAM evidence
echo "Collecting IAM evidence..."
aws iam generate-credential-report
aws iam get-credential-report --output text --query 'Content' | base64 --decode > /tmp/iam-credential-report-${DATE}.csv
aws s3 cp /tmp/iam-credential-report-${DATE}.csv ${EVIDENCE_BUCKET}/CC6/CC6.1/iam-credentials/${DATE}/

# Collect Security Hub findings
echo "Collecting Security Hub findings..."
aws securityhub get-findings \
  --filters '{"ComplianceStatus":[{"Value":"FAILED","Comparison":"EQUALS"}]}' \
  --output json > /tmp/securityhub-findings-${DATE}.json
aws s3 cp /tmp/securityhub-findings-${DATE}.json ${EVIDENCE_BUCKET}/CC7/CC7.2/security-hub/${DATE}/

# Collect Config compliance
echo "Collecting Config compliance..."
aws configservice describe-compliance-by-config-rule --output json > /tmp/config-compliance-${DATE}.json
aws s3 cp /tmp
