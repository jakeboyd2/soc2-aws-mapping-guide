# Evidence Collection Guide

A comprehensive guide for collecting and organizing evidence for SOC 2 Type II audits.

## Overview

SOC 2 Type II audits require evidence demonstrating that controls operated effectively over a period of time (typically 6-12 months). This guide helps you collect, organize, and present evidence to auditors.

---

## Evidence Collection Principles

### 1. The Three E's
- **Existence**: Does the control exist?
- **Effectiveness**: Does it work as designed?
- **Execution**: Has it been operating over the audit period?

### 2. Evidence Types
- **Screenshots**: AWS console configurations
- **Logs**: CloudTrail, CloudWatch, Config
- **Reports**: Automated compliance reports
- **Documents**: Policies, procedures, architecture diagrams
- **Records**: Meeting minutes, training records, incident reports
- **Attestations**: Management sign-offs, third-party reports

### 3. Best Practices
- ✅ Collect evidence continuously, not just before audit
- ✅ Automate evidence collection where possible
- ✅ Organize evidence by control
- ✅ Include timestamps on all evidence
- ✅ Maintain evidence for 7+ years
- ✅ Protect evidence from tampering (read-only S3 bucket)

---

## Evidence by Control Category

## Common Criteria (CC)

### CC1: Control Environment

#### CC1.1 - Commitment to Integrity
**Evidence Required**:
- ✅ Code of Conduct document
- ✅ Employee acknowledgment records
- ✅ Ethics training materials and completion records
- ✅ AWS Organizations SCP configurations
- ✅ CloudTrail logs showing policy enforcement

**Collection Method**:
```bash
# Export SCPs
aws organizations list-policies --filter SERVICE_CONTROL_POLICY

# Screenshot: AWS Organizations console showing SCP assignments
# Document: Code of Conduct (PDF with version and date)
# Spreadsheet: Training completion tracker
```

**Frequency**: Annual policy review, ongoing training

#### CC1.2 - Board Oversight
**Evidence Required**:
- ✅ Board meeting minutes (discussing security/compliance)
- ✅ Security review presentations
- ✅ Audit committee charter
- ✅ CloudWatch dashboards for executive reporting

**Collection Method**:
```bash
# Export dashboard
aws cloudwatch get-dashboard --dashboard-name Executive-Security-Dashboard

# Screenshot: Dashboard with current security metrics
# Document: Board meeting minutes (redacted as needed)
```

**Frequency**: Quarterly board meetings

#### CC1.3 - Organizational Structure
**Evidence Required**:
- ✅ Organizational chart
- ✅ Job descriptions (security-related roles)
- ✅ AWS Organizations account structure
- ✅ IAM role definitions and assignments
- ✅ RACI matrix for security responsibilities

**Collection Method**:
```bash
# Export AWS Organizations structure
aws organizations describe-organization
aws organizations list-accounts

# Export IAM roles
aws iam list-roles > iam-roles.json

# Document: Org chart, RACI matrix, job descriptions
```

**Frequency**: Annual or when changes occur

#### CC1.4 - Commitment to Competence
**Evidence Required**:
- ✅ Training program documentation
- ✅ AWS certification tracking
- ✅ Training completion records
- ✅ Performance review documentation
- ✅ Competency matrix

**Collection Method**:
```
# Spreadsheet: Employee certifications tracker
# Documents: Training materials, completion certificates
# Screenshots: AWS Skill Builder progress
```

**Frequency**: Ongoing training, annual reviews

#### CC1.5 - Accountability
**Evidence Required**:
- ✅ CloudTrail logs showing user activities
- ✅ Access review logs (quarterly)
- ✅ Performance metrics tied to security
- ✅ Incident response assignments and outcomes

**Collection Method**:
```bash
# Query CloudTrail for user activities
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=Username,AttributeValue=john.doe \
  --start-time 2024-01-01 \
  --end-time 2024-03-31 > user-activities-q1.json

# Document: Access review sign-off sheets
# Spreadsheet: Incident response metrics by person
```

**Frequency**: Quarterly access reviews, continuous monitoring

---

### CC6: Logical and Physical Access Controls

#### CC6.1 - Access Authentication
**Evidence Required**:
- ✅ IAM user list with MFA status
- ✅ Password policy configuration
- ✅ IAM policies (least privilege)
- ✅ Secrets Manager configurations
- ✅ KMS key policies

