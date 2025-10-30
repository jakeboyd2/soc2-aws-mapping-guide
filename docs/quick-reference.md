# Quick Reference Guide

Fast lookup for SOC 2 controls and AWS services.

## Common Scenarios

### "I need to implement logging and monitoring"

**Services**: CloudTrail, CloudWatch, Config, VPC Flow Logs  
**Cost**: $150-400/month  
**Time**: 1-2 weeks  
**Controls**: CC7.2, CC7.3, CC4.1

**Quick Implementation**:
```bash
# Enable CloudTrail
aws cloudtrail create-trail --name org-trail --s3-bucket-name logs-bucket --is-multi-region-trail

# Enable Config
aws configservice put-configuration-recorder --configuration-recorder name=default

# Enable VPC Flow Logs
aws ec2 create-flow-logs --resource-type VPC --resource-ids vpc-xxx --traffic-type ALL
```

---

### "I need to encrypt all data"

**Services**: KMS, S3, EBS, RDS encryption  
**Cost**: $50-200/month  
**Time**: 2-4 weeks  
**Controls**: C2.1, CC6.1

**Quick Implementation**:
```bash
# Create KMS key
aws kms create-key --description "Production encryption key"

# Enable default EBS encryption
aws ec2 enable-ebs-encryption-by-default

# S3 bucket encryption
aws s3api put-bucket-encryption --bucket my-bucket --server-side-encryption-configuration '{...}'
```

---

### "I need to implement access controls"

**Services**: IAM, IAM Identity Center, MFA  
**Cost**: $0-150/month  
**Time**: 1-2 weeks  
**Controls**: CC6.1, CC6.2, CC6.3

**Quick Implementation**:
```bash
# Set password policy
aws iam update-account-password-policy --minimum-password-length 14 --require-symbols --require-numbers

# Enable MFA for root
# (Done via console)

# Create IAM role for EC2
aws iam create-role --role-name EC2-S3-Access --assume-role-policy-document file://trust-policy.json
```

---

### "I need backup and disaster recovery"

**Services**: AWS Backup, RDS automated backups, S3 versioning  
**Cost**: $100-1,000/month  
**Time**: 1-2 weeks  
**Controls**: A1.3, CC7.4

**Quick Implementation**:
```bash
# Create backup plan
aws backup create-backup-plan --backup-plan file://plan.json

# Enable RDS automated backups
aws rds modify-db-instance --db-instance-identifier mydb --backup-retention-period 35

# Enable S3 versioning
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled
```

---

### "I need threat detection"

**Services**: GuardDuty, Security Hub, AWS WAF  
**Cost**: $200-800/month  
**Time**: 1 week  
**Controls**: CC3.2, CC3.3, CC7.1

**Quick Implementation**:
```bash
# Enable GuardDuty
aws guardduty create-detector --enable

# Enable Security Hub
aws securityhub enable-security-hub --enable-default-standards

# Create WAF Web ACL
aws wafv2 create-web-acl --name my-acl --scope REGIONAL --default-action Block={}
```

---

## Control Mapping Quick Reference

### Critical Controls (Must Implement)

| Control | Description | AWS Services | Priority |
|---------|-------------|--------------|----------|
| **CC6.1** | Authentication & MFA | IAM, Cognito | 🔴 Critical |
| **CC6.6** | Network Security | VPC, Security Groups, WAF | 🔴 Critical |
| **CC7.2** | Logging | CloudTrail, CloudWatch | 🔴 Critical |
| **C2.1** | Encryption at Rest | KMS, S3, EBS, RDS | 🔴 Critical |
| **C2.2** | Encryption in Transit | ACM, ALB, CloudFront | 🔴 Critical |
| **A1.3** | Backup & Recovery | AWS Backup, RDS | 🔴 Critical |

### Important Controls (Recommended)

| Control | Description | AWS Services | Priority |
|---------|-------------|--------------|----------|
| **CC3.2** | Risk Assessment | Security Hub, GuardDuty | 🟡 High |
| **CC4.1** | Monitoring | Config, CloudWatch | 🟡 High |
| **CC7.3** | Incident Response | Systems Manager, SNS | 🟡 High |
| **CC8.1** | Change Management | Config, CloudFormation | 🟡 High |
| **PI1.1** | Input Validation | API Gateway, WAF | 🟡 High |

### Conditional Controls (If Applicable)

| Control | Description | AWS Services | When Needed |
|---------|-------------|--------------|-------------|
| **C1.1** | Data Classification | Macie | If handling sensitive data |
| **P1-P9** | Privacy | Multiple | If handling personal data |
| **PI1-PI2** | Processing Integrity | Lambda, Step Functions | If processing transactions |

