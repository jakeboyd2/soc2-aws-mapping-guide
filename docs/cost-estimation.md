# Cost Estimation Guide

Detailed cost breakdown for implementing SOC 2 controls on AWS.

## Cost Overview

SOC 2 compliance on AWS involves three main cost categories:
1. **AWS Service Costs**: Infrastructure and security services
2. **Audit Costs**: External auditor fees
3. **Internal Costs**: Staff time, training, tools

---

## AWS Service Costs

### Small Organization
**Profile**: 1-10 employees, single application, 1 AWS account, <100 users

#### Essential Services (Minimum Viable Compliance)

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **AWS CloudTrail** | 1 trail, 90-day retention | $50-100 |
| **AWS Config** | 10 rules, 100 resources | $50-100 |
| **Amazon CloudWatch** | Basic logs and metrics | $50-100 |
| **AWS IAM** | Identity management | $0 (Free) |
| **AWS Backup** | 500GB backups | $50-150 |
| **AWS KMS** | 5-10 keys | $20-50 |
| **AWS WAF** | Basic rules, 1M requests | $50-100 |
| **VPC Flow Logs** | 10GB/month | $5-10 |
| **S3 Storage** | 1TB (logs, backups) | $25-50 |

**Subtotal**: **$300-660/month**

#### Recommended Additional Services

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **Amazon GuardDuty** | 1 account | $50-150 |
| **AWS Security Hub** | 1 account, 2 standards | $50-100 |
| **AWS IAM Identity Center** | 10 users | $0-50 |
| **AWS Secrets Manager** | 10 secrets | $10-30 |

**Subtotal**: **$110-330/month**

**Total Small Org**: **$410-990/month**  
**Annual**: **~$5,000-12,000/year**

---

### Medium Organization
**Profile**: 11-100 employees, multiple applications, 3-5 AWS accounts, 100-500 users

#### Comprehensive Services

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **AWS CloudTrail** | Org trail, 365-day retention | $150-300 |
| **AWS Config** | 50 rules, 500 resources | $200-400 |
| **Amazon CloudWatch** | Advanced logs, custom metrics | $200-500 |
| **AWS Organizations** | Multi-account structure | $0 (Free) |
| **Amazon GuardDuty** | 3-5 accounts | $200-500 |
| **AWS Security Hub** | 3-5 accounts, 3 standards | $200-400 |
| **AWS IAM Identity Center** | 100 users | $50-150 |
| **AWS Backup** | 5TB backups, cross-region | $300-800 |
| **AWS KMS** | 20-50 keys | $50-150 |
| **AWS WAF** | Advanced rules, 10M requests | $200-500 |
| **AWS Network Firewall** | 1 firewall | $400-800 |
| **VPC Flow Logs** | 100GB/month | $50-100 |
| **AWS X-Ray** | 10M traces/month | $50-100 |
| **Amazon Macie** | 10TB scanned | $100-500 |
| **AWS Systems Manager** | 100 managed instances | $50-150 |
| **S3 Storage** | 10TB (logs, backups) | $230-500 |
| **AWS Inspector** | 50 instances | $50-150 |
| **AWS Secrets Manager** | 50 secrets | $50-150 |

**Total Medium Org**: **$2,530-6,150/month**  
**Annual**: **~$30,000-74,000/year**

---

### Large/Enterprise Organization
**Profile**: 100+ employees, complex infrastructure, 10+ AWS accounts, 500+ users

#### Enterprise Services

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **AWS CloudTrail** | Org trail, multi-year retention | $500-1,000 |
| **AWS Config** | 200+ rules, 2,000+ resources | $800-1,500 |
| **Amazon CloudWatch** | Enterprise logs, dashboards | $1,000-2,500 |
| **AWS Organizations** | Complex OU structure | $0 (Free) |
| **Amazon GuardDuty** | 10+ accounts | $800-2,000 |
| **AWS Security Hub** | 10+ accounts, all standards | $500-1,000 |
| **AWS IAM Identity Center** | 500+ users | $250-500 |
| **AWS Backup** | 50TB+, multi-region | $2,000-5,000 |
| **AWS KMS** | 100+ keys | $150-300 |
| **AWS WAF** | Multiple ACLs, 100M+ requests | $1,000-3,000 |
| **AWS Network Firewall** | Multiple firewalls | $2,000-5,000 |
| **VPC Flow Logs** | 1TB+/month | $500-1,000 |
| **AWS X-Ray** | 100M+ traces/month | $500-1,000 |
| **Amazon Macie** | 100TB+ scanned | $1,000-10,000 |
| **AWS Systems Manager** | 1,000+ managed instances | $300-800 |
| **AWS Audit Manager** | Continuous compliance | $500-1,000 |
| **AWS Detective** | Investigation tool | $300-800 |
| **S3 Storage** | 100TB+ (logs, backups) | $2,300-5,000 |
| **AWS Inspector** | 500+ instances | $500-1,500 |
| **AWS Elastic Disaster Recovery** | DR replication | $1,000-3,000 |
| **AWS Secrets Manager** | 500+ secrets | $500-1,000 |
| **CloudWatch Synthetics** | 50+ canaries | $100-300 |
| **CloudWatch RUM** | 100M events/month | $100-200 |

