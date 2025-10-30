# AWS Service Mapping Reference

This document provides a comprehensive mapping of AWS services to SOC 2 Trust Service Criteria controls.

## Quick Service Lookup

| AWS Service | Primary Controls | Trust Service Categories |
|-------------|------------------|--------------------------|
| **AWS IAM** | CC6.1, CC6.2, CC6.3, CC6.5 | Common Criteria |
| **AWS Organizations** | CC1.1, CC1.3 | Common Criteria |
| **AWS CloudTrail** | CC7.2, CC7.3, CC6.2, CC3.3 | Common Criteria |
| **AWS Config** | CC4.1, CC7.2, CC8.1, CC3.4 | Common Criteria |
| **AWS CloudWatch** | CC7.2, CC4.1, A1.1, A2.1 | Common Criteria, Availability |
| **Amazon GuardDuty** | CC3.2, CC3.3, CC7.1, C3.1 | Common Criteria, Confidentiality |
| **AWS Security Hub** | CC3.2, CC4.1, CC9.1 | Common Criteria |
| **AWS KMS** | C2.1, C2.2, CC6.1 | Confidentiality |
| **Amazon VPC** | CC6.6, C3.1 | Common Criteria, Confidentiality |
| **AWS WAF** | CC6.6, CC5.2, PI1.1 | Common Criteria, Processing Integrity |
| **AWS Shield** | CC5.2, CC6.6 | Common Criteria |
| **AWS Secrets Manager** | CC6.1, CC6.4 | Common Criteria |
| **AWS Systems Manager** | CC8.1, CC3.4, CC5.2 | Common Criteria |
| **AWS Backup** | A1.3, CC7.4 | Availability |
| **Amazon S3** | C2.1, P4.2, A1.3 | Confidentiality, Privacy, Availability |
| **Amazon RDS** | C2.1, A1.3 | Confidentiality, Availability |
| **Amazon DynamoDB** | C2.1, PI1.2 | Confidentiality, Processing Integrity |
| **AWS Certificate Manager** | C2.2, CC6.7 | Confidentiality |
| **Amazon CloudFront** | C2.2, CC6.7 | Confidentiality |
| **Elastic Load Balancing** | A1.1, C2.2 | Availability, Confidentiality |
| **Amazon Route 53** | A1.1, A2.1 | Availability |
| **AWS Auto Scaling** | A1.1 | Availability |
| **Amazon API Gateway** | PI1.1, PI1.3, P5.1 | Processing Integrity, Privacy |
| **AWS Lambda** | PI1.1, PI1.2, PI1.5 | Processing Integrity |
| **AWS Step Functions** | PI1.2, PI1.4, P5.2 | Processing Integrity, Privacy |
| **Amazon SQS** | PI1.2, PI1.4 | Processing Integrity |
| **Amazon SNS** | CC2.1, PI1.5 | Common Criteria, Processing Integrity |
| **AWS X-Ray** | PI1.2, PI1.5, A2.1 | Processing Integrity, Availability |
| **Amazon Macie** | C1.1, P3.1, P9.1 | Confidentiality, Privacy |
| **AWS Inspector** | CC3.2 | Common Criteria |
| **AWS Detective** | CC3.3, CC7.1 | Common Criteria |
| **Amazon Cognito** | PI1.3, P1.1, P2.1 | Processing Integrity, Privacy |
| **AWS Audit Manager** | CC4.1 | Common Criteria |
| **AWS Artifact** | CC2.2, CC9.2 | Common Criteria |
| **AWS Network Firewall** | CC6.6, C3.1 | Common Criteria, Confidentiality |
| **VPC Flow Logs** | CC7.2, C3.1 | Common Criteria, Confidentiality |
| **Amazon EventBridge** | CC2.1, PI1.4, P4.2 | Common Criteria, Processing Integrity, Privacy |
| **AWS CodePipeline** | CC8.1 | Common Criteria |
| **AWS CloudFormation** | CC8.1, CC3.4 | Common Criteria |
| **AWS Glue** | PI2.1, P8.1 | Processing Integrity, Privacy |
| **Amazon Athena** | PI2.1 | Processing Integrity |

