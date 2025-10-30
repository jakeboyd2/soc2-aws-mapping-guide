# Reference Architecture: SaaS Application

A complete SOC 2 compliant architecture for a multi-tenant SaaS application on AWS.

## Architecture Overview

This reference architecture demonstrates a SOC 2 Type II compliant SaaS application with:
- Multi-tenant architecture
- High availability (99.9% uptime)
- Comprehensive security controls
- Automated compliance monitoring
- Data encryption at rest and in transit
- Disaster recovery capabilities

**Estimated Monthly Cost**: $2,000-5,000 (excluding application compute)

---

## High-Level Architecture

```
                            ┌─────────────────────────────────────┐
                            │     Route 53 (DNS + Health Checks)  │
                            │     *.example.com → CloudFront      │
                            └────────────────┬────────────────────┘
                                             │
                            ┌────────────────▼────────────────────┐
                            │        CloudFront (CDN)             │
                            │    • HTTPS only (ACM cert)          │
                            │    • AWS WAF (Web Firewall)         │
                            │    • Shield Standard (DDoS)         │
                            │    • Custom domain                  │
                            └────────────────┬────────────────────┘
                                             │
                    ┌────────────────────────┴────────────────────────┐
                    │                                                 │
    ┌───────────────▼────────────────┐          ┌──────────────────▼──────────────┐
    │   Production VPC (us-east-1)   │          │   DR VPC (us-west-2)            │
    │   10.0.0.0/16                  │          │   10.1.0.0/16                   │
    └────────────────────────────────┘          └─────────────────────────────────┘
```

---

## Detailed Production VPC Architecture

```
Production VPC (10.0.0.0/16)
├── Availability Zone A (us-east-1a)
│   ├── Public Subnet (10.0.1.0/24)
│   │   ├── NAT Gateway
│   │   └── Application Load Balancer
│   ├── Private Subnet - Application (10.0.10.0/24)
│   │   ├── ECS Tasks (Fargate)
│   │   ├── Security Group: App-SG
│   │   └── VPC Endpoints (S3, DynamoDB, Secrets Manager, KMS)
│   └── Private Subnet - Data (10.0.20.0/24)
│       ├── RDS Aurora (Primary)
│       ├── ElastiCache Redis (Primary)
│       └── Security Group: DB-SG
│
└── Availability Zone B (us-east-1b)
    ├── Public Subnet (10.0.2.0/24)
    │   ├── NAT Gateway
    │   └── Application Load Balancer
    ├── Private Subnet - Application (10.0.11.0/24)
    │   ├── ECS Tasks (Fargate)
    │   └── Security Group: App-SG
    └── Private Subnet - Data (10.0.21.0/24)
        ├── RDS Aurora (Replica)
        ├── ElastiCache Redis (Replica)
        └── Security Group: DB-SG

Security & Monitoring
├── AWS WAF (attached to ALB and CloudFront)
├── VPC Flow Logs → CloudWatch Logs → S3
├── Network Firewall (optional, for advanced inspection)
└── Security Groups (all traffic denied by default)
```

---

## Component Details

### 1. Identity & Access Management

**IAM**:
- ❌ No IAM users (except break-glass)
- ✅ IAM Identity Center for SSO
- ✅ MFA enforced for all humans
- ✅ IAM roles for all services
- ✅ Least privilege policies
- ✅ Password policy: 14+ chars, 90-day rotation

**Cognito**:
- User authentication for SaaS application
- Social IdP integration (Google, GitHub)
- MFA available for end users
- Custom domain
- Password policy enforcement

**Controls Addressed**: CC6.1, CC6.2, CC6.3, CC1.3

### 2. Network Security

**VPC Configuration**:
- Multi-AZ deployment (us-east-1a, us-east-1b)
- 3-tier architecture (public, private-app, private-data)
- No direct internet access for application/data tiers
- VPC Endpoints for AWS services (no internet routing)

**Security Groups**:
```
ALB-SG:
  Inbound: 443 from 0.0.0.0/0
  Outbound: 8080 to App-SG

App-SG:
  Inbound: 8080 from ALB-SG
  Outbound: 5432 to DB-SG, 6379 to Cache-SG, 443 to VPC Endpoints

DB-SG:
  Inbound: 5432 from App-SG
  Outbound: None

Cache-SG:
  Inbound: 6379 from App-SG
  Outbound: None
```

