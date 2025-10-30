# SOC 2 Implementation Guide

A step-by-step guide to implementing SOC 2 controls using AWS services.

## Overview

This guide provides a practical roadmap for implementing SOC 2 controls on AWS, from initial assessment through audit readiness.

**Timeline**: 20-24 weeks for initial implementation  
**Minimum Budget**: $500-1,000/month for small organizations  
**Team Size**: 2-5 people depending on organization size

---

## Phase 0: Pre-Implementation (Weeks -2 to 0)

### Week -2 to -1: Planning and Assessment

#### 1. Determine Scope
- **Which Trust Service Categories?**
  - Common Criteria (CC): Required for all
  - Availability (A): If you commit to uptime SLAs
  - Confidentiality (C): If you handle confidential data
  - Processing Integrity (PI): If processing accuracy is critical
  - Privacy (P): If you handle personal information

- **Define System Boundaries**
  - Which AWS accounts?
  - Which applications/services?
  - Third-party integrations?
  - On-premises components?

#### 2. Gap Analysis
```
Assessment Checklist:
□ Current AWS security posture
□ Existing policies and procedures
□ Current logging and monitoring
□ Access control maturity
□ Backup and recovery capabilities
□ Incident response procedures
□ Change management process
□ Vendor management process
```

#### 3. Resource Planning
- **Team Roles**:
  - Security Engineer (AWS implementation)
  - Compliance Manager (documentation, evidence)
  - DevOps Engineer (automation, CI/CD)
  - Project Manager (coordination)
  - External Consultant (optional, for guidance)

- **Budget Allocation**:
  - AWS services: 60-70%
  - Tools and software: 10-15%
  - External audit: 15-20%
  - Training and certifications: 5-10%

#### 4. Select Auditor
- Research SOC 2 auditors
- Get quotes (typically $15,000-$50,000+)
- Understand their requirements
- Plan audit timeline (typically 6-12 months of evidence)

### Week -1 to 0: Documentation Baseline

#### Create Initial Documents
1. **Information Security Policy** (template-based)
2. **Acceptable Use Policy**
3. **Access Control Policy**
4. **Data Classification Policy**
5. **Incident Response Plan**
6. **Business Continuity Plan**
7. **Vendor Management Policy**
8. **Change Management Policy**

**Tip**: Start with templates, customize for your organization.

---

## Phase 1: Foundation (Weeks 1-4)

### Week 1: Logging and Audit Trail

#### Day 1-2: Enable AWS CloudTrail
```bash
# Using AWS CLI (or Console)
aws cloudtrail create-trail \
  --name organization-trail \
  --s3-bucket-name compliance-logs-bucket \
  --is-multi-region-trail \
  --is-organization-trail

aws cloudtrail start-logging --name organization-trail
```

**Key Configurations**:
- Enable in all regions
- Enable log file validation
- Configure S3 lifecycle (1-7 years retention)
- Enable SSE-KMS encryption
- Set up CloudWatch Logs integration

**Controls Addressed**: CC7.2, CC7.3, CC6.2

#### Day 3-4: Configure AWS Config
```bash
# Enable AWS Config
aws configservice put-configuration-recorder \
  --configuration-recorder name=default,roleARN=arn:aws:iam::123456789012:role/config-role

aws configservice put-delivery-channel \
  --delivery-channel name=default,s3BucketName=config-bucket
```

**Managed Rules to Enable**:
- `cloudtrail-enabled`
- `encrypted-volumes`
- `s3-bucket-public-read-prohibited`
- `s3-bucket-public-write-prohibited`
- `rds-storage-encrypted`
- `iam-password-policy`
- `iam-user-mfa-enabled`
- `root-account-mfa-enabled`

**Controls Addressed**: CC4.1, CC7.2, CC8.1

#### Day 5: CloudWatch Alarms and Dashboards
```bash
# Create alarm for root account usage
aws cloudwatch put-metric-alarm \
  --alarm-name root-account-usage \
  --alarm-description "Alert on root account usage" \
  --metric-name RootAccountUsage \
  --namespace CloudTrailMetrics \
  --statistic Sum \
  --period 300 \
  --threshold 1 \
  --comparison-operator GreaterThanOrEqualToThreshold
```