---

## Services by Trust Service Category

### Common Criteria (CC)

#### Control Environment (CC1)
- **AWS Organizations**: Policy enforcement, account structure
- **AWS IAM**: Role-based access control
- **AWS CloudTrail**: Activity monitoring
- **AWS SSO/IAM Identity Center**: Centralized access management

#### Communication and Information (CC2)
- **Amazon SNS**: Notifications and alerts
- **Amazon EventBridge**: Event routing
- **Amazon SES**: Email communication
- **AWS Chatbot**: Team communication integration
- **AWS Artifact**: Compliance report distribution

#### Risk Assessment (CC3)
- **AWS Security Hub**: Finding aggregation and risk analysis
- **Amazon GuardDuty**: Threat detection
- **AWS Inspector**: Vulnerability assessment
- **AWS Trusted Advisor**: Best practice checks
- **AWS Well-Architected Tool**: Architecture assessment
- **AWS Detective**: Security investigation

#### Monitoring Activities (CC4)
- **AWS Config**: Continuous compliance monitoring
- **AWS CloudWatch**: Operational monitoring
- **AWS Audit Manager**: Audit automation
- **AWS Security Hub**: Security posture monitoring

#### Control Activities (CC5)
- **AWS Config Rules**: Automated controls
- **AWS Lambda**: Custom control automation
- **AWS WAF**: Application protection
- **AWS Shield**: DDoS protection
- **AWS Systems Manager**: Patch management

#### Logical and Physical Access Controls (CC6)
- **AWS IAM**: Identity and access management
- **AWS IAM Identity Center (SSO)**: Single sign-on
- **AWS Secrets Manager**: Credential management
- **AWS KMS**: Encryption key management
- **Amazon VPC**: Network isolation
- **Security Groups**: Instance-level firewalls
- **Network ACLs**: Subnet-level firewalls
- **AWS Network Firewall**: Advanced network protection
- **AWS WAF**: Web application firewall
- **AWS Certificate Manager**: SSL/TLS certificates

#### System Operations (CC7)
- **AWS CloudTrail**: Audit logging
- **AWS CloudWatch**: Logging and monitoring
- **Amazon GuardDuty**: Threat detection
- **AWS Systems Manager Incident Manager**: Incident response
- **AWS Backup**: Data backup
- **VPC Flow Logs**: Network traffic logging

#### Change Management (CC8)
- **AWS Systems Manager Change Manager**: Change tracking
- **AWS Config**: Configuration tracking
- **AWS CloudFormation**: Infrastructure as Code
- **AWS CodePipeline**: CI/CD automation
- **AWS CodeCommit**: Version control
- **AWS Service Catalog**: Service approval

#### Risk Mitigation (CC9)
- **AWS Security Hub**: Risk visibility
- **AWS Trusted Advisor**: Best practice recommendations
- **AWS Artifact**: Third-party compliance reports

---

### Availability (A)

#### Availability (A1)
- **Amazon CloudWatch**: Capacity and health monitoring
- **AWS Auto Scaling**: Dynamic capacity management
- **AWS Health Dashboard**: Service health monitoring
- **AWS Backup**: Backup management
- **Amazon S3**: Reliable storage with versioning
- **Amazon RDS**: Multi-AZ databases
- **AWS Elastic Disaster Recovery**: DR solution

#### Performance and Availability Monitoring (A2)
- **Amazon CloudWatch**: Metrics and dashboards
- **AWS X-Ray**: Distributed tracing
- **Amazon CloudWatch Synthetics**: Synthetic monitoring
- **Amazon CloudWatch RUM**: Real user monitoring
- **AWS Compute Optimizer**: Performance optimization