**Collection Method**:
```bash
# Generate IAM credential report
aws iam generate-credential-report
aws iam get-credential-report > credential-report.csv

# Export password policy
aws iam get-account-password-policy > password-policy.json

# Export IAM policies
aws iam list-policies --scope Local > custom-policies.json

# Screenshot: IAM console showing MFA devices
# Screenshot: Secrets Manager showing rotation enabled
```

**Frequency**: Monthly for credential reports, continuous monitoring

#### CC6.2 - User Access Provisioning
**Evidence Required**:
- ✅ User provisioning procedures
- ✅ Access request forms (approved)
- ✅ CloudTrail logs of user creation
- ✅ IAM user creation audit trail
- ✅ Onboarding checklist

**Collection Method**:
```bash
# Query CloudTrail for CreateUser events
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser \
  --max-results 50 > user-creation-events.json

# Documents: Access request forms with approvals
# Checklist: IT onboarding checklist (template and completed)
```

**Frequency**: Per user onboarding event

#### CC6.3 - User Access Removal
**Evidence Required**:
- ✅ Access review documentation (quarterly)
- ✅ CloudTrail logs of user deactivation
- ✅ Terminated user list
- ✅ Access revocation within SLA (e.g., same day)
- ✅ IAM Access Analyzer reports

**Collection Method**:
```bash
# Query CloudTrail for DeleteUser events
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=DeleteUser > user-deletion-events.json

# Run IAM Access Analyzer
aws accessanalyzer list-findings > access-analyzer-findings.json

# Document: Access review sign-off (quarterly)
# Spreadsheet: Termination tracker with revocation timestamps
```

**Frequency**: Quarterly reviews, immediate on termination

#### CC6.6 - Network Security
**Evidence Required**:
- ✅ VPC architecture diagram
- ✅ Security group configurations
- ✅ Network ACL configurations
- ✅ VPC Flow Logs
- ✅ WAF rule configurations
- ✅ Network Firewall policies

**Collection Method**:
```bash
# Export security groups
aws ec2 describe-security-groups > security-groups.json

# Export network ACLs
aws ec2 describe-network-acls > network-acls.json

# Export VPC Flow Logs configuration
aws ec2 describe-flow-logs > flow-logs-config.json

# Export WAF rules
aws wafv2 list-web-acls > waf-acls.json

# Diagram: VPC architecture (use draw.io, Lucidchart)
# Screenshot: VPC console showing subnets and route tables
```

**Frequency**: Monthly or when changes occur

#### CC6.7 - Data Transmission Security
**Evidence Required**:
- ✅ ACM certificate inventory
- ✅ TLS/SSL configurations
- ✅ Load balancer listener configurations
- ✅ CloudFront distribution settings (HTTPS only)
- ✅ S3 bucket policies (HTTPS enforcement)

**Collection Method**:
```bash
# List ACM certificates
aws acm list-certificates > acm-certificates.json

# Export load balancer listeners
aws elbv2 describe-listeners --load-balancer-arn <arn> > alb-listeners.json

# Export CloudFront distributions
aws cloudfront list-distributions > cloudfront-distributions.json

# Screenshot: Load balancer listener showing SSL policy
# Screenshot: CloudFront settings showing "Redirect HTTP to HTTPS"
```

**Frequency**: Monthly

---

### CC7: System Operations

#### CC7.2 - Logging and Monitoring
**Evidence Required**:
- ✅ CloudTrail configuration (all regions, multi-region trail)
- ✅ CloudTrail log file validation enabled
- ✅ Log retention configuration (1+ years)
- ✅ Sample CloudTrail logs (API calls, access patterns)
- ✅ CloudWatch Logs configuration
- ✅ VPC Flow Logs configuration
- ✅ Config recording status

**Collection Method**:
```bash
# Verify CloudTrail status
aws cloudtrail describe-trails > cloudtrail-config.json
aws cloudtrail get-trail-status --name <trail-name> > trail-status.json

# Export sample logs
aws cloudtrail lookup-events \
  --start-time 2024-01-01 \
  --max-results 100 > sample-cloudtrail-logs.json

# Verify AWS Config
aws configservice describe-configuration-recorders > config-recorders.json
aws configservice describe-delivery-channels > config-delivery.json

# Screenshot: CloudTrail console showing enabled status
# Screenshot: S3 bucket lifecycle policy (log retention)
```