**Critical Alarms**:
- Root account usage
- IAM policy changes
- Network ACL changes
- Security group changes
- S3 bucket policy changes
- CloudTrail logging disabled
- Config changes

**Controls Addressed**: CC7.2, CC4.1

### Week 2: Identity and Access Management

#### Day 1-2: IAM Hardening
```bash
# Set password policy
aws iam update-account-password-policy \
  --minimum-password-length 14 \
  --require-symbols \
  --require-numbers \
  --require-uppercase-characters \
  --require-lowercase-characters \
  --allow-users-to-change-password \
  --max-password-age 90 \
  --password-reuse-prevention 24
```

**IAM Best Practices**:
- ✅ Enable MFA for all users
- ✅ Delete root access keys
- ✅ Enable MFA for root account
- ✅ Use IAM roles instead of long-term credentials
- ✅ Implement least privilege access
- ✅ Create custom IAM policies (avoid AdministratorAccess)
- ✅ Use groups for permission management
- ✅ Enable IAM Access Analyzer

**Controls Addressed**: CC6.1, CC6.2, CC6.3

#### Day 3-4: AWS Organizations (Multi-Account)
```
Root Organization
├── Security OU
│   └── Security Tooling Account
├── Production OU
│   └── Production Account
├── Development OU
│   └── Dev Account
└── Shared Services OU
    └── Shared Services Account
```

**Service Control Policies (SCPs)**:
- Deny leaving organization
- Deny disabling CloudTrail
- Deny disabling AWS Config
- Require encryption
- Restrict regions
- Deny root account usage

**Controls Addressed**: CC1.1, CC1.3

#### Day 5: IAM Identity Center (SSO)
- Set up IAM Identity Center
- Integrate with identity provider (Okta, Azure AD, etc.)
- Create permission sets
- Assign users to accounts
- Enforce MFA

**Controls Addressed**: CC6.1, CC6.2

### Week 3: Data Protection

#### Day 1-2: Encryption at Rest
```bash
# Enable default EBS encryption
aws ec2 enable-ebs-encryption-by-default --region us-east-1

# Create KMS key for different data classifications
aws kms create-key \
  --description "Production data encryption key" \
  --key-policy file://key-policy.json
```

**Encryption Checklist**:
- ✅ Enable default EBS encryption
- ✅ Enable S3 default encryption (all buckets)
- ✅ Encrypt RDS databases (requires recreation if not initially encrypted)
- ✅ Enable DynamoDB encryption
- ✅ Use AWS KMS customer-managed keys for sensitive data
- ✅ Enable key rotation (annual)

**Controls Addressed**: C2.1, CC6.1

#### Day 3-4: Encryption in Transit
```bash
# Create ACM certificate
aws acm request-certificate \
  --domain-name example.com \
  --validation-method DNS \
  --subject-alternative-names *.example.com
```

**TLS/SSL Checklist**:
- ✅ Request ACM certificates
- ✅ Configure HTTPS-only on CloudFront
- ✅ Enable HTTPS-only on ALB/NLB
- ✅ Enforce SSL connections for RDS
- ✅ Use S3 bucket policies to deny non-HTTPS access
- ✅ Set minimum TLS version to 1.2

**Controls Addressed**: C2.2, CC6.7

#### Day 5: Secrets Management
```bash
# Create secret
aws secretsmanager create-secret \
  --name production/database/password \
  --secret-string file://secret.json \
  --kms-key-id arn:aws:kms:us-east-1:123456789012:key/xxx
```

**Secrets Management**:
- ✅ Store all credentials in Secrets Manager or Parameter Store
- ✅ Remove hardcoded credentials from code
- ✅ Enable automatic rotation
- ✅ Use IAM policies for access control
- ✅ Audit secret access via CloudTrail

**Controls Addressed**: CC6.1, CC6.4

### Week 4: Network Security

