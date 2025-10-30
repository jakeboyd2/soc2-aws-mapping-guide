# Frequently Asked Questions (FAQ)

Common questions about SOC 2 compliance on AWS.

## General SOC 2 Questions

### What is SOC 2?

SOC 2 (Service Organization Control 2) is an auditing procedure that ensures service providers securely manage data to protect the interests and privacy of their clients. It's based on the AICPA's Trust Service Criteria.

### Do I need SOC 2?

You likely need SOC 2 if:
- ✅ You provide SaaS or cloud services to other businesses
- ✅ Enterprise customers are requesting it
- ✅ You store or process customer data
- ✅ You're in healthcare, finance, or handle sensitive data
- ✅ You want to demonstrate security commitment
- ✅ Competitors have SOC 2 certification

### What's the difference between SOC 2 Type I and Type II?

**Type I**: Auditor tests your controls at a specific point in time  
**Type II**: Auditor tests your controls over a period (typically 6-12 months)

Type II is more valuable as it proves controls operate effectively over time.

### How long does SOC 2 take?

**Timeline**:
- Planning: 1-2 months
- Implementation: 3-6 months
- Observation period: 6-12 months
- Audit: 2-3 months

**Total**: 12-21 months from start to SOC 2 Type II report

**Shortcut**: You can get Type I in 4-6 months, then upgrade to Type II after the observation period.

### How much does SOC 2 cost?

**Total First-Year Costs**:
- Small org: $140K-300K
- Medium org: $320K-660K
- Large org: $700K-1.6M

**Breakdown**:
- AWS services: $5K-580K/year
- External audit: $15K-150K
- Internal staff: $50K-500K
- Tools and training: $10K-160K

See [Cost Estimation Guide](cost-estimation.md) for details.

### Which Trust Service Categories should I choose?

**Always Required**:
- Common Criteria (Security) - Required for all SOC 2 audits

**Choose based on your commitments**:
- **Availability**: If you promise uptime (e.g., 99.9% SLA)
- **Confidentiality**: If you handle confidential data
- **Processing Integrity**: If processing accuracy is critical (financial, healthcare)
- **Privacy**: If you handle personal information (most should include this)

**Most Common**: Security + Availability + Confidentiality

---

## AWS-Specific Questions

### Can I host SOC 2 compliant systems entirely on AWS?

**Yes**. AWS infrastructure is SOC 2 certified. You can leverage AWS's compliance for physical and environmental controls while implementing logical controls yourself.

### Does AWS's SOC 2 certification cover me?

**No**. AWS's SOC 2 covers their infrastructure (data centers, hardware, etc.). You're responsible for:
- Your application security
- Access controls
- Data encryption
- Logging and monitoring
- Backup and recovery
- Your organizational controls

This is the "Shared Responsibility Model."

### What's the AWS Shared Responsibility Model?

**AWS is responsible for**:
- Physical security of data centers
- Hardware and infrastructure
- Network infrastructure
- Virtualization layer

**You are responsible for**:
- OS and application security
- Data encryption
- Access management (IAM)
- Network configuration (VPC, security groups)
- Data backup
- Application code security

### Which AWS services are "in scope" for SOC 2?

Common in-scope services:
- Compute: EC2, Lambda, ECS
- Storage: S3, EBS, EFS
- Database: RDS, DynamoDB
- Networking: VPC, CloudFront, Route 53
- Security: IAM, KMS, CloudTrail, GuardDuty

All services you use that store, process, or transmit customer data should be in scope.

### Do I need AWS Support for SOC 2?

Not required, but **Business or Enterprise Support** is recommended for:
- 24/7 support for critical issues
- Faster response times
- Technical Account Manager (Enterprise)
- Demonstrating vendor support for availability controls

**Cost**: Business (>$100/month), Enterprise ($15K+/month)

---

## Implementation Questions

### Where do I start with SOC 2?

**Phase 1 (Week 1-2)**: Foundation
1. Enable CloudTrail (all regions)
2. Enable AWS Config
3. Enable MFA for all users
4. Set IAM password policy
5. Document basic policies

**Phase 2 (Week 3-4)**: Security
1. Enable encryption (S3, EBS, RDS)
2. Configure VPC security
3. Enable GuardDuty
4. Deploy AWS WAF
5. Set up AWS Backup

See [Implementation Guide](implementation-guide.md) for full roadmap.

### What's the minimum AWS spend for SOC 2?

**Minimum Essential**: $300-500/month ($3,600-6,000/year)
- CloudTrail, Config, CloudWatch (logging)
- IAM (free)
- AWS Backup
- Basic encryption (KMS)