**Frequency**: Monthly verification, continuous logs

#### CC7.3 - Security Incident Response
**Evidence Required**:
- ✅ Incident response plan
- ✅ Incident logs (tickets, timestamps)
- ✅ GuardDuty findings and remediation
- ✅ Security Hub findings and response
- ✅ Post-incident reviews
- ✅ Runbooks for common scenarios

**Collection Method**:
```bash
# Export GuardDuty findings
aws guardduty list-findings \
  --detector-id <detector-id> \
  --finding-criteria file://criteria.json > guardduty-findings.json

# Export Security Hub findings
aws securityhub get-findings > securityhub-findings.json

# Documents: Incident response plan, runbooks
# Tickets: Jira/ServiceNow tickets for incidents
# Documents: Post-incident review reports
```

**Frequency**: Per incident, monthly review

#### CC7.4 - Backup and Recovery
**Evidence Required**:
- ✅ Backup policy documentation (RPO/RTO)
- ✅ AWS Backup plan configurations
- ✅ Backup job success logs
- ✅ Restore test documentation (quarterly)
- ✅ Backup retention configurations
- ✅ Cross-region replication evidence

**Collection Method**:
```bash
# Export backup plans
aws backup list-backup-plans > backup-plans.json

# Get backup job status
aws backup list-backup-jobs \
  --by-created-after 2024-01-01 \
  --by-created-before 2024-03-31 > backup-jobs-q1.json

# Get restore job status
aws backup list-restore-jobs > restore-jobs.json

# Screenshot: AWS Backup console showing successful backups
# Document: Restore test report with timestamps and outcomes
```

**Frequency**: Daily backup logs, quarterly restore tests

---

### CC8: Change Management

#### CC8.1 - Change Authorization and Testing
**Evidence Required**:
- ✅ Change management policy
- ✅ Change request records (approved)
- ✅ AWS Config timeline showing changes
- ✅ CloudFormation change sets
- ✅ CodePipeline execution history
- ✅ Test results for changes

**Collection Method**:
```bash
# Get Config timeline
aws configservice get-resource-config-history \
  --resource-type AWS::EC2::SecurityGroup \
  --resource-id sg-12345 > sg-change-history.json

# Get CloudFormation change sets
aws cloudformation list-change-sets \
  --stack-name production-stack > change-sets.json

# Get CodePipeline executions
aws codepipeline list-pipeline-executions \
  --pipeline-name production-pipeline > pipeline-executions.json

# Documents: Change request forms with approvals
# Screenshots: Change Manager approval workflow
```

**Frequency**: Per change, weekly summary

---

## Availability (A)

### A1.3 - Backup and Restoration
**Evidence Required**:
- ✅ RPO/RTO definitions
- ✅ Backup configurations
- ✅ Successful backup logs (daily/weekly)
- ✅ Restore test results (quarterly minimum)
- ✅ Backup monitoring alerts

**Collection Method**:
```bash
# See CC7.4 above for backup evidence

# Additional for availability:
# RDS automated backup configuration
aws rds describe-db-instances > rds-instances.json

# EBS snapshot policy
aws dlm get-lifecycle-policies > ebs-snapshot-policies.json

# Document: RPO/RTO matrix
# Document: Restore test report (step-by-step with screenshots)
```

**Frequency**: Daily backup logs, quarterly restore tests

---

## Confidentiality (C)

### C2.1 - Encryption at Rest
**Evidence Required**:
- ✅ KMS key inventory
- ✅ Key rotation configuration
- ✅ S3 bucket encryption settings
- ✅ EBS encryption enabled
- ✅ RDS encryption enabled
- ✅ Config rules for encryption compliance

**Collection Method**:
```bash
# List KMS keys
aws kms list-keys > kms-keys.json
aws kms describe-key --key-id <key-id> > key-details.json

# Get encryption settings
aws s3api get-bucket-encryption --bucket <bucket-name> > bucket-encryption.json
aws ec2 get-ebs-encryption-by-default > ebs-encryption-default.json

# Config compliance for encryption
aws configservice describe-compliance-by-config-rule \
  --config-rule-names encrypted-volumes > encryption-compliance.json

# Screenshot: KMS console showing key rotation enabled
# Screenshot: S3 bucket showing default encryption
```

