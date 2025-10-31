# SOC 2 to AWS Services Mapping Guide

> A comprehensive, practical guide for mapping SOC 2 Trust Service Criteria to AWS services, complete with implementation guidance, architecture diagrams, and evidence collection strategies.

## 🎯 Purpose

This guide helps organizations preparing for SOC 2 Type II certification understand exactly which AWS services address each SOC 2 control, how to implement them, and what evidence auditors will require.

**Perfect for:**
- Startups preparing for their first SOC 2 audit
- Cloud architects designing compliant systems
- GRC professionals transitioning to cloud compliance
- Security teams implementing AWS controls

## 📊 What's Included

### 1. Complete Control Mapping
- All SOC 2 Trust Service Criteria (CC1-CC9, plus A1, C1)
- Mapped to specific AWS services
- Implementation difficulty ratings
- Cost estimates
- Evidence collection guidance

### 2. Architecture Diagrams
- Reference architectures for common scenarios
- Visual representation of control implementation
- Secure data flow patterns
- Multi-account strategies

### 3. Implementation Checklists
- Step-by-step AWS configuration guides
- Security baseline requirements
- Evidence collection procedures
- Audit preparation checklist


## 🗂️ Repository Structure
```

├── checklists/
│   ├── aws-initial-setup.md
│   ├── evidence-collection-guide.md
│   └── pre-audit-checklist.md
├── diagrams/
│   ├── CI/CD-pipeline-with-security-controls.png
│   ├── logging-monitoring-and-incident-response.png
|   ├── multi-account-organization-structure.png
|   ├── secure-data-storage-and-encryption.png
│   └── soc2-compliant-web-app.png
└── mappings/
    ├── SOC2-AWS-Master-Mapping.xlsx     # Main mapping spreadsheet
    ├── SOC2-AWS-Master-Mapping.pdf      # PDF version for easy sharing
    └── control-details/                 # Detailed guides per control family
        ├── CC1-control-environment.md
        ├── CC3-risk-assessment.md
        ├── CC6-logical-access.md
        ├── CC7-system-operations.md
        └── CC8-change-management.md

```

## 🚀 Quick Start

1. **Download the Master Mapping**: [SOC2-AWS-Master-Mapping.xlsx](mappings/SOC2-AWS-Master-Mapping.xlsx)
2. **Review Architecture Diagrams**: Start with the [compliant web application architecture](diagrams/soc2-compliant-web-app.png)
3. **Follow Implementation Checklists**: Begin with [AWS Initial Setup](checklists/aws-initial-setup.md)

## 📋 SOC 2 Coverage Summary

| Trust Service Category | Controls Covered | AWS Services Used |
|------------------------|------------------|-------------------|
| CC1: Control Environment | 5 controls | Organizations, IAM, CloudTrail |
| CC2: Communication | 3 controls | Security Hub, SNS, CloudWatch |
| CC3: Risk Assessment | 4 controls | GuardDuty, Inspector, Config |
| CC4: Monitoring | 3 controls | CloudWatch, EventBridge, Security Hub |
| CC5: Control Activities | 4 controls | Config, Systems Manager, Backup |
| CC6: Logical Access | 8 controls | IAM, Cognito, Secrets Manager |
| CC7: System Operations | 5 controls | CloudWatch, Systems Manager, Backup |
| CC8: Change Management | 3 controls | CodePipeline, Config, CloudTrail |
| CC9: Risk Mitigation | 4 controls | WAF, Shield, GuardDuty, Macie |
| A1: Availability | 7 controls | Auto Scaling, Route 53, CloudFront |
| C1: Confidentiality | 5 controls | KMS, S3 Encryption, VPC |

**Total: 51 controls mapped** ✅

## 💰 AWS Cost Considerations

Most AWS services required for SOC 2 compliance fit within free tier or cost < $50/month for small organizations:

- **Free Tier Services**: IAM, CloudTrail (1 trail), VPC, Organizations
- **Low Cost (< $10/month)**: Config, CloudWatch, SNS
- **Moderate Cost ($10-30/month)**: GuardDuty, Security Hub
- **Variable Cost**: Depends on usage (S3, EC2, RDS)

*Detailed cost breakdown available in the master mapping spreadsheet*

## 📚 How to Use This Guide

### For First-Time SOC 2 Preparation
1. Start with [AWS Initial Setup Checklist](checklists/aws-initial-setup.md)
2. Review the [Master Mapping Spreadsheet](mappings/SOC2-AWS-Master-Mapping.xlsx)
3. Implement controls prioritized by risk level (High → Medium → Low)
4. Use the [Evidence Collection Guide](checklists/evidence-collection-guide.md) throughout

### For Audit Preparation
1. Use the [Pre-Audit Checklist](checklists/pre-audit-checklist.md)
2. Reference control-specific guides in `mappings/control-details/`
3. Collect evidence using examples from `examples/screenshots/`
4. Share relevant architecture diagrams with your auditor

### For Architecture Design
1. Review reference diagrams in `diagrams/`
2. Adapt patterns to your specific use case
3. Ensure all required AWS services are included
4. Document deviations and compensating controls

## 🏗️ Reference Architectures

### 1. SOC 2 Compliant Web Application

![SOC 2 Compliant Web Application Architecture](https://github.com/user-attachments/assets/0b77fb5b-139f-44cf-adfc-ab36384bcc5c) />



**This architecture demonstrates:**
- **High Availability** (Multi-AZ, Auto Scaling) - Addresses A1.x controls
- **Data Encryption** (KMS, TLS) - Addresses C1.x controls  
- **Access Controls** (Security Groups, IAM) - Addresses CC6.x controls
- **Monitoring & Logging** (CloudTrail, CloudWatch, Config) - Addresses CC7.x controls
- **Threat Detection** (GuardDuty, Security Hub) - Addresses CC9.x controls

**Estimated Monthly Cost**: $150-300 for small production workload

**[Download High-Res PDF](diagrams/soc2-compliant-web-app.pdf)** | **[Edit Source File](diagrams/source-files/soc2-compliant-web-app.drawio)**

---

## 🤝 Contributing

This is a living document! Contributions are welcome:

- **Found an error?** Open an issue
- **Have a better implementation?** Submit a pull request
- **Want to add a diagram?** Share your architecture
- **Have audit experience?** Share lessons learned

## 📖 Related Resources

- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)
- [AICPA SOC 2 Trust Service Criteria](https://us.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report)
- [AWS Compliance Programs](https://aws.amazon.com/compliance/programs/)
- [AWS Security Hub Standards](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards.html)

## 📝 Disclaimer

This guide provides general information and should not be considered legal or compliance advice. Each organization's SOC 2 requirements may vary based on their specific Trust Service Criteria selection and auditor requirements. Always consult with qualified compliance professionals and auditors.

## 📬 Connect

- **Author**: Jake Boyd
- **[LinkedIn](www.linkedin.com/in/boydjake)**
- **[Medium](https://medium.com/@jakeboyd)**
- **Questions?** Open an issue or reach out!

## 📄 License

MIT License - Feel free to use this in your organization or adapt for other frameworks

---

⭐ **Star this repo** if you find it helpful!  
🔔 **Watch** for updates as we add more controls and diagrams  
🍴 **Fork** to customize for your organization's needs