**Realistic Minimum**: $500-1,000/month ($6,000-12,000/year)
- Above + GuardDuty, Security Hub, WAF

### Can I use AWS free tier for SOC 2?

**Partially**. Free tier includes:
- CloudTrail: 1 free trail
- Some Config rules
- Some CloudWatch metrics
- IAM (always free)

However, SOC 2 requirements typically exceed free tier limits. Budget $500+/month.

### Should I use a GRC platform?

**Yes, if**:
- ✅ You lack dedicated compliance staff
- ✅ You want faster time-to-certification
- ✅ You need continuous monitoring
- ✅ Platform cost < staff time savings

**Popular platforms**: Vanta, Drata, Secureframe, Tugboat Logic  
**Cost**: $1,000-4,000/month  
**Savings**: 50-70% reduction in staff time

### Do I need a consultant?

**Not required**, but helpful if:
- First SOC 2 audit
- No in-house compliance expertise
- Complex architecture
- Tight timeline

**Cost**: $150-400/hour or $20K-100K for full engagement

---

## Technical Questions

### How do I enable MFA for all AWS users?

```bash
# 1. Set password policy requiring MFA
aws iam update-account-password-policy --require-symbols --require-numbers

# 2. Create IAM policy denying actions without MFA
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "Action": "*",
    "Resource": "*",
    "Condition": {
      "BoolIfExists": {"aws:MultiFactorAuthPresent": "false"}
    }
  }]
}

# 3. Attach policy to all users/groups
aws iam attach-user-policy --user-name john --policy-arn arn:aws:iam::aws:policy/RequireMFA
```

### How do I encrypt all my data?

**At Rest**:
```bash
# Enable default EBS encryption
aws ec2 enable-ebs-encryption-by-default

# Enable S3 default encryption
aws s3api put-bucket-encryption --bucket my-bucket \
  --server-side-encryption-configuration '{...}'

# RDS encryption (must enable at creation)
aws rds create-db-instance --storage-encrypted --kms-key-id xxx
```

**In Transit**:
- Use ACM for SSL/TLS certificates
- Configure ALB/CloudFront for HTTPS only
- Enable SSL for RDS connections
- Deny non-HTTPS S3 access via bucket policy

### How do I prove continuous monitoring?

**Enable**:
1. CloudTrail (continuous API logging)
2. Config (continuous configuration monitoring)
3. GuardDuty (continuous threat detection)
4. VPC Flow Logs (continuous network logging)
5. CloudWatch (continuous metrics and alarms)

**Evidence**: Export logs covering the entire audit period (6-12 months).

### How do I test disaster recovery?

**Quarterly DR Test**:
1. Select non-critical resources to test
2. Simulate failure (terminate instance, delete data)
3. Restore from backup
4. Validate restored data
5. Measure RTO (time to restore)
6. Document results with screenshots

**Annual Full DR Drill**:
- Failover to secondary region
- Full system restoration
- Stakeholder involvement
- Post-test report

---

## Audit Questions

### When should I engage an auditor?

**Timeline**:
- **Month 1-2**: Start looking at auditors
- **Month 3-4**: Select auditor, sign engagement letter
- **Month 6**: Readiness assessment (optional)
- **Month 12-18**: Begin official audit

**Tip**: Engage early for guidance on scope and requirements.

### How do I choose an auditor?

**Criteria**:
- ✅ CPA firm licensed in your state
- ✅ Experience with your industry
- ✅ Experience with AWS
- ✅ Reasonable pricing
- ✅ Good communication
- ✅ References from similar companies

**Questions to ask**:
- How many SOC 2 audits have you completed?
- Do you have AWS experience?
- What's your typical timeline?
- What's included in your fee?
- What are common failures you see?

### What evidence do auditors want?

**For each control**, provide:
- Screenshots of configurations
- Exported logs (CloudTrail, Config)
- Compliance reports
- Policy documents
- Meeting minutes
- Training records
- Incident reports
- Test results

See [Evidence Collection Guide](evidence-collection.md) for details.

### What if I fail the audit?

**Minor Issues**:
- Auditor notes observations or deficiencies
- You remediate during audit
- Still receive SOC 2 report with qualifications

**Major Issues**:
- Auditor identifies material weaknesses
- May need to extend audit period
- Implement controls and re-test
- Worst case: Audit terminated (rare)

**Tip**: Do readiness assessment before official audit.

### Can I share my SOC 2 report publicly?