**Frequency**: Monthly

### C2.2 - Encryption in Transit
**Evidence Required**:
- ✅ ACM certificate list
- ✅ TLS policy configurations
- ✅ HTTPS enforcement evidence
- ✅ Load balancer SSL settings

**Collection Method**: See CC6.7 above

---

## Processing Integrity (PI)

### PI1.2 - Processing Completeness
**Evidence Required**:
- ✅ Step Functions workflow definitions
- ✅ Execution success rates
- ✅ Error handling configurations
- ✅ Dead letter queue metrics
- ✅ Reconciliation reports

**Collection Method**:
```bash
# List Step Functions executions
aws stepfunctions list-executions \
  --state-machine-arn <arn> \
  --status-filter SUCCEEDED > successful-executions.json

# Get SQS DLQ metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/SQS \
  --metric-name ApproximateNumberOfMessagesVisible \
  --dimensions Name=QueueName,Value=my-dlq \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-03-31T23:59:59Z \
  --period 86400 \
  --statistics Average > dlq-metrics.json

# Screenshot: Step Functions console showing success rate
# Document: Reconciliation report (inputs vs outputs)
```

**Frequency**: Daily metrics, monthly reports

---

## Privacy (P)

### P2.1 - Consent Collection
**Evidence Required**:
- ✅ Consent collection UI (screenshots)
- ✅ Consent records (sample, anonymized)
- ✅ Consent database schema
- ✅ Privacy policy (versioned)
- ✅ Consent withdrawal process

**Collection Method**:
```bash
# Query DynamoDB for consent records (anonymized)
aws dynamodb scan \
  --table-name consent_records \
  --projection-expression "consent_type,consent_given,timestamp" \
  --limit 10 > sample-consent-records.json

# Screenshot: Signup page showing privacy policy checkbox
# Screenshot: User profile page showing consent preferences
# Document: Privacy policy (PDF with version and date)
```

**Frequency**: Continuous collection, quarterly review

### P5.1 - Data Subject Access Requests
**Evidence Required**:
- ✅ DSAR process documentation
- ✅ Access request logs
- ✅ Export completion records
- ✅ Response time metrics (must meet SLA)
- ✅ Sample export files (anonymized)

**Collection Method**:
```bash
# Query logs for DSAR API calls
aws logs filter-log-events \
  --log-group-name /aws/lambda/dsar-handler \
  --filter-pattern "DSAR request" \
  --start-time 1640995200000 > dsar-requests.json

# Screenshot: DSAR portal UI
# Document: DSAR process flowchart
# Spreadsheet: DSAR tracker (request date, completion date, SLA met)
```

**Frequency**: Per request, quarterly summary

---

## Evidence Organization Structure

### Recommended Folder Structure
```
SOC2-Evidence/
├── 00-Overview/
│   ├── System-Description.pdf
│   ├── Architecture-Diagrams/
│   └── Control-Matrix.xlsx
├── 01-Policies/
│   ├── Information-Security-Policy.pdf
│   ├── Access-Control-Policy.pdf
│   ├── Change-Management-Policy.pdf
│   └── ... (all policies)
├── 02-Common-Criteria/
│   ├── CC1-Control-Environment/
│   │   ├── CC1.1/
│   │   │   ├── Code-of-Conduct.pdf
│   │   │   ├── Training-Records.xlsx
│   │   │   └── Screenshots/
│   │   ├── CC1.2/
│   │   └── ...
│   ├── CC2-Communication/
│   ├── CC3-Risk-Assessment/
│   └── ...
├── 03-Availability/
│   └── ...
├── 04-Confidentiality/
│   └── ...
├── 05-Processing-Integrity/
│   └── ...
├── 06-Privacy/
│   └── ...
├── 07-Logs/
│   ├── 2024-Q1/
│   │   ├── CloudTrail/
│   │   ├── Config/
│   │   ├── GuardDuty/
│   │   └── Backup/
│   ├── 2024-Q2/
│   └── ...
└── 08-Testing/
    ├── Backup-Restore-Tests/
    ├── Vulnerability-Scans/
    ├── Access-Reviews/
    └── DR-Tests/
```