**AWS WAF Rules**:
- AWS Managed Rules: Core Rule Set
- AWS Managed Rules: Known Bad Inputs
- Rate limiting: 2,000 requests per 5 minutes per IP
- Geo-blocking (if needed)
- SQL injection protection
- XSS protection

**VPC Flow Logs**:
- Enabled for all subnets
- Sent to CloudWatch Logs
- Retention: 90 days
- Cross-account copy to security account

**Controls Addressed**: CC6.6, CC6.7, C3.1

### 3. Application Layer

**ECS Fargate**:
- Serverless container platform
- No server management
- Auto-scaling based on CPU/memory
- Task definitions in version control
- Immutable deployments

**Application Load Balancer**:
- Multi-AZ deployment
- HTTPS only (HTTP redirects to HTTPS)
- ACM certificate
- Health checks every 30 seconds
- Deregistration delay: 30 seconds

**API Gateway** (for APIs):
- REST or HTTP APIs
- Lambda authorizer (JWT validation)
- Request validation
- API keys for partners
- Rate limiting per client

**Controls Addressed**: A1.1, A1.2, PI1.1, PI1.3

### 4. Data Layer

**RDS Aurora PostgreSQL**:
- Multi-AZ with automatic failover
- Encrypted at rest (KMS customer-managed key)
- SSL/TLS required for connections
- Automated backups (35-day retention)
- Manual snapshots before changes
- Point-in-time recovery enabled
- Enhanced monitoring
- Performance Insights

**ElastiCache Redis**:
- Multi-AZ with automatic failover
- Encryption at rest
- Encryption in transit
- Auth token required
- Automatic backups

**DynamoDB**:
- On-demand or provisioned capacity
- Point-in-time recovery
- Encryption at rest (KMS)
- DynamoDB Streams for CDC
- Global tables for DR (optional)

**S3 Buckets**:
```
Files Bucket (app-files-production):
- Default encryption (SSE-KMS)
- Versioning enabled
- Lifecycle policies (transition to IA after 90 days)
- Cross-region replication to DR
- Block public access
- Bucket policy: HTTPS only
- Access logs enabled

Logs Bucket (app-logs-production):
- Default encryption (SSE-S3)
- Lifecycle: Archive to Glacier after 90 days
- MFA delete enabled
- Block public access
- No public access ever

Backups Bucket (app-backups-production):
- Encryption (SSE-KMS)
- Versioning enabled
- S3 Object Lock (compliance mode)
- Vault Lock for immutability
- Cross-region replication
```

**Controls Addressed**: C2.1, C2.2, A1.3, CC7.4

### 5. Encryption

**AWS KMS Keys**:
```
kms-production-data (CMK):
- Purpose: RDS, S3 (sensitive data)
- Automatic rotation: Enabled (annual)
- Key policy: Restrict to data services only
- Deletion window: 30 days

kms-production-app (CMK):
- Purpose: ECS secrets, application secrets
- Automatic rotation: Enabled
- Key policy: Application services only
- Deletion window: 30 days

kms-production-logs (CMK):
- Purpose: CloudTrail, CloudWatch Logs
- Automatic rotation: Enabled
- Key policy: Logging services only
- Deletion window: 30 days
```

**Secrets Manager**:
- Database passwords
- API keys
- Third-party credentials
- Automatic rotation (30-90 days)
- Encryption with KMS
- Audit access via CloudTrail

**Controls Addressed**: C2.1, CC6.1, CC6.4

### 6. Logging & Monitoring

**CloudTrail**:
- Organization trail (all accounts)
- All regions enabled
- Management and data events
- Log file validation enabled
- Encrypted logs (KMS)
- Sent to dedicated S3 bucket
- CloudWatch Logs integration
- 7-year retention

**CloudWatch**:
```
Logs:
- Application logs (retention: 30 days)
- VPC Flow Logs (retention: 90 days)
- Lambda logs (retention: 30 days)
- API Gateway logs (retention: 90 days)

Metrics:
- Application-level custom metrics
- Infrastructure metrics
- Business metrics (active users, transactions)

Dashboards:
- Executive dashboard (availability, errors, users)
- Operations dashboard (latency, throughput, errors)
- Security dashboard (failed logins, suspicious activity)

Alarms:
- High error rate (5xx > 1% for 5 minutes)
- High latency (p99 > 1000ms for 5 minutes)
- Database CPU > 80%
- Failed login attempts > 10 in 5 minutes
- CloudTrail disabled
- Config non-compliance
```