#### Day 1-2: VPC Architecture
```
Production VPC (10.0.0.0/16)
├── Public Subnets (10.0.1.0/24, 10.0.2.0/24)
│   ├── ALB
│   └── NAT Gateway
├── Private Subnets (10.0.10.0/24, 10.0.11.0/24)
│   └── Application Servers
└── Data Subnets (10.0.20.0/24, 10.0.21.0/24)
    └── RDS (Multi-AZ)
```

**VPC Security**:
- ✅ Design multi-tier architecture
- ✅ Use private subnets for application/data layers
- ✅ Configure security groups (least privilege)
- ✅ Implement Network ACLs
- ✅ Enable VPC Flow Logs
- ✅ Use VPC endpoints for AWS services

**Controls Addressed**: CC6.6, C3.1

#### Day 3-4: AWS WAF
```bash
# Create Web ACL
aws wafv2 create-web-acl \
  --name production-web-acl \
  --scope REGIONAL \
  --default-action Block={} \
  --rules file://waf-rules.json
```

**WAF Rules**:
- ✅ AWS Managed Rules (Core Rule Set)
- ✅ Rate limiting (e.g., 2000 requests per 5 minutes)
- ✅ Geographic restrictions (if needed)
- ✅ IP reputation lists
- ✅ Known bad inputs

**Controls Addressed**: CC6.6, CC5.2

#### Day 5: Shield and DDoS Protection
- Enable AWS Shield Standard (automatic)
- Consider Shield Advanced for critical applications
- Configure health checks
- Set up DDoS response procedures

**Controls Addressed**: CC5.2, CC6.6

---

## Phase 2: Availability and Backup (Weeks 5-8)

### Week 5: High Availability

#### Multi-AZ Architecture
```bash
# Create RDS Multi-AZ
aws rds create-db-instance \
  --db-instance-identifier prod-db \
  --db-instance-class db.t3.medium \
  --engine postgres \
  --multi-az \
  --allocated-storage 100 \
  --storage-encrypted
```

**HA Checklist**:
- ✅ RDS Multi-AZ
- ✅ Auto Scaling groups across multiple AZs
- ✅ Application Load Balancer (multi-AZ)
- ✅ Route 53 health checks
- ✅ ElastiCache with replication
- ✅ S3 (99.999999999% durability)

**Controls Addressed**: A1.1, A1.2

### Week 6: Backup Strategy

#### AWS Backup Configuration
```bash
# Create backup plan
aws backup create-backup-plan \
  --backup-plan file://backup-plan.json
```

**Backup Plan Example**:
```json
{
  "BackupPlan": {
    "BackupPlanName": "ProductionDailyBackup",
    "Rules": [
      {
        "RuleName": "DailyBackups",
        "TargetBackupVaultName": "Default",
        "ScheduleExpression": "cron(0 5 ? * * *)",
        "StartWindowMinutes": 60,
        "CompletionWindowMinutes": 120,
        "Lifecycle": {
          "DeleteAfterDays": 35
        }
      }
    ]
  }
}
```

**Backup Checklist**:
- ✅ Define RPO/RTO for all critical systems
- ✅ Configure AWS Backup policies
- ✅ Enable RDS automated backups (7-35 days)
- ✅ Configure EBS snapshot policies (DLM)
- ✅ Enable S3 versioning
- ✅ Set up cross-region backup replication
- ✅ Use Backup Vault Lock for immutability

**Controls Addressed**: A1.3, CC7.4

### Week 7-8: Monitoring and Alerting

#### CloudWatch Configuration
```bash
# Create custom dashboard
aws cloudwatch put-dashboard \
  --dashboard-name Production-Dashboard \
  --dashboard-body file://dashboard.json
```

**Monitoring Setup**:
- ✅ CloudWatch dashboards for each service
- ✅ Application-level custom metrics
- ✅ Log aggregation to CloudWatch Logs
- ✅ CloudWatch Synthetics canaries
- ✅ X-Ray for distributed tracing
- ✅ SNS topics for critical alerts

**Controls Addressed**: CC7.2, A2.1

---

## Phase 3: Threat Detection (Weeks 9-12)

### Week 9: Amazon GuardDuty
```bash
# Enable GuardDuty
aws guardduty create-detector \
  --enable \
  --finding-publishing-frequency FIFTEEN_MINUTES
```

