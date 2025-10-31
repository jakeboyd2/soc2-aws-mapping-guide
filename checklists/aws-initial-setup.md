# AWS Account Initial Security Setup Checklist

## Purpose
This checklist ensures new AWS accounts meet baseline SOC 2 security requirements before deploying workloads. Use this during account provisioning or when inheriting an existing AWS environment.

## Pre-Deployment Security Baseline

### Root Account Hardening
- [ ] Enable MFA on root account (hardware token preferred)
- [ ] Create root account password in password manager
- [ ] Remove all root account access keys (if any exist)
- [ ] Set root account email to security team distribution list
- [ ] Document root account access procedure (break-glass process)
- [ ] Test root account login quarterly (calendar reminder)

**SOC 2 Mapping:** CC6.1 (Root access controls), CC6.2 (Credential management)

**Evidence to Collect:**
- Screenshot of root MFA configuration
- IAM credential report showing no root access keys
- Root account access procedure document

---

### IAM Foundation (CC6.1, CC6.2, CC6.3)
- [ ] Enable IAM password policy with requirements:
  - Minimum 14 characters
  - Require uppercase, lowercase, numbers, symbols
  - Password expiration: 90 days
  - Prevent password reuse (last 24 passwords)
  - Enable password expiration warnings
  
- [ ] Create IAM administrative role (not users for admins)
- [ ] Enable IAM Access Analyzer in all regions
- [ ] Create IAM groups for role-based access:
  - `Administrators` (full access, requires MFA)
  - `Developers` (limited to dev resources)
  - `ReadOnly` (view-only access for auditors)
  - `SecurityAuditors` (security service read access)

- [ ] Implement mandatory MFA for console access
- [ ] Disable unused regions (reduces attack surface)
- [ ] Enable IAM Identity Center (SSO) if multi-account

**Evidence to Collect:**
- IAM password policy screenshot
- IAM group membership list
- Access Analyzer findings (should be empty)

---

### Logging and Monitoring Foundation (CC7.2, CC7.3)

#### CloudTrail (Audit Logging)
- [ ] Enable CloudTrail in all regions
- [ ] Create dedicated S3 bucket for CloudTrail logs
- [ ] Enable log file validation (detect tampering)
- [ ] Enable S3 bucket versioning for log bucket
- [ ] Configure bucket lifecycle policy (retain 1 year minimum)
- [ ] Enable CloudTrail log encryption (KMS)
- [ ] Block public access on CloudTrail S3 bucket
- [ ] Enable MFA Delete on CloudTrail S3 bucket
- [ ] Create CloudWatch Logs integration for real-time monitoring
- [ ] Create SNS topic for CloudTrail log delivery failures

**SOC 2 Mapping:** CC7.2 (Activity monitoring), CC8.1 (Change tracking)

**Evidence to Collect:**
- CloudTrail configuration screenshot
- S3 bucket policy document
- Sample CloudTrail log entries
- Log retention configuration

#### AWS Config (Configuration Tracking)
- [ ] Enable AWS Config in all regions
- [ ] Create dedicated S3 bucket for Config snapshots
- [ ] Enable Config recording for all resource types
- [ ] Deploy Config Managed Rules for SOC 2 controls:
  - `iam-password-policy`
  - `root-account-mfa-enabled`
  - `iam-user-mfa-enabled`
  - `access-keys-rotated`
  - `s3-bucket-public-read-prohibited`
  - `s3-bucket-public-write-prohibited`
  - `encrypted-volumes`
  - `rds-encryption-enabled`
  - `cloudtrail-enabled`

- [ ] Configure SNS notifications for compliance changes

**Evidence to Collect:**
- Config recorder status
- List of enabled Config rules
- Config compliance dashboard screenshot

#### Security Hub (Centralized Security Monitoring)
- [ ] Enable Security Hub in primary region
- [ ] Enable AWS Foundational Security Best Practices standard
- [ ] Enable CIS AWS Foundations Benchmark
- [ ] Integrate GuardDuty findings
- [ ] Integrate Config findings
- [ ] Create custom insights for SOC 2 control families
- [ ] Configure automated remediation for critical findings
- [ ] Set up SNS notifications for CRITICAL/HIGH findings

**Evidence to Collect:**
- Security Hub standards enabled screenshot
- Security score trends
- Sample finding details

---

### Data Protection (CC6.7, C1.1)

#### Encryption at Rest
- [ ] Enable default EBS encryption in all regions
- [ ] Create KMS Customer Managed Keys (CMKs) for:
  - EBS volumes
  - RDS databases
  - S3 buckets (sensitive data)
  - Secrets Manager secrets
  