---

## AWS Service Cheat Sheet

### Security Services

| Service | Primary Use | Cost | Setup Time |
|---------|------------|------|------------|
| **IAM** | Access control | Free | 1-2 days |
| **IAM Identity Center** | SSO | $0-50/month | 2-3 days |
| **KMS** | Encryption keys | $1/key/month | 1 day |
| **Secrets Manager** | Credential storage | $0.40/secret/month | 1 day |
| **AWS Organizations** | Multi-account | Free | 1-2 days |
| **CloudTrail** | Audit logging | $2/100K events | 1 day |
| **Config** | Configuration tracking | $0.003/item | 1-2 days |
| **GuardDuty** | Threat detection | ~$5/million events | 30 mins |
| **Security Hub** | Security aggregation | $0.001/finding | 30 mins |
| **WAF** | Web firewall | $5 + rules | 1-2 days |
| **Shield** | DDoS protection | Free (Standard) | Auto |
| **Inspector** | Vulnerability scanning | $0.30/assessment | 1 day |
| **Macie** | Data classification | $1/GB scanned | 1 day |

### Monitoring Services

| Service | Primary Use | Cost | Setup Time |
|---------|------------|------|------------|
| **CloudWatch** | Metrics & logs | $0.50/GB ingested | 1-2 days |
| **X-Ray** | Distributed tracing | $5/million traces | 2-3 days |
| **CloudWatch Synthetics** | Synthetic monitoring | $0.0012/run | 1 day |
| **CloudWatch RUM** | Real user monitoring | $1/100K events | 1 day |

### Availability Services

| Service | Primary Use | Cost | Setup Time |
|---------|------------|------|------------|
| **AWS Backup** | Centralized backups | $0.05/GB | 1-2 days |
| **Auto Scaling** | Dynamic capacity | Free (pay for EC2) | 1-2 days |
| **Elastic Disaster Recovery** | DR replication | Complex pricing | 1-2 weeks |

---

## Command Cheat Sheet

### Quick Status Checks

```bash
# CloudTrail status
aws cloudtrail get-trail-status --name my-trail

# Config status
aws configservice describe-configuration-recorder-status

# GuardDuty status
aws guardduty list-detectors

# Security Hub status
aws securityhub describe-hub

# IAM credential report
aws iam generate-credential-report && aws iam get-credential-report

# Config compliance
aws configservice describe-compliance-by-config-rule

# Recent CloudTrail events
aws cloudtrail lookup-events --max-results 10

# S3 bucket encryption
aws s3api get-bucket-encryption --bucket my-bucket

# EBS encryption default
aws ec2 get-ebs-encryption-by-default
```

### Quick Remediation

```bash
# Enable MFA delete on S3 bucket
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled,MFADelete=Enabled

# Update security group to remove 0.0.0.0/0
aws ec2 revoke-security-group-ingress --group-id sg-xxx --protocol tcp --port 22 --cidr 0.0.0.0/0

# Enable S3 bucket logging
aws s3api put-bucket-logging --bucket my-bucket --bucket-logging-status file://logging.json

# Rotate KMS key
aws kms enable-key-rotation --key-id xxx

# Delete unused IAM access keys
aws iam delete-access-key --user-name john --access-key-id AKIAIOSFODNN7EXAMPLE
```

---

## Configuration Templates

### Minimal CloudTrail Configuration
```json
{
  "Name": "organization-trail",
  "S3BucketName": "my-cloudtrail-logs",
  "IsMultiRegionTrail": true,
  "IsOrganizationTrail": true,
  "EnableLogFileValidation": true,
  "KmsKeyId": "arn:aws:kms:us-east-1:123456789012:key/xxx",
  "IncludeGlobalServiceEvents": true
}
```

### Minimal Config Rules
```json
[
  "cloudtrail-enabled",
  "encrypted-volumes",
  "s3-bucket-public-read-prohibited",
  "s3-bucket-public-write-prohibited",
  "rds-storage-encrypted",
  "iam-password-policy",
  "iam-user-mfa-enabled",
  "root-account-mfa-enabled"
]
```

### Minimal IAM Password Policy
```json
{
  "MinimumPasswordLength": 14,
  "RequireSymbols": true,
  "RequireNumbers": true,
  "RequireUppercaseCharacters": true,
  "RequireLowercaseCharacters": true,
  "AllowUsersToChangePassword": true,
  "ExpirePasswords": true,
  "MaxPasswordAge": 90,
  "PasswordReusePrevention": 24,
  "HardExpiry": false
}
```