---

### Confidentiality (C)

#### Confidential Information Identification (C1)
- **Amazon Macie**: Data discovery and classification
- **AWS Resource Tags**: Classification tagging
- **AWS IAM**: Classification-based access control

#### Encryption (C2)
- **AWS KMS**: Key management for encryption
- **Amazon S3**: Encryption at rest
- **Amazon EBS**: Volume encryption
- **Amazon RDS**: Database encryption
- **Amazon DynamoDB**: Table encryption
- **AWS Certificate Manager**: TLS certificates
- **Amazon CloudFront**: HTTPS delivery
- **Application/Network Load Balancer**: SSL/TLS termination

#### Network Protection (C3)
- **Amazon VPC**: Network isolation
- **Security Groups**: Instance firewalls
- **Network ACLs**: Subnet firewalls
- **AWS Network Firewall**: Advanced filtering
- **AWS PrivateLink**: Private connectivity
- **VPC Endpoints**: Private AWS service access
- **VPC Flow Logs**: Network monitoring

---

### Processing Integrity (PI)

#### Processing Inputs (PI1.1)
- **Amazon API Gateway**: Request validation
- **AWS WAF**: Input filtering
- **AWS Lambda**: Input validation functions
- **Amazon SQS**: Message queuing

#### Processing Completeness and Accuracy (PI1.2)
- **AWS Step Functions**: Workflow orchestration
- **Amazon SQS**: Guaranteed delivery
- **Amazon DynamoDB**: Atomic transactions
- **Amazon RDS**: ACID transactions
- **AWS X-Ray**: Distributed tracing

#### Processing Authorization (PI1.3)
- **Amazon API Gateway**: Authorizers
- **Amazon Cognito**: User authentication
- **AWS IAM**: Service authorization
- **AWS Lambda**: Custom authorization

#### Processing Timeliness (PI1.4)
- **AWS Lambda**: Real-time processing
- **Amazon SQS**: Asynchronous processing
- **Amazon Kinesis**: Stream processing
- **AWS Batch**: Batch processing
- **Amazon EventBridge**: Scheduled processing

#### Error Identification and Correction (PI1.5)
- **AWS CloudWatch**: Error monitoring
- **AWS X-Ray**: Error tracing
- **Amazon SQS**: Dead letter queues
- **Amazon SNS**: Error notifications
- **AWS Systems Manager OpsCenter**: Incident management

#### Output Completeness and Accuracy (PI2.1)
- **AWS Lambda**: Output validation
- **AWS Glue**: Data quality checks
- **Amazon Athena**: Output validation queries
- **Amazon S3**: Versioned output storage

---

### Privacy (P)

#### Notice and Communication (P1)
- **Amazon S3**: Privacy policy hosting
- **Amazon CloudFront**: Content delivery
- **Amazon Cognito**: Privacy notice during signup
- **Amazon SES**: Privacy notifications

#### Choice and Consent (P2)
- **Amazon Cognito**: Consent collection
- **AWS Lambda**: Consent logic
- **Amazon DynamoDB**: Consent storage
- **AWS CloudTrail**: Consent audit trail

#### Collection (P3)
- **Amazon API Gateway**: Collection validation
- **AWS Lambda**: Purpose validation
- **Amazon Macie**: Data discovery
- **AWS WAF**: Input filtering

#### Use, Retention, and Disposal (P4)
- **Amazon DynamoDB**: TTL for automatic deletion
- **Amazon S3**: Lifecycle policies
- **AWS KMS**: Cryptographic erasure
- **AWS Lambda**: Automated deletion
- **Amazon EventBridge**: Scheduled cleanup

#### Access (P5)
- **Amazon API Gateway**: Data access API
- **AWS Lambda**: Data retrieval and export
- **Amazon S3**: Data export storage
- **AWS Step Functions**: Deletion workflows