**GuardDuty Configuration**:
- ✅ Enable in all regions
- ✅ Configure SNS notifications for high/critical findings
- ✅ Set up automated response (Lambda)
- ✅ Review findings weekly
- ✅ Document remediation procedures

**Controls Addressed**: CC3.2, CC3.3, CC7.1

### Week 10: AWS Security Hub
```bash
# Enable Security Hub
aws securityhub enable-security-hub \
  --enable-default-standards
```

**Security Hub Standards**:
- ✅ AWS Foundational Security Best Practices
- ✅ CIS AWS Foundations Benchmark
- ✅ PCI-DSS (if applicable)
- ✅ Custom insights for SOC 2

**Controls Addressed**: CC3.2, CC4.1, CC9.1

### Week 11: AWS Inspector
```bash
# Create assessment target
aws inspector create-assessment-target \
  --assessment-target-name production-servers \
  --resource-group-arn arn:aws:inspector:us-east-1:123456789012:resourcegroup/xxx
```

**Inspector Scanning**:
- ✅ Enable Inspector for EC2/ECR
- ✅ Schedule monthly scans
- ✅ Review and remediate findings
- ✅ Track vulnerability remediation

**Controls Addressed**: CC3.2

### Week 12: Incident Response

#### Create Incident Response Plan
1. **Detection**: GuardDuty, Security Hub, CloudWatch
2. **Analysis**: AWS Detective, CloudTrail, VPC Flow Logs
3. **Containment**: Security group changes, IAM policy changes
4. **Eradication**: Remove malicious resources
5. **Recovery**: Restore from backups
6. **Post-Incident**: Document lessons learned

#### Set Up AWS Systems Manager Incident Manager
```bash
# Create response plan
aws ssm-incidents create-response-plan \
  --name security-incident-response \
  --display-name "Security Incident Response" \
  --incident-template file://template.json
```

**Controls Addressed**: CC7.1, CC7.3

---

## Phase 4: Processing Integrity and Privacy (Weeks 13-16)

### Week 13: Input Validation

#### API Gateway Validation
```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "UserInput",
  "type": "object",
  "properties": {
    "email": {"type": "string", "format": "email"},
    "age": {"type": "integer", "minimum": 18}
  },
  "required": ["email"]
}
```

**Validation Checklist**:
- ✅ API Gateway request validators
- ✅ Lambda input validation functions
- ✅ WAF rules for malicious inputs
- ✅ Error handling and logging

**Controls Addressed**: PI1.1

### Week 14: Workflow Orchestration

#### Step Functions
```json
{
  "StartAt": "ValidateInput",
  "States": {
    "ValidateInput": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:xxx:function:validate",
      "Next": "ProcessData",
      "Catch": [{
        "ErrorEquals": ["ValidationError"],
        "Next": "HandleError"
      }]
    },
    "ProcessData": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:xxx:function:process",
      "Next": "Success"
    },
    "Success": {"Type": "Succeed"},
    "HandleError": {"Type": "Fail"}
  }
}
```

**Controls Addressed**: PI1.2, PI1.4

### Week 15-16: Privacy Implementation

#### Data Subject Rights
```python
# Lambda function for data export
def export_user_data(user_id):
    # Collect data from all sources
    data = {
        'profile': get_user_profile(user_id),
        'activity': get_user_activity(user_id),
        'files': list_user_files(user_id)
    }
    
    # Upload to S3 with pre-signed URL
    export_key = f'exports/{user_id}-{timestamp}.json'
    s3.put_object(Bucket='exports', Key=export_key, Body=json.dumps(data))
    
    # Generate download link (expires in 7 days)
    url = generate_presigned_url(export_key, expires_in=604800)
    
    # Send email with link
    send_email(user_email, url)
```

**Privacy Checklist**:
- ✅ Create privacy policy
- ✅ Implement consent collection
- ✅ Build data export API
- ✅ Implement deletion workflow
- ✅ Set up data retention automation
- ✅ Enable Amazon Macie for data classification

**Controls Addressed**: P1, P2, P3, P4, P5

---

## Phase 5: Documentation and Testing (Weeks 17-20)