**No**. SOC 2 reports are confidential and should only be shared with:
- Prospective customers (under NDA)
- Current customers
- Business partners who need it
- Regulators (if required)

Do not post publicly on your website.

### How often do I need to be audited?

**Best Practice**:
- **Year 1**: Type I or Type II
- **Year 2+**: Type II annually

**Minimum**:
- Many customers require annual Type II reports
- Some accept biennial audits

**Continuous**: Some organizations pursue continuous compliance programs with quarterly reviews.

---

## Cost Questions

### Can I reduce costs after certification?

**Limited**. Most services are necessary for maintaining compliance.

**Optimization opportunities**:
- Right-size resources (Compute Optimizer)
- Adjust log retention (1 year instead of indefinite)
- Use S3 Intelligent-Tiering
- Enable S3 Bucket Keys (reduce KMS costs)
- Reserved Instances/Savings Plans

**Typical savings**: 10-20%

### Is SOC 2 worth the cost?

**ROI Factors**:

**Revenue**:
- Opens enterprise market
- Increases deal size (2-3x)
- Faster sales cycles (30-50%)
- Higher win rates (20-40%)

**Risk Reduction**:
- Lower insurance premiums (10-30%)
- Reduced breach probability (60-80%)
- Faster incident response

**Example**: Medium org spends $350K/year, gains $500K-2M in new revenue.

**Break-even**: Typically 6-12 months

### Can startups afford SOC 2?

**Yes**, but prioritize:

**Year 1 (Bootstrap)**:
- Minimum AWS services: $5K-12K
- Type I audit: $8K-15K
- Part-time contractor: $30K-60K
- **Total**: $43K-87K

**Year 2 (Type II)**:
- Full AWS services: $10K-20K
- Type II audit: $15K-30K
- Part-time staff: $50K-100K
- **Total**: $75K-150K

**When to start**: When first enterprise customer requests it or you're raising Series A.

---

## Privacy Questions

### Do I need the Privacy criteria?

**Yes, if**:
- You collect personal information (names, emails, addresses)
- You're subject to GDPR, CCPA, or other privacy laws
- Customers are asking about data privacy

**Most companies should include Privacy**.

### What's the difference between SOC 2 Privacy and GDPR?

**SOC 2 Privacy**:
- Voluntary framework
- Audited by CPA
- North American focus
- Annual audit

**GDPR**:
- Legal requirement (EU)
- Enforced by regulators
- Heavy penalties for non-compliance
- Continuous compliance

SOC 2 Privacy helps demonstrate GDPR compliance but doesn't guarantee it.

### How do I handle data deletion requests?

**Implementation**:
1. Create deletion API/workflow
2. Delete from all data stores (DynamoDB, S3, RDS)
3. Delete from backups (or crypto-erase)
4. Delete from logs (where possible)
5. Notify third parties
6. Send confirmation
7. Log deletion

**AWS Services**: Lambda, Step Functions, DynamoDB, S3

**SLA**: 30 days (GDPR), 45 days (CCPA)

---

## Miscellaneous Questions

### Can I use multiple AWS accounts?

**Yes**, and it's recommended. Use AWS Organizations for:
- Production account
- Development account
- Security tooling account
- Shared services account

**Benefits**:
- Improved security (blast radius)
- Better cost allocation
- Easier compliance scoping

### What about multi-region deployments?

**Considerations**:
- Increases availability (good for A controls)
- Increases costs (data transfer, duplicate resources)
- Increases complexity (evidence collection)
- May be required for DR

**Recommendation**: Primary region + backup region for critical data.

### Do I need to monitor my AWS bill?

**Yes**. Controls require:
- Budget monitoring (AWS Budgets)
- Cost anomaly detection
- Regular cost reviews
- Finance oversight

**Implementation**:
- Set up AWS Budgets with alerts
- Review Cost Explorer monthly
- Tag all resources for cost allocation

### What happens if AWS has an outage?

**Your Responsibility**:
- Design for multi-AZ (within region)
- Implement failover procedures
- Have runbooks ready
- Communicate with customers

**AWS Responsibility**:
- Restore service
- Provide post-incident report

**For Audit**: Document the incident, your response, and lessons learned.

---

## Next Steps

Still have questions?

1. Review the detailed guides in this repository
2. Consult with a SOC 2 auditor
3. Join SOC 2 communities (Reddit r/BusinessIntelligence, GRC Slack groups)
4. Consider hiring a compliance consultant
5. Check AWS documentation and whitepapers

[← Back to Glossary](glossary.md) | [Back to Main Guide](../README.md)