**AWS Config**:
```
Config Rules:
- cloudtrail-enabled
- multi-region-cloudtrail-enabled
- encrypted-volumes
- s3-bucket-server-side-encryption-enabled
- rds-storage-encrypted
- dynamodb-table-encrypted-kms
- iam-password-policy
- iam-user-mfa-enabled
- rds-multi-az-support
- s3-bucket-public-read-prohibited
- s3-bucket-public-write-prohibited
- vpc-flow-logs-enabled
- guardduty-enabled-centralized

Delivery:
- Configuration history to S3
- Compliance reports daily
- Aggregator in security account
```

**Controls Addressed**: CC7.2, CC4.1, CC8.1

### 7. Security Services

**Amazon GuardDuty**:
- Enabled in all regions
- All log sources enabled (CloudTrail, VPC Flow Logs, DNS)
- Findings sent to Security Hub
- Critical/High findings → SNS → PagerDuty
- 90-day retention

**AWS Security Hub**:
- Enabled in all accounts
- Standards: AWS Foundational, CIS AWS Foundations
- Aggregator in security account
- Automated remediation via EventBridge + Lambda
- Daily compliance reports

**Amazon Macie**:
- S3 bucket scanning (weekly)
- Sensitive data discovery (PII, financial data)
- Findings sent to Security Hub
- Automated remediation for exposed data

**AWS Inspector**:
- Container image scanning (ECR)
- Monthly EC2 scans (if used)
- CVE detection
- Findings sent to Security Hub

**Controls Addressed**: CC3.2, CC3.3, CC7.1, C1.1

### 8. Backup & Disaster Recovery

**AWS Backup**:
```
Backup Plan: Production-Daily
- RDS: Daily at 2 AM UTC, 35-day retention
- DynamoDB: Daily at 2 AM UTC, 35-day retention
- EFS: Daily at 2 AM UTC, 35-day retention
- Cross-region copy: Enabled (us-west-2)
- Backup vault: Production-Vault
- Vault Lock: 35-day retention minimum (compliance mode)
```

**Cross-Region Replication**:
- S3 buckets: Real-time replication to us-west-2
- DynamoDB: Global tables (optional, for active-active)
- RDS: Cross-region read replica in us-west-2

**Disaster Recovery Plan**:
- RPO: 1 hour (hourly snapshots if needed)
- RTO: 4 hours (failover to DR region)
- Monthly DR tests
- Runbooks for common scenarios

**Controls Addressed**: A1.3, CC7.4, CC7.5

### 9. CI/CD Pipeline

**AWS CodePipeline**:
```
Pipeline: Production-Deploy
1. Source: GitHub (main branch)
2. Build: AWS CodeBuild
   - Run tests
   - Security scan (Snyk, Trivy)
   - Build Docker image
   - Push to ECR
3. Deploy to Staging: ECS (staging cluster)
4. Integration Tests: CodeBuild
5. Manual Approval: Required by Security team
6. Deploy to Production: ECS (production cluster)
7. Smoke Tests: CodeBuild
```

**Change Management**:
- All changes via pull requests
- 2 approvals required
- Automated tests must pass
- Security review for infrastructure changes
- Change logged in Systems Manager Change Manager

**Controls Addressed**: CC8.1, CC3.4

### 10. User Application Features

**Privacy Features**:
- Privacy policy (hosted on S3 + CloudFront)
- Consent collection (Cognito custom attributes)
- Data export API (Lambda + Step Functions)
- Data deletion API (Lambda + Step Functions)
- Preference management (DynamoDB)

**Processing Integrity**:
- Input validation (API Gateway + Lambda)
- Idempotent API endpoints
- Transaction support (DynamoDB transactions)
- Error handling and DLQ
- Audit trail for all actions

**Controls Addressed**: P1-P9, PI1-PI2

---

## AWS Account Structure

```
Root Organization (AWS Organizations)
├── Security OU
│   └── Security Account (123456789012)
│       ├── CloudTrail (org trail)
│       ├── Config Aggregator
│       ├── Security Hub (delegated admin)
│       └── GuardDuty (delegated admin)
├── Production OU
│   └── Production Account (234567890123)
│       └── Application resources (this architecture)
├── Development OU
│   └── Dev Account (345678901234)
│       └── Development/staging resources
└── Shared Services OU
    └── Shared Services Account (456789012345)
        ├── CI/CD pipeline
        ├── ECR repositories
        └── Centralized logging
```

