# SOC 2 to AWS Services Mapping Guide
<img width="1753" height="872" alt="image" src="https://github.com/user-attachments/assets/181c8988-329c-4c83-ad18-2d660396c2d5" />


> A comprehensive, practical guide for mapping SOC 2 Trust Service Criteria to AWS services, complete with implementation guidance, architecture diagrams, and evidence collection strategies.

##  Purpose

This guide helps organizations preparing for SOC 2 Type II certification understand exactly which AWS services address each SOC 2 control, how to implement them, and what evidence auditors will require.

**Perfect for:**
- Startups preparing for their first SOC 2 audit
- Cloud architects designing compliant systems
- GRC professionals transitioning to cloud compliance
- Security teams implementing AWS controls

## What's Included

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


## Repository Structure
```

├── checklists/
│   ├── aws-initial-setup.md             # Step-by-step AWS account hardening
│   ├── evidence-collection-guide.md     # What to screenshot, when, and how
│   └── pre-audit-checklist.md           # Final verification before audit
├── diagrams/
│   ├── CI/CD-pipeline-with-security-controls.png     # Secure deployment process
│   ├── logging-monitoring-and-incident-response.png  # The entire observability stack
|   ├── multi-account-organization-structure.png      # How to organize AWS accounts for compliance
|   ├── secure-data-storage-and-encryption.png        # How data is encrypted everywhere
│   └── soc2-compliant-web-app.png                    # SOC 2 compliant web application architecture
├── mappings/
|   ├── SOC2-AWS-Master-Mapping.xlsx        # Main mapping spreadsheet
|   ├── SOC2-AWS-Master-Mapping.pdf         # PDF version for easy sharing
|   └── control-details/                 
|       ├── A1-availability.md           # Ensures system uptime, capacity, and recovery
|       ├── C1-confidentiality.md        # Protects data from unauthorized access
|       ├── CC1-control-environment.md   # Establishes governance and accountability
|       ├── CC2-communication.md         # Ensures timely, accurate information sharing
|       ├── CC3-risk-assessment.md       # Identifies and analyzes system risks
|       ├── CC4-monitoring.md            # Evaluates control performance and issues
|       ├── CC5-control-activities.md    # Implements policies to mitigate risks
|       ├── CC6-logical-access.md        # Restricts access to authorized users
|       ├── CC7-system-operations.md     # Monitors and responds to system events
|       ├── CC8-change-management.md     # Controls and reviews system changes
|       └── CC9-risk-mitigation.md       # Reduces impact of disruptions and threats
├── resources/
|   ├── AICPA Trust Service Criteria 2017.pdf    # Trust Services Criteria 
|   └── aws.md                                   # AWS resources
├── LICENSE       # MIT license
└── README.md     # You are here


```

## Quick Start

1. **Download the Master Mapping**: [SOC2-AWS-Master-Mapping.xlsx](mappings/SOC2-AWS-Master-Mapping.xlsx)
2. **Review Architecture Diagrams**: Start with the [compliant web application architecture](diagrams/soc2-compliant-web-app.png)
3. **Follow Implementation Checklists**: Begin with [AWS Initial Setup](checklists/aws-initial-setup.md)

## SOC 2 Coverage Summary

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

**Total: 51 controls mapped** 

## AWS Cost Considerations

Most AWS services required for SOC 2 compliance fit within free tier or cost < $50/month for small organizations:

- **Free Tier Services**: IAM, CloudTrail (1 trail), VPC, Organizations
- **Low Cost (< $10/month)**: Config, CloudWatch, SNS
- **Moderate Cost ($10-30/month)**: GuardDuty, Security Hub
- **Variable Cost**: Depends on usage (S3, EC2, RDS)

*Detailed cost breakdown available in the master mapping spreadsheet*

##  How to Use This Guide

### For First-Time SOC 2 Preparation
1. Start with [AWS Initial Setup Checklist](checklists/aws-initial-setup.md)
2. Review the [Master Mapping Spreadsheet](mappings/SOC2-AWS-Master-Mapping.csv)
3. Implement controls prioritized by risk level (High → Medium → Low)
4. Use the [Evidence Collection Guide](checklists/evidence-collection-guide.md) throughout

### For Audit Preparation
1. Use the [Pre-Audit Checklist](checklists/pre-audit-checklist.md)
2. Reference control-specific guides in `mappings/control-details/`
3. Share relevant architecture diagrams with your auditor

### For Architecture Design
1. Review reference diagrams in `diagrams/`
2. Adapt patterns to your specific use case
3. Ensure all required AWS services are included
4. Document deviations and compensating controls



---

##  Contributing

This is a living document! Contributions are welcome:

- **Found an error?** Open an issue
- **Have a better implementation?** Submit a pull request
- **Want to add a diagram?** Share your architecture
- **Have audit experience?** Share lessons learned

## Related Resources

- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)
- [AICPA SOC 2 Trust Service Criteria](https://us.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report)
- [AWS Compliance Programs](https://aws.amazon.com/compliance/programs/)
- [AWS Security Hub Standards](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards.html)

## Disclaimer

This guide provides general information and should not be considered legal or compliance advice. Each organization's SOC 2 requirements may vary based on their specific Trust Service Criteria selection and auditor requirements. Always consult with qualified compliance professionals and auditors.

##  Connect

- **Author**: Jake Boyd
- **[LinkedIn](www.linkedin.com/in/boydjake)**
- **[Medium](https://medium.com/@jakeboyd)**
- **Questions?** Open an issue or reach out!

##  License

MIT License - Feel free to use this in your organization or adapt for other frameworks

---

**Star this repo** if you find it helpful!  
**Watch** for updates as we add more controls and diagrams  
 **Fork** to customize for your organization's needs