#### Disclosure to Third Parties (P6)
- **AWS CloudTrail**: Disclosure audit logging
- **Amazon S3**: Data transfer logs
- **AWS Lambda**: Disclosure validation

#### Security for Privacy (P7)
- See all Confidentiality (C) and Common Criteria (CC6) services

#### Data Quality (P8)
- **AWS Lambda**: Validation functions
- **Amazon API Gateway**: Input validation
- **AWS Glue**: Data quality rules

#### Monitoring and Enforcement (P9)
- **AWS Config**: Compliance monitoring
- **Amazon CloudWatch**: Operational monitoring
- **AWS Security Hub**: Security findings
- **Amazon Macie**: Data protection monitoring

---

## Service Selection Guide

### For Startups and Small Organizations

**Essential Services** (Budget: $200-500/month):
1. **AWS IAM** - Identity and access management (Free)
2. **AWS CloudTrail** - Audit logging ($50-100)
3. **Amazon CloudWatch** - Basic monitoring ($50-100)
4. **AWS Backup** - Data protection ($50-150)
5. **AWS Config** - Basic compliance rules ($50-100)
6. **AWS WAF** - Basic web protection ($50-100)

**Optional Additions** (+$200-500/month):
- **Amazon GuardDuty** - Threat detection
- **AWS Security Hub** - Centralized security view
- **AWS KMS** - Customer-managed encryption keys

### For Mid-Size Organizations

**Standard Services** (Budget: $1,000-3,000/month):
- All essential services above
- **AWS Organizations** - Multi-account structure
- **Amazon GuardDuty** - Threat detection
- **AWS Security Hub** - Security aggregation
- **AWS IAM Identity Center** - SSO
- **AWS Network Firewall** - Advanced network protection
- **Amazon Macie** - Data classification
- **AWS X-Ray** - Application tracing
- **AWS Systems Manager** - Fleet management

### For Enterprise Organizations

**Comprehensive Services** (Budget: $5,000-15,000+/month):
- All mid-size services above
- **AWS Audit Manager** - Continuous audit
- **AWS Detective** - Security investigation
- **AWS Elastic Disaster Recovery** - DR solution
- **Amazon CloudWatch RUM** - Real user monitoring
- **AWS Network Firewall** - Multiple firewalls
- **Amazon Macie** - Full data classification
- **AWS CloudFormation/Terraform** - Full IaC
- **Multiple regions** - Global redundancy

---

## Implementation Priority

### Phase 1: Foundation (Weeks 1-4)
**Critical for all organizations:**
1. Enable AWS CloudTrail in all regions
2. Set up AWS Config with basic rules
3. Configure AWS IAM with MFA
4. Enable AWS CloudWatch logging
5. Document policies and procedures

**Controls Addressed**: CC1, CC6, CC7

### Phase 2: Security Hardening (Weeks 5-8)
1. Enable Amazon GuardDuty
2. Deploy AWS WAF
3. Configure VPC with security groups
4. Enable encryption (S3, EBS, RDS)
5. Set up AWS Secrets Manager

**Controls Addressed**: CC3, CC5, CC6, C2

### Phase 3: Availability & Backup (Weeks 9-12)
1. Configure AWS Backup policies
2. Implement Multi-AZ architectures
3. Set up Auto Scaling
4. Configure CloudWatch alarms
5. Test disaster recovery

**Controls Addressed**: A1, A2, CC7

### Phase 4: Processing & Privacy (Weeks 13-16)
1. Implement input validation
2. Set up workflow orchestration
3. Configure data retention policies
4. Implement DSAR processes
5. Enable data classification

**Controls Addressed**: PI1, P3, P4, P5

### Phase 5: Monitoring & Compliance (Weeks 17-20)
1. Enable AWS Security Hub
2. Configure AWS Audit Manager
3. Set up comprehensive dashboards
4. Implement automated remediation
5. Prepare for audit