**Service Control Policies (SCPs)**:
- Deny leaving organization
- Deny disabling CloudTrail
- Deny disabling Config
- Require encryption
- Restrict to approved regions (us-east-1, us-west-2)

---

## Cost Breakdown

### Monthly Estimated Costs

| Component | Configuration | Monthly Cost |
|-----------|--------------|--------------|
| **Compute** |
| ECS Fargate | 4 tasks, 2 vCPU, 4GB each | $200-400 |
| Lambda | 10M invocations | $20-50 |
| **Storage** |
| RDS Aurora | db.r5.large, Multi-AZ | $400-600 |
| ElastiCache | cache.r5.large | $150-250 |
| S3 | 1TB | $25-50 |
| EFS (if used) | 100GB | $30-50 |
| **Networking** |
| ALB | 2 AZs | $30-50 |
| NAT Gateway | 2 AZs, 1TB data | $90-150 |
| CloudFront | 1TB transfer | $100-150 |
| **Security** |
| GuardDuty | 1 account | $50-150 |
| WAF | 10M requests | $50-100 |
| Secrets Manager | 20 secrets | $10-30 |
| **Monitoring** |
| CloudWatch | Logs + metrics | $100-200 |
| X-Ray | 5M traces | $25-50 |
| **Backup** |
| AWS Backup | 500GB | $50-100 |
| S3 backup storage | 500GB | $15-30 |
| **Other** |
| Config | 100 resources | $50-100 |
| CloudTrail | Org trail | $50-100 |
| KMS | 10 keys | $10-30 |

**Total**: **$1,455-2,740/month** (excluding variable costs like data transfer)

**With DR (active-passive)**: **+$500-1,000/month**

**Total with DR**: **$2,000-4,000/month**

---

## Implementation Checklist

### Week 1-2: Foundation
- [ ] Create AWS Organization structure
- [ ] Enable CloudTrail (org trail)
- [ ] Enable AWS Config
- [ ] Set up IAM Identity Center
- [ ] Enable MFA for all users

### Week 3-4: Networking
- [ ] Create VPC in us-east-1
- [ ] Configure subnets (public, private-app, private-data)
- [ ] Set up NAT Gateways
- [ ] Configure security groups
- [ ] Enable VPC Flow Logs
- [ ] Deploy ALB

### Week 5-6: Application
- [ ] Set up ECS cluster (Fargate)
- [ ] Deploy application containers
- [ ] Configure ALB target groups
- [ ] Set up Auto Scaling

### Week 7-8: Data
- [ ] Create RDS Aurora cluster (encrypted)
- [ ] Configure ElastiCache Redis
- [ ] Create S3 buckets with encryption
- [ ] Enable DynamoDB with encryption

### Week 9-10: Security
- [ ] Create KMS keys
- [ ] Set up Secrets Manager
- [ ] Configure AWS WAF
- [ ] Enable GuardDuty
- [ ] Enable Security Hub
- [ ] Configure Macie

### Week 11-12: Monitoring & Backup
- [ ] Configure CloudWatch dashboards
- [ ] Set up alarms
- [ ] Enable X-Ray
- [ ] Configure AWS Backup
- [ ] Test backup restoration

### Week 13-14: CI/CD
- [ ] Set up CodePipeline
- [ ] Configure CodeBuild
- [ ] Implement change approval
- [ ] Test deployment

### Week 15-16: DR & Testing
- [ ] Set up DR region resources
- [ ] Configure cross-region replication
- [ ] Test DR failover
- [ ] Document runbooks

### Week 17-20: Documentation & Evidence
- [ ] Complete system description
- [ ] Document all procedures
- [ ] Begin evidence collection
- [ ] Prepare for audit

---

## Security Best Practices Implemented

✅ Multi-AZ for high availability  
✅ Encryption at rest and in transit  
✅ Least privilege access (IAM)  
✅ MFA enforced  
✅ No public database access  
✅ VPC isolation  
✅ WAF protection  
✅ DDoS protection (Shield)  
✅ Comprehensive logging  
✅ Automated monitoring  
✅ Backup and DR  
✅ Immutable infrastructure  
✅ Infrastructure as Code  
✅ Automated compliance checks  
✅ Incident response plan  

---

## Next Steps

1. Review this architecture for your use case
2. Customize based on your requirements
3. Deploy using Infrastructure as Code (Terraform/CloudFormation)
4. Test thoroughly before production
5. Begin evidence collection immediately

[← Back to Main Guide](../README.md) | [View More Architectures →](../docs/architectures/)