### Week 17-18: Complete Documentation

#### Required Documents
1. **System Description**
   - Architecture diagrams
   - Data flow diagrams
   - Network diagrams
   - Technology stack

2. **Policies and Procedures**
   - Information Security Policy
   - Access Control Policy
   - Change Management Policy
   - Incident Response Plan
   - Business Continuity Plan
   - Vendor Management Policy
   - Risk Assessment Policy
   - Data Retention Policy
   - Privacy Policy

3. **Control Documentation**
   - Control matrix (controls to services)
   - Implementation evidence
   - Testing procedures
   - Responsibilities (RACI)

4. **Risk Assessment**
   - Risk register
   - Risk treatment plan
   - Residual risks

### Week 19: Testing

#### Control Testing Checklist
- ✅ Access control testing (try unauthorized access)
- ✅ MFA enforcement testing
- ✅ Backup restoration testing
- ✅ Incident response drill
- ✅ DR failover testing
- ✅ Vulnerability scanning
- ✅ Penetration testing (optional but recommended)
- ✅ CloudWatch alarm testing

### Week 20: Pre-Audit Preparation

#### Evidence Collection
- ✅ CloudTrail logs (6-12 months)
- ✅ Config compliance reports
- ✅ Security Hub findings and remediation
- ✅ GuardDuty findings and response
- ✅ Backup success logs
- ✅ Restore test results
- ✅ Change logs
- ✅ Access review logs
- ✅ Incident logs
- ✅ Training records

---

## Phase 6: Audit Period (6-12 Months)

### Continuous Evidence Collection

#### Monthly Tasks
- ✅ Access reviews (quarterly)
- ✅ Risk assessment updates (quarterly)
- ✅ Backup testing (quarterly)
- ✅ Vulnerability scans (monthly)
- ✅ Policy reviews (annual)
- ✅ Security awareness training
- ✅ Vendor reviews (annual)

#### Evidence Organization
```
Evidence/
├── 2024-Q1/
│   ├── access-reviews/
│   ├── backup-tests/
│   ├── config-reports/
│   ├── guardduty-findings/
│   ├── incident-reports/
│   └── change-logs/
├── 2024-Q2/
└── ...
```

### Audit Timeline

**Month 6-12**: Evidence period begins  
**Month 11**: Pre-audit readiness assessment  
**Month 12**: Audit kicks off  
- Week 1-2: Planning and scoping
- Week 3-4: Testing and evidence review
- Week 5-6: Management interviews
- Week 7-8: Draft report and review
- Week 9-10: Final report issued

---

## Common Implementation Challenges

### Challenge 1: Resource Constraints
**Solution**: Prioritize Common Criteria controls, use automation, consider managed services

### Challenge 2: Complex Architecture
**Solution**: Start with critical systems, document clearly, simplify where possible

### Challenge 3: Cost Overruns
**Solution**: Set budgets, use AWS Budgets, optimize regularly, right-size resources

### Challenge 4: Evidence Collection
**Solution**: Automate evidence collection, use AWS Audit Manager, maintain organized structure

### Challenge 5: Tribal Knowledge
**Solution**: Document everything, maintain runbooks, cross-train team members

---

## Success Metrics

### Technical Metrics
- ✅ 100% MFA adoption
- ✅ 0 critical GuardDuty findings open >30 days
- ✅ 95%+ AWS Config compliance
- ✅ 100% encryption at rest for production data
- ✅ <4 hour RTO, <1 hour RPO
- ✅ 99.9%+ availability SLA

### Process Metrics
- ✅ Quarterly access reviews completed on time
- ✅ 100% incident response SLA met
- ✅ 100% change approval compliance
- ✅ 0 audit findings from internal audits
- ✅ 100% training completion

---

## Next Steps

1. Complete Phase 0 planning
2. Get executive buy-in and budget approval
3. Assemble implementation team
4. Start with Phase 1 (Foundation)
5. Track progress weekly
6. Adjust timeline as needed
7. Engage auditor early for guidance

[← Back to Main Guide](../README.md) | [View Evidence Collection Guide →](evidence-collection.md)
