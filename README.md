# SOC 2 to AWS Services Mapping Guide

> A comprehensive reference guide mapping SOC 2 Trust Service Criteria to AWS services with practical implementation guidance, evidence collection requirements, and cost estimates.

## 🎯 Purpose

This guide helps organizations preparing for **SOC 2 Type II certification** understand:
- Which AWS services address each SOC 2 control
- How to implement security and compliance controls on AWS
- What evidence auditors require for each control
- Estimated costs for implementing compliance controls

## 📋 Table of Contents

1. [Quick Start](#quick-start)
2. [SOC 2 Trust Service Criteria](#soc-2-trust-service-criteria)
3. [Trust Service Categories](#trust-service-categories)
4. [Implementation Guide](#implementation-guide)
5. [Evidence Collection](#evidence-collection)
6. [Cost Estimation](#cost-estimation)
7. [Reference Architectures](#reference-architectures)
8. [Resources](#resources)

## 🚀 Quick Start

### Understanding SOC 2

SOC 2 (System and Organization Controls 2) is an auditing procedure that ensures service providers securely manage data to protect the interests of their organization and the privacy of their clients.

**SOC 2 Type I**: Audit at a point in time  
**SOC 2 Type II**: Audit over a period (typically 6-12 months)

### Trust Service Categories

SOC 2 is organized into five Trust Service Categories:

| Category | Description | Documentation |
|----------|-------------|---------------|
| **Common Criteria (CC)** | Fundamental controls applicable to all systems | [View Details](docs/common-criteria.md) |
| **Availability** | System is available for operation and use | [View Details](docs/availability.md) |
| **Confidentiality** | Confidential information is protected | [View Details](docs/confidentiality.md) |
| **Processing Integrity** | System processing is complete, valid, accurate, timely, and authorized | [View Details](docs/processing-integrity.md) |
| **Privacy** | Personal information is collected, used, retained, disclosed, and disposed properly | [View Details](docs/privacy.md) |

## 📊 AWS Service Mapping Overview

### Core AWS Services for SOC 2 Compliance

| AWS Service | Primary Use Case | Controls Addressed |
|-------------|------------------|-------------------|
| **AWS IAM** | Identity & Access Management | CC6.1, CC6.2, CC6.3 |
| **AWS CloudTrail** | Audit logging | CC7.2, CC7.3 |
| **AWS Config** | Configuration monitoring | CC7.2, CC8.1 |
| **AWS GuardDuty** | Threat detection | CC7.2, CC7.3 |
| **Amazon VPC** | Network isolation | CC6.6, CC6.7 |
| **AWS KMS** | Encryption key management | CC6.1, CC6.7 |
| **AWS CloudWatch** | Monitoring & alerting | CC7.2, A1.2 |
| **AWS Backup** | Data backup | A1.2, A1.3 |
| **AWS WAF** | Web application firewall | CC6.6, CC6.7 |
| **AWS Systems Manager** | Change management | CC8.1 |

[View Complete Service Mapping →](docs/aws-service-mapping.md)

## 📖 Implementation Guide

### Step-by-Step Implementation

1. **Assessment Phase** (Weeks 1-2)
   - Review your current AWS architecture
   - Identify applicable Trust Service Criteria
   - Gap analysis against SOC 2 requirements

2. **Planning Phase** (Weeks 3-4)
   - Design control implementation
   - Define policies and procedures
   - Establish monitoring and logging

3. **Implementation Phase** (Weeks 5-12)
   - Deploy AWS services per control requirements
   - Configure security controls
   - Implement logging and monitoring

4. **Evidence Collection Phase** (6-12 months)
   - Collect evidence continuously
   - Document control effectiveness
   - Prepare for audit

[View Detailed Implementation Guide →](docs/implementation-guide.md)

## 📁 Evidence Collection

Auditors require specific evidence for each control. Common evidence types:

- **Screenshots** of AWS console configurations
- **AWS Config** compliance reports
- **CloudTrail logs** demonstrating access controls
- **IAM policies** and permission configurations
- **CloudWatch** alerts and monitoring dashboards
- **Backup** and disaster recovery test results
- **Incident response** documentation
- **Change management** records from Systems Manager

[View Evidence Collection Guide →](docs/evidence-collection.md)

## 💰 Cost Estimation

### Estimated Monthly AWS Costs for SOC 2 Compliance

**Small Organization** (1-10 employees, single application):
- Basic compliance stack: $150-300/month
- Enhanced monitoring: +$100-200/month

**Medium Organization** (11-100 employees, multiple applications):
- Comprehensive compliance: $500-1,500/month
- Advanced security: +$500-1,000/month

**Enterprise Organization** (100+ employees, complex infrastructure):
- Full compliance suite: $2,000-5,000+/month
- Enterprise features: +$2,000-5,000+/month

[View Detailed Cost Breakdown →](docs/cost-estimation.md)

## 🏗️ Reference Architectures

Pre-built architecture examples for common scenarios:

- [SaaS Application Architecture](docs/architectures/saas-application.md)
- [Multi-Tenant Platform](docs/architectures/multi-tenant.md)
- [Data Processing Pipeline](docs/architectures/data-pipeline.md)
- [API-First Architecture](docs/architectures/api-first.md)

## 📚 Resources

### Official Documentation
- [AICPA Trust Service Criteria](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/trustservicesprinciples.html)
- [AWS Compliance Programs](https://aws.amazon.com/compliance/programs/)
- [AWS Security Best Practices](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html)

### Tools & Templates
- [Control Matrix Template](docs/templates/control-matrix.md)
- [Evidence Collection Checklist](docs/templates/evidence-checklist.md)
- [Risk Assessment Template](docs/templates/risk-assessment.md)

### Additional Guides
- [Glossary](docs/glossary.md)
- [FAQ](docs/faq.md)
- [Quick Reference](docs/quick-reference.md)

## 🤝 Contributing

This is a portfolio project by Jake Boyd. Suggestions and improvements are welcome via issues or pull requests.

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## ⚠️ Disclaimer

This guide provides general information and should not be considered as professional audit advice. Always consult with qualified SOC 2 auditors and compliance professionals for your specific situation.

---

**Author**: Jake Boyd  
**Last Updated**: October 2025  
**Version**: 1.0.0