---

## Automated Evidence Collection

### Using AWS Audit Manager
```bash
# Create assessment
aws auditmanager create-assessment \
  --name "SOC2-2024" \
  --assessment-reports-destination destinationType=S3,destination=s3://evidence-bucket \
  --scope awsAccounts='[{id=123456789012}]',awsServices='[{serviceName=S3},{serviceName=EC2}]' \
  --roles roleType=PROCESS_OWNER,roleArn=arn:aws:iam::123456789012:role/AuditManager \
  --framework-id arn:aws:auditmanager:us-east-1:123456789012:framework/SOC2

# List evidence folders
aws auditmanager get-evidence-folders-by-assessment \
  --assessment-id <assessment-id>

# Export evidence
aws auditmanager get-evidence \
  --assessment-id <assessment-id> \
  --control-set-id <control-set-id> \
  --evidence-folder-id <folder-id> \
  --evidence-id <evidence-id>
```

### Custom Evidence Collection Script
```python
import boto3
import json
from datetime import datetime, timedelta

def collect_monthly_evidence():
    """Automate monthly evidence collection"""
    
    # CloudTrail evidence
    cloudtrail = boto3.client('cloudtrail')
    events = cloudtrail.lookup_events(
        StartTime=datetime.now() - timedelta(days=30),
        MaxResults=1000
    )
    
    # Config compliance
    config = boto3.client('config')
    compliance = config.describe_compliance_by_config_rule()
    
    # GuardDuty findings
    guardduty = boto3.client('guardduty')
    detectors = guardduty.list_detectors()
    findings = guardduty.list_findings(
        DetectorId=detectors['DetectorIds'][0],
        FindingCriteria={
            'Criterion': {
                'createdAt': {
                    'Gte': int((datetime.now() - timedelta(days=30)).timestamp() * 1000)
                }
            }
        }
    )
    
    # Save to S3
    s3 = boto3.client('s3')
    evidence = {
        'collection_date': datetime.now().isoformat(),
        'cloudtrail_events': events,
        'config_compliance': compliance,
        'guardduty_findings': findings
    }
    
    s3.put_object(
        Bucket='soc2-evidence',
        Key=f'monthly/{datetime.now().strftime("%Y-%m")}/evidence.json',
        Body=json.dumps(evidence, default=str)
    )

# Run monthly via EventBridge
```

---

## Evidence Review Checklist

Before submitting evidence to auditors:

### Completeness
- [ ] All controls have evidence
- [ ] Evidence covers entire audit period
- [ ] No gaps in time series data
- [ ] All required documents present

### Quality
- [ ] Screenshots include timestamps
- [ ] Logs are readable and relevant
- [ ] Documents are current versions
- [ ] No personal information in evidence (unless necessary)

### Organization
- [ ] Folder structure is logical
- [ ] Files are named descriptively
- [ ] Index/table of contents exists
- [ ] Cross-references are clear

### Security
- [ ] Evidence is stored securely
- [ ] Access is logged
- [ ] Sensitive data is redacted
- [ ] Retention policy is followed

---

## Common Auditor Requests

### Typical Questions and Evidence Needed

1. **"Show me how you enforce MFA"**
   - IAM credential report
   - IAM policy requiring MFA
   - Screenshots of MFA configuration

2. **"Prove all data is encrypted"**
   - Config compliance reports
   - KMS key inventory
   - S3/EBS/RDS encryption settings

3. **"Show me a restore test"**
   - Restore test documentation
   - Screenshots of restore process
   - Validation of restored data

4. **"How do you respond to security incidents?"**
   - Incident response plan
   - Actual incident logs
   - GuardDuty/Security Hub findings
   - Remediation records

5. **"Show me access reviews"**
   - Access review sign-off sheets
   - Before/after access lists
   - Terminated user list with revocation dates

---

## Next Steps

1. Set up evidence folder structure
2. Configure automated evidence collection
3. Schedule recurring evidence tasks
4. Train team on evidence collection
5. Perform quarterly evidence review
6. Prepare evidence package for auditors

[← Back to Implementation Guide](implementation-guide.md) | [View Cost Estimation →](cost-estimation.md) | [Back to Main Guide](../README.md)