- [ ] Document KMS key policies
- [ ] Enable automatic key rotation (annual)
- [ ] Restrict KMS key access to specific roles/services

**Evidence to Collect:**
- KMS key policies
- Default encryption settings per region
- Key rotation configuration

#### S3 Bucket Security Baseline
- [ ] Block all public access at account level
- [ ] Enable S3 Block Public Access for organization
- [ ] Require encryption for all S3 buckets (bucket policy)
- [ ] Enable S3 versioning for critical data buckets
- [ ] Enable S3 Object Lock for immutable data (if required)
- [ ] Configure S3 lifecycle policies for data retention
- [ ] Enable S3 access logging for audit trail

**Evidence to Collect:**
- Account-level Block Public Access settings
- Sample bucket policies
- S3 inventory report

---

### Network Security (CC6.6, CC6.7)

#### VPC Foundation
- [ ] Create VPC with private subnets for workloads
- [ ] Create public subnets only for load balancers/NAT
- [ ] Enable VPC Flow Logs (all traffic, to CloudWatch)
- [ ] Configure Network ACLs (default deny, explicit allow)
- [ ] Review default security group (remove all rules)
- [ ] Create security groups following least privilege
- [ ] Enable GuardDuty for network threat detection
- [ ] Document network architecture diagram

**Evidence to Collect:**
- VPC architecture diagram
- Security group rules export
- NACL configurations
- Flow Logs configuration

---

### Backup and Recovery (A1.2, A1.3)

- [ ] Enable AWS Backup in all regions
- [ ] Create backup plans for:
  - RDS databases (daily, 35-day retention)
  - EBS volumes (tagged as production)
  - EFS file systems
  - DynamoDB tables
  
- [ ] Tag resources for automatic backup (`Backup=True`)
- [ ] Test restore procedures quarterly
- [ ] Document RTO/RPO requirements
- [ ] Enable backup vault lock (prevent deletion)
- [ ] Configure cross-region backup copies

**Evidence to Collect:**
- Backup plan configurations
- Restore test results
- RTO/RPO documentation

---

### Incident Response Preparation (CC7.3, CC7.4)

- [ ] Create SNS topics for security alerts
- [ ] Configure EventBridge rules for critical events:
  - Root account usage
  - IAM policy changes
  - Security group modifications
  - GuardDuty high-severity findings
  
- [ ] Document incident response runbooks
- [ ] Create Lambda functions for automated response
- [ ] Establish communication channels (Slack/Teams)
- [ ] Define escalation procedures

**Evidence to Collect:**
- EventBridge rules documentation
- Incident response playbooks
- Contact lists

---

## Post-Setup Validation

### Automated Checks
```bash
# Run AWS Config compliance check
aws configservice describe-compliance-by-config-rule

# Generate IAM credential report
aws iam generate-credential-report
aws iam get-credential-report

# Check Security Hub compliance score
aws securityhub get-findings --filters '{"ComplianceStatus":[{"Value":"FAILED","Comparison":"EQUALS"}]}'
```

### Manual Validation Checklist
- [ ] Review CloudTrail logs (last 24 hours)
- [ ] Review Security Hub dashboard
- [ ] Review Config compliance dashboard
- [ ] Test MFA enforcement (try to login without MFA)
- [ ] Test IAM password policy (try weak password)
- [ ] Review GuardDuty findings
- [ ] Verify backup completion

---

## Maintenance Schedule

### Daily
- [ ] Review Security Hub critical/high findings
- [ ] Monitor CloudWatch alarms

### Weekly
- [ ] Review IAM Access Analyzer findings
- [ ] Review new AWS Config non-compliance items
- [ ] Review GuardDuty findings

### Monthly
- [ ] IAM access review (remove unused credentials)
- [ ] Security group review (remove overly permissive rules)
- [ ] Review KMS key usage
- [ ] Test backup restore procedures

### Quarterly
- [ ] Full security baseline review
- [ ] Update documentation
- [ ] Disaster recovery tabletop exercise
- [ ] Access key rotation verification

---

## Common Audit Questions Addressed

**"How do you ensure only authorized users access AWS resources?"**
→ Point to IAM policies, MFA enforcement, IAM Access Analyzer results

**"How do you track changes to security controls?"**
→ Point to CloudTrail, Config timeline, EventBridge alerts

**"How do you protect data at rest?"**
→ Point to KMS encryption, default encryption settings, S3 bucket policies

**"How do you detect security incidents?"**
→ Point to GuardDuty, Security Hub, CloudWatch alarms, EventBridge rules

**"How do you ensure business continuity?"**
→ Point to AWS Backup configurations, tested restore procedures, RTO/RPO documentation