### Minimal S3 Bucket Policy (HTTPS Only)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyInsecureTransport",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

---

## Compliance Checklist

### Week 1 Checklist
- [ ] Enable CloudTrail (all regions)
- [ ] Enable AWS Config
- [ ] Enable MFA for all users
- [ ] Enable MFA for root account
- [ ] Delete root access keys
- [ ] Set IAM password policy
- [ ] Enable CloudWatch logging

### Month 1 Checklist
- [ ] Enable GuardDuty
- [ ] Enable Security Hub
- [ ] Configure AWS Backup
- [ ] Enable encryption at rest (S3, EBS, RDS)
- [ ] Configure VPC security groups
- [ ] Enable VPC Flow Logs
- [ ] Deploy AWS WAF
- [ ] Document initial policies

### Quarter 1 Checklist
- [ ] Complete all technical implementations
- [ ] Document all procedures
- [ ] Perform first access review
- [ ] Test backup restoration
- [ ] Conduct incident response drill
- [ ] Begin evidence collection
- [ ] Schedule auditor engagement

---

## Common Commands by Control

### CC6.1 - Authentication
```bash
# List users without MFA
aws iam get-credential-report | grep -v "mfa_active=true"

# Enable MFA for user (via console or script)
# Force MFA via IAM policy condition
```

### CC7.2 - Logging
```bash
# Enable CloudTrail
aws cloudtrail create-trail --name my-trail --s3-bucket-name logs

# Enable Config
aws configservice put-configuration-recorder --configuration-recorder name=default

# Enable VPC Flow Logs
aws ec2 create-flow-logs --resource-type VPC --resource-ids vpc-xxx
```

### C2.1 - Encryption at Rest
```bash
# Enable EBS encryption
aws ec2 enable-ebs-encryption-by-default

# Enable S3 encryption
aws s3api put-bucket-encryption --bucket my-bucket

# Check RDS encryption
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,StorageEncrypted]'
```

### A1.3 - Backups
```bash
# Create backup plan
aws backup create-backup-plan --backup-plan file://plan.json

# List backup jobs
aws backup list-backup-jobs

# Perform restore test
aws backup start-restore-job --recovery-point-arn arn --metadata file://metadata.json
```

---

## Troubleshooting Quick Fixes

### Issue: CloudTrail Not Logging
```bash
# Check trail status
aws cloudtrail get-trail-status --name my-trail

# Verify S3 bucket policy allows CloudTrail
# Check CloudWatch Logs role

# Restart logging
aws cloudtrail stop-logging --name my-trail
aws cloudtrail start-logging --name my-trail
```

### Issue: Config Rules Failing
```bash
# Describe compliance
aws configservice describe-compliance-by-config-rule --config-rule-names my-rule

# Get evaluation results
aws configservice get-compliance-details-by-config-rule --config-rule-name my-rule

# Re-evaluate
aws configservice start-config-rules-evaluation --config-rule-names my-rule
```

### Issue: GuardDuty Not Sending Alerts
```bash
# Check detector
aws guardduty get-detector --detector-id xxx

# Check findings
aws guardduty list-findings --detector-id xxx

# Verify EventBridge rule
aws events describe-rule --name guardduty-alert

# Check SNS topic subscription
aws sns list-subscriptions-by-topic --topic-arn arn:aws:sns:...
```

---

## Resource Links

### AWS Documentation
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)
- [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)
- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)

### SOC 2 Resources
- [AICPA TSP Section 100](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/trustservicesprinciples.html)
- [SOC 2 Sample Report](https://www.aicpa.org/resources/download/soc-2-sample-report)

### Tools
- [AWS CLI](https://aws.amazon.com/cli/)
- [AWS Well-Architected Tool](https://aws.amazon.com/well-architected-tool/)
- [AWS Security Hub](https://aws.amazon.com/security-hub/)
- [AWS Pricing Calculator](https://calculator.aws/)

---

## Keyboard Shortcuts

### AWS Console
- `Alt + S`: Open Services menu
- `Alt + /`: Search services
- `/`: Focus search box

### CloudFormation
- `Ctrl/Cmd + K`: Keyboard shortcuts
- `Ctrl/Cmd + F`: Find in template

### CLI
- `aws help`: Get help
- `aws <service> help`: Service help
- `aws <service> <command> help`: Command help

---

## Next Steps

1. Bookmark this page for quick reference
2. Save command snippets to your notes
3. Customize templates for your environment
4. Review weekly during implementation
5. Update as you learn more

[← Back to Cost Estimation](cost-estimation.md) | [View Glossary →](glossary.md) | [Back to Main Guide](../README.md)