**Total Enterprise Org**: **$15,600-48,400/month**  
**Annual**: **~$187,000-581,000/year**

---

## Cost Breakdown by Control Category

### Common Criteria (CC)

| Controls | Services | Small | Medium | Large |
|----------|----------|-------|--------|-------|
| **CC1-CC2** (Control Environment) | Organizations, IAM, CloudTrail | $50 | $150 | $500 |
| **CC3** (Risk Assessment) | GuardDuty, Security Hub, Inspector | $100 | $450 | $1,600 |
| **CC4** (Monitoring) | Config, CloudWatch, Audit Manager | $150 | $750 | $2,800 |
| **CC5** (Control Activities) | Config Rules, WAF, Systems Manager | $100 | $450 | $2,300 |
| **CC6** (Access Controls) | IAM, KMS, Network Firewall, VPC | $100 | $1,200 | $5,800 |
| **CC7** (System Operations) | CloudTrail, CloudWatch, Backup | $200 | $1,200 | $8,000 |
| **CC8** (Change Management) | Config, CloudFormation, CodePipeline | $50 | $200 | $800 |
| **CC9** (Risk Mitigation) | Security Hub, Trusted Advisor | $50 | $200 | $500 |

**Total CC**: $800 | $4,600 | $22,300

### Availability (A)

| Controls | Services | Small | Medium | Large |
|----------|----------|-------|--------|-------|
| **A1** (Availability) | CloudWatch, Auto Scaling, Backup | $150 | $800 | $5,000 |
| **A2** (Performance Monitoring) | CloudWatch, X-Ray, Synthetics | $50 | $300 | $1,500 |

**Total A**: $200 | $1,100 | $6,500

### Confidentiality (C)

| Controls | Services | Small | Medium | Large |
|----------|----------|-------|--------|-------|
| **C1** (Data Classification) | Macie, Tags | $50 | $250 | $2,000 |
| **C2** (Encryption) | KMS, ACM, S3, EBS | $50 | $200 | $800 |
| **C3** (Network Protection) | VPC, Network Firewall, WAF | $100 | $1,000 | $7,000 |

**Total C**: $200 | $1,450 | $9,800

### Processing Integrity (PI)

| Controls | Services | Small | Medium | Large |
|----------|----------|-------|--------|-------|
| **PI1-PI2** (Processing) | Lambda, Step Functions, SQS, API GW | $100 | $500 | $3,000 |

**Total PI**: $100 | $500 | $3,000

### Privacy (P)

| Controls | Services | Small | Medium | Large |
|----------|----------|-------|--------|-------|
| **P1-P9** (Privacy) | Macie, Cognito, DynamoDB, S3 | $100 | $500 | $2,500 |

**Total P**: $100 | $500 | $2,500

---

## Additional Costs

### External Audit Costs

| Organization Size | SOC 2 Type I | SOC 2 Type II | Annual Surveillance |
|------------------|--------------|---------------|---------------------|
| **Small** | $8,000-15,000 | $15,000-30,000 | $10,000-20,000 |
| **Medium** | $15,000-30,000 | $30,000-60,000 | $20,000-40,000 |
| **Large** | $30,000-75,000 | $75,000-150,000+ | $50,000-100,000+ |

**Note**: Prices vary significantly based on scope, complexity, and auditor.

### Internal Staff Costs

| Role | Time Commitment | Annual Cost (Fully Loaded) |
|------|----------------|---------------------------|
| **Security Engineer** | 50-100% (Year 1), 25-50% (Ongoing) | $75,000-150,000 |
| **Compliance Manager** | 50% | $60,000-100,000 |
| **DevOps Engineer** | 25-50% | $40,000-80,000 |
| **Project Manager** | 25% (Year 1) | $30,000-50,000 |

**Total Internal Costs (Year 1)**: $205,000-380,000 (can vary widely)