**Controls Addressed**: CC4, CC9, P9

---

## Cost Optimization Tips

### 1. Use AWS Budgets
Set up budget alerts to avoid cost overruns.

### 2. Leverage Free Tier
- AWS IAM: Free
- AWS CloudTrail: 1 free trail
- AWS Config: Limited free rules
- AWS Trusted Advisor: Basic checks free

### 3. Optimize Storage
- Use S3 Intelligent-Tiering for backups
- Enable S3 Bucket Keys (reduce KMS costs)
- Set appropriate CloudWatch Logs retention

### 4. Right-Size Resources
- Use AWS Compute Optimizer
- Review AWS Trusted Advisor recommendations
- Terminate unused resources

### 5. Reserved Capacity
- CloudWatch Log retention (1 year vs. indefinite)
- RDS Reserved Instances
- Savings Plans for compute

### 6. Aggregate Logs
- Centralize logs in one account
- Use cross-account access
- Reduce duplicate storage

---

## Integration Patterns

### Multi-Account Strategy
```
Root Account (AWS Organizations)
├── Security Account
│   ├── AWS CloudTrail (organization trail)
│   ├── AWS Config (aggregator)
│   ├── AWS Security Hub (delegated admin)
│   └── Amazon GuardDuty (delegated admin)
├── Production Account
│   └── Application resources
├── Development Account
│   └── Development resources
└── Shared Services Account
    └── Shared resources
```

### Logging and Monitoring Architecture
```
Application → CloudWatch Logs → CloudWatch Alarms → SNS → PagerDuty/Email
           → CloudTrail → S3 → Athena (queries)
           → VPC Flow Logs → CloudWatch Logs Insights
           → X-Ray → ServiceLens
```

### Backup and DR Architecture
```
Production Resources
├── AWS Backup (central policy)
│   ├── Daily backups → S3
│   ├── Cross-region copy
│   └── Backup Vault Lock
├── RDS Automated Backups
├── EBS Snapshots (via DLM)
└── S3 Cross-Region Replication
```

---

## Service Comparison

### Monitoring: CloudWatch vs. X-Ray vs. CloudTrail

| Feature | CloudWatch | X-Ray | CloudTrail |
|---------|-----------|-------|-----------|
| **Purpose** | Operational monitoring | Application tracing | Audit logging |
| **Data Type** | Metrics, logs, events | Request traces | API calls |
| **Use Case** | Infrastructure health | Application debugging | Compliance, security |
| **Cost** | $0.30/metric | $5/million traces | $2/100K events |
| **Retention** | Configurable | 30 days | Indefinite (S3) |

### Encryption: SSE-S3 vs. SSE-KMS vs. SSE-C

| Feature | SSE-S3 | SSE-KMS | SSE-C |
|---------|--------|---------|-------|
| **Key Management** | AWS managed | Customer managed | Customer provided |
| **Audit Trail** | Limited | CloudTrail logs | None |
| **Cost** | Free | $1/key + requests | Free |
| **Compliance** | Basic | Advanced | Advanced |
| **Rotation** | Automatic | Automatic | Manual |

### Backup: AWS Backup vs. Native Backups

| Feature | AWS Backup | Native (RDS, etc.) |
|---------|-----------|-------------------|
| **Centralized** | Yes | No |
| **Cross-service** | Yes | No |
| **Policy-based** | Yes | Limited |
| **Cross-region** | Yes | Manual |
| **Compliance features** | Vault Lock | Limited |
| **Cost** | Storage + jobs | Storage only |

---

## Next Steps

1. Identify which controls apply to your organization
2. Select appropriate AWS services from this mapping
3. Review the detailed implementation guides for each service
4. Estimate costs using the AWS Pricing Calculator
5. Plan your implementation roadmap
6. Begin with Phase 1 (Foundation) services

[← Back to Main Guide](../README.md) | [View Implementation Guide →](implementation-guide.md)