### Training and Certifications

| Item | Cost |
|------|------|
| **AWS Security Specialty Cert** | $300 per person |
| **CISSP/CISM Certification** | $700 + study materials |
| **SOC 2 Training Course** | $500-2,000 per person |
| **AWS Training Subscription** | $29-299/month per person |
| **Compliance Consulting** | $150-400/hour (optional) |

**Annual Training Budget**: $5,000-20,000

### Tools and Software

| Tool | Purpose | Annual Cost |
|------|---------|-------------|
| **Terraform/Pulumi** | Infrastructure as Code | $0-15,000 |
| **Documentation Tools** | Confluence, Notion | $500-5,000 |
| **Ticketing System** | Jira, ServiceNow | $1,000-10,000 |
| **SIEM (if needed)** | Log aggregation beyond CloudWatch | $5,000-50,000 |
| **GRC Platform** | Vanta, Drata, Secureframe | $12,000-50,000 |

**Annual Tools Budget**: $18,500-130,000

---

## Total Cost of Ownership (TCO)

### Year 1 (Initial Implementation)

| Cost Category | Small | Medium | Large |
|--------------|-------|--------|-------|
| **AWS Services** | $5K-12K | $30K-74K | $187K-581K |
| **External Audit (Type II)** | $15K-30K | $30K-60K | $75K-150K |
| **Internal Staff** | $100K-200K | $205K-380K | $300K-500K |
| **Training** | $5K-10K | $10K-20K | $15K-30K |
| **Tools** | $5K-20K | $18K-50K | $50K-130K |
| **Consulting (optional)** | $10K-30K | $30K-75K | $75K-200K |

**Year 1 Total**: $140K-302K | $323K-659K | $702K-1,591K

### Ongoing Annual Costs (Year 2+)

| Cost Category | Small | Medium | Large |
|--------------|-------|--------|-------|
| **AWS Services** | $5K-12K | $30K-74K | $187K-581K |
| **Annual Surveillance Audit** | $10K-20K | $20K-40K | $50K-100K |
| **Internal Staff (reduced)** | $50K-100K | $100K-200K | $200K-350K |
| **Training** | $3K-7K | $7K-15K | $10K-25K |
| **Tools** | $5K-20K | $18K-50K | $50K-130K |

**Annual Ongoing**: $73K-159K | $175K-379K | $497K-1,186K

---

## Cost Optimization Strategies

### 1. Right-Size Resources

**Potential Savings: 20-40%**

- Use AWS Compute Optimizer
- Review Trusted Advisor recommendations
- Terminate unused resources
- Use Reserved Instances/Savings Plans
- Downsize over-provisioned instances

### 2. Optimize Log Retention

**Potential Savings: 10-30%**

- Set appropriate CloudWatch Logs retention (e.g., 90 days vs. indefinite)
- Use S3 Intelligent-Tiering for long-term log storage
- Transition old logs to Glacier ($0.004/GB vs. $0.50/GB)

**Example**: 1TB of logs for 7 years
- CloudWatch Logs: $3,650/year
- S3 Standard: $276/year
- S3 Glacier: $33.60/year

**Savings: $3,616/year (99% reduction)**

### 3. Leverage Free Tier

**Potential Savings: $500-2,000/year**

- AWS IAM: Free
- AWS CloudTrail: 1 free trail
- AWS Trusted Advisor: Basic checks free
- First 5GB VPC Flow Logs: Free
- AWS Config: 2 free rules in first month

### 4. Use S3 Bucket Keys

**Potential Savings: 50-99% on KMS costs**

Without S3 Bucket Keys:
- 1M PUT requests = 1M KMS requests = $30
  
With S3 Bucket Keys:
- 1M PUT requests = ~100 KMS requests = $0.003

**Savings: $29.997 per 1M requests (99.99% reduction)**

### 5. Aggregate Logs Centrally

**Potential Savings: 20-40%**

- Use one centralized logging account
- Avoid duplicate logging in multiple accounts
- Use cross-account access for analysis

### 6. Automate Everything

**Potential Savings: Reduces staff time by 50-70%**

- Infrastructure as Code (CloudFormation/Terraform)
- Automated evidence collection
- Automated remediation (Lambda, Step Functions)
- Automated compliance checks (Config Rules)

### 7. Use GRC Platforms

**Cost: $12K-50K/year, Saves: $50K-150K in staff time**

- Automates evidence collection
- Manages audit workflow
- Continuous compliance monitoring
- Pre-built control frameworks

Examples: Vanta, Drata, Secureframe, Tugboat Logic

### 8. Compress and Archive

**Potential Savings: 10-20%**

- Compress CloudTrail logs before storage
- Use S3 lifecycle policies
- Archive old evidence to Glacier

---

## ROI Calculation

### Benefits of SOC 2 Certification

1. **Revenue Enablement**
   - Enterprise customers often require SOC 2
   - Average deal size increase: 2-3x
   - Win rate increase: 20-40%

2. **Customer Trust**
   - Reduced sales cycle (30-50% faster)
   - Higher conversion rates
   - Premium pricing capability (+10-20%)

3. **Risk Reduction**
   - Lower insurance premiums (10-30%)
   - Reduced breach probability (60-80%)
   - Faster incident response (50-70%)

4. **Operational Efficiency**
   - Streamlined security processes
   - Reduced manual work (50-70%)
   - Better visibility and control

### Example ROI Calculation (Medium Organization)

**Costs (Year 1)**: $323,000-659,000
**Costs (Annual Ongoing)**: $175,000-379,000

**Benefits (Annual)**:
- Revenue from new enterprise customers: $500,000-2,000,000
- Reduced breach risk: $100,000-500,000 (avoided costs)
- Operational efficiency: $50,000-150,000
- Insurance savings: $10,000-30,000

**Total Benefits**: $660,000-2,680,000/year

**ROI**: 100-400% in Year 1, 200-600% ongoing

**Break-even**: Typically 6-12 months

---

## Budget Planning Template

### Year 1 Budget

| Quarter | Activity | AWS Costs | Professional Services | Internal Staff | Total |
|---------|----------|-----------|---------------------|----------------|-------|
| **Q1** | Planning & Foundation | $X | $X | $X | $X |
| **Q2** | Implementation | $X | $X | $X | $X |
| **Q3** | Testing & Evidence | $X | $X | $X | $X |
| **Q4** | Audit Preparation | $X | $X | $X | $X |
| **Year 1 Total** | | | | | |

### Ongoing Annual Budget

| Quarter | Activity | AWS Costs | Audit | Internal Staff | Total |
|---------|----------|-----------|-------|----------------|-------|
| **Q1-Q4** | Operations & Evidence Collection | $X/qtr | Annual | $X/qtr | $X |
| **Annual Total** | | | | | |

---

## Cost Comparison: DIY vs. GRC Platform

### DIY Approach (Medium Org)

| Component | Annual Cost |
|-----------|-------------|
| AWS Services | $30K-74K |
| Audit | $20K-40K |
| Staff (0.75 FTE) | $150K |
| Tools | $18K-50K |
| **Total** | **$218K-314K** |

### GRC Platform Approach (Medium Org)

| Component | Annual Cost |
|-----------|-------------|
| AWS Services | $30K-74K |
| Audit | $20K-40K |
| Staff (0.25 FTE) | $50K |
| GRC Platform | $20K-40K |
| **Total** | **$120K-204K** |

**Savings with GRC Platform**: $98K-110K/year (45-50% reduction)

**Best for**: Organizations without dedicated compliance staff

---

## FAQ

### Q: Can I get SOC 2 certified for less than $100K/year?
**A**: Yes, small organizations can achieve SOC 2 for $70K-160K/year total, including:
- AWS: $5K-12K
- Audit: $15K-30K
- Staff: $50K-100K (part-time or outsourced)
- Tools: $5K-20K

### Q: What's the minimum AWS spend for SOC 2?
**A**: Approximately $300-500/month ($3,600-6,000/year) for essential services only. However, $500-1,000/month is more realistic for proper coverage.

### Q: How much does it cost to maintain SOC 2?
**A**: After initial certification, annual costs typically drop 40-60% as infrastructure is already in place. Ongoing costs primarily cover monitoring, evidence collection, and annual audit.

### Q: Should I use a GRC platform?
**A**: Recommended if:
- You lack dedicated compliance staff
- You want faster time to certification
- You need continuous compliance monitoring
- Your staff time costs exceed platform costs

### Q: Can I reduce costs after certification?
**A**: Limited. Most services are essential for maintaining compliance. Focus on optimization (right-sizing, reserved capacity, log retention tuning) rather than elimination.

---

## Next Steps

1. Use this guide to estimate your organization's costs
2. Create a detailed budget proposal
3. Identify cost optimization opportunities
4. Get executive approval
5. Begin implementation

[← Back to Evidence Collection](evidence-collection.md) | [View Quick Reference →](quick-reference.md) | [Back to Main Guide](../README.md)
