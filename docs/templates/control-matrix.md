# Control Matrix Template

A comprehensive mapping of SOC 2 controls to AWS services, implementations, and evidence.

## How to Use This Template

1. **Review Controls**: Identify which controls apply to your organization
2. **Map Services**: Document which AWS services address each control
3. **Document Implementation**: Describe how you've implemented the control
4. **List Evidence**: Note what evidence demonstrates control effectiveness
5. **Assign Owners**: Identify who is responsible for each control
6. **Track Status**: Monitor implementation progress

---

## Control Matrix

### Common Criteria (CC)

#### CC1 - Control Environment

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **CC1.1** | Commitment to Integrity and Ethical Values | AWS Organizations, IAM, CloudTrail | - Code of conduct created<br>- SCPs implemented<br>- CloudTrail enabled | - Code of conduct doc<br>- Employee acknowledgments<br>- SCP configurations<br>- CloudTrail logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC1.2** | Board Independence and Oversight | CloudWatch, Security Hub, Cost Explorer | - Executive dashboards created<br>- Quarterly security reviews | - Board meeting minutes<br>- Dashboard screenshots<br>- Review documentation | Leadership | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC1.3** | Organizational Structure | AWS Organizations, IAM, IAM Identity Center | - Org chart created<br>- Multi-account structure<br>- IAM roles defined | - Org chart<br>- AWS account structure<br>- IAM role definitions | HR / IT | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC1.4** | Commitment to Competence | AWS Training | - Training program defined<br>- AWS certifications tracked | - Job descriptions<br>- Training records<br>- Certifications | HR | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC1.5** | Accountability | CloudTrail, Config, IAM | - CloudTrail per-user logging<br>- Quarterly access reviews<br>- Performance metrics | - Access review logs<br>- Performance reviews<br>- CloudTrail logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

#### CC6 - Logical and Physical Access Controls

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **CC6.1** | Authentication | IAM, IAM Identity Center, Cognito, Secrets Manager, KMS | - MFA enforced for all users<br>- Password policy set<br>- Secrets Manager for credentials | - IAM credential report<br>- Password policy config<br>- MFA device list | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.2** | User Provisioning | IAM, CloudTrail | - Provisioning procedure documented<br>- Approval workflow | - Access request forms<br>- CloudTrail user creation events | IT / HR | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.3** | User Access Removal | IAM Access Analyzer, CloudTrail, Config | - Quarterly access reviews<br>- Immediate termination process | - Access review reports<br>- Deprovisioning logs | IT / HR | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.4** | Credential Restriction | Secrets Manager, Systems Manager Parameter Store | - No hardcoded credentials<br>- Secrets Manager for all secrets | - Secrets Manager logs<br>- Code review results | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.5** | Credential Lifecycle | IAM, Secrets Manager | - Credential rotation policy<br>- Automated rotation where possible | - Rotation logs<br>- Policy documentation | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.6** | Network Security | VPC, Security Groups, Network ACLs, WAF, Shield, Network Firewall | - VPC with private subnets<br>- Security groups (least privilege)<br>- WAF deployed | - VPC architecture diagram<br>- Security group configs<br>- VPC Flow Logs | Network Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.7** | Data Transmission Security | ACM, CloudFront, ALB, VPN | - HTTPS enforced everywhere<br>- TLS 1.2+ only | - ACM certificates<br>- ALB listener configs<br>- S3 HTTPS policies | Network Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC6.8** | Physical Access | AWS (inherited) | - Review AWS SOC 2 report<br>- Document reliance | - AWS SOC 2 report from Artifact<br>- Vendor management doc | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

#### CC7 - System Operations

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **CC7.1** | Incident Detection | GuardDuty, Security Hub, Detective, CloudWatch, EventBridge | - GuardDuty enabled<br>- Incident response plan | - GuardDuty findings<br>- Incident response plan<br>- Incident logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC7.2** | Logging and Monitoring | CloudTrail, CloudWatch, Config, VPC Flow Logs | - CloudTrail in all regions<br>- 1+ year log retention<br>- CloudWatch dashboards | - CloudTrail config<br>- Log retention policies<br>- Sample logs | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC7.3** | Incident Response | Systems Manager Incident Manager, Lambda, SNS, Step Functions | - Incident response procedures<br>- Automated response where possible | - Incident response plan<br>- Incident logs<br>- Post-incident reviews | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC7.4** | Backup and Recovery | AWS Backup, S3, RDS, EBS Snapshots, DLM | - AWS Backup policies<br>- Quarterly restore tests | - Backup configurations<br>- Backup success logs<br>- Restore test reports | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **CC7.5** | Business Continuity | Elastic Disaster Recovery, Route 53, Multi-AZ, Auto Scaling, CloudFormation | - Multi-AZ architecture<br>- DR plan documented<br>- Annual DR test | - DR plan<br>- Architecture diagrams<br>- DR test results | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

#### CC8 - Change Management

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **CC8.1** | Change Authorization and Testing | Systems Manager Change Manager, CodePipeline, CloudFormation, Config | - Change approval workflow<br>- Infrastructure as Code<br>- Automated testing | - Change request records<br>- Config timeline<br>- Test results | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

### Availability (A)

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **A1.1** | Capacity Management | CloudWatch, Auto Scaling, Compute Optimizer, Trusted Advisor | - Capacity monitoring<br>- Auto Scaling configured<br>- Monthly capacity reviews | - CloudWatch dashboards<br>- Auto Scaling configs<br>- Capacity review notes | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **A1.2** | Environmental Monitoring | AWS Health Dashboard, CloudWatch, Systems Manager | - CloudWatch monitoring<br>- Health notifications configured | - CloudWatch configs<br>- Health notifications<br>- AWS SOC 2 report | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **A1.3** | Backup and Restoration | AWS Backup, S3, RDS, EBS, DLM | - RPO/RTO defined<br>- Backup policies configured<br>- Quarterly restore tests | - RPO/RTO documentation<br>- Backup configurations<br>- Restore test results | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **A2.1** | Performance Monitoring | CloudWatch, X-Ray, Synthetics, RUM | - Performance baselines defined<br>- Monitoring configured<br>- Alerting set up | - Performance dashboards<br>- X-Ray traces<br>- Synthetics results | Operations | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

### Confidentiality (C)

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **C1.1** | Data Classification | Macie, Tags, IAM | - Data classification framework<br>- Tagging strategy<br>- Macie scanning | - Classification policy<br>- Tag compliance reports<br>- Macie findings | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **C1.2** | Data Disposal | S3 Lifecycle, DynamoDB TTL, KMS | - Retention policies defined<br>- Automated deletion configured<br>- Cryptographic erasure | - Retention policy<br>- Lifecycle configs<br>- Deletion logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **C1.3** | Confidential Information Access | IAM, IAM Access Analyzer, CloudTrail | - Least privilege access<br>- Quarterly access reviews<br>- MFA for sensitive data | - IAM policies<br>- Access review logs<br>- CloudTrail logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **C2.1** | Encryption at Rest | KMS, S3, EBS, RDS, DynamoDB, EFS | - All data encrypted<br>- KMS keys with rotation<br>- Config rules enforcing | - KMS key inventory<br>- Encryption settings<br>- Config compliance reports | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **C2.2** | Encryption in Transit | ACM, CloudFront, ALB, VPN, Direct Connect | - TLS 1.2+ enforced<br>- HTTPS only<br>- VPN for remote access | - ACM certificates<br>- TLS configurations<br>- S3 HTTPS policies | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **C3.1** | Network Segmentation | VPC, Security Groups, Network ACLs, Network Firewall, PrivateLink | - Multi-tier VPC<br>- Least privilege security groups<br>- VPC Flow Logs enabled | - VPC architecture<br>- Security group configs<br>- VPC Flow Logs | Network Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

### Processing Integrity (PI)

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **PI1.1** | Input Validation | API Gateway, WAF, Lambda, EventBridge, SQS | - API Gateway validation<br>- WAF rules<br>- Lambda validation functions | - Validation configs<br>- WAF rules<br>- Validation code | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **PI1.2** | Processing Completeness | Step Functions, SQS, DynamoDB, RDS, X-Ray | - Transaction management<br>- Idempotent processing<br>- Error handling | - Step Functions workflows<br>- Success rate metrics<br>- Reconciliation reports | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **PI1.3** | Authorization | API Gateway, Cognito, IAM, Lambda | - API authorization<br>- User authentication<br>- Service-to-service auth | - Authorization configs<br>- Cognito settings<br>- IAM policies | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **PI1.4** | Timeliness | Lambda, SQS, Kinesis, Batch, EventBridge | - SLA definitions<br>- Processing monitoring<br>- Alerting on delays | - SLA documentation<br>- Processing metrics<br>- Alert configurations | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **PI1.5** | Error Handling | CloudWatch, X-Ray, SQS DLQ, SNS, OpsCenter | - Error detection<br>- DLQ configured<br>- Notification on errors | - Error handling procedures<br>- DLQ configurations<br>- Error resolution logs | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **PI2.1** | Output Quality | Lambda, Glue, Athena, S3 | - Output validation<br>- Data quality checks<br>- Reconciliation | - Validation code<br>- Quality rules<br>- Reconciliation reports | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

### Privacy (P)

| Control ID | Control Description | AWS Services | Implementation | Evidence | Owner | Status |
|-----------|-------------------|--------------|----------------|----------|-------|--------|
| **P1.1** | Privacy Notice | S3, CloudFront, Cognito, SES | - Privacy policy created<br>- Consent collection<br>- Change notifications | - Privacy policy<br>- Consent records<br>- Notification logs | Legal / Security | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P1.2** | Privacy Choices | Cognito, DynamoDB, Lambda, API Gateway | - Preference management<br>- Granular consent<br>- Easy opt-out | - Preference system<br>- Preference logs<br>- Opt-out procedures | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P2.1** | Informed Consent | Cognito, Lambda, DynamoDB, CloudTrail | - Consent at collection<br>- Consent storage<br>- Withdrawal mechanism | - Consent UI<br>- Consent records<br>- Withdrawal logs | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P3.1** | Collection Limited to Purpose | Lambda, API Gateway, WAF, Macie, Config | - Data minimization<br>- Purpose documentation<br>- Validation | - Data collection matrix<br>- Validation code<br>- Macie scans | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P4.1** | Use Limited to Purpose | IAM, DynamoDB, CloudTrail, Macie | - Purpose-based access<br>- Data tagging<br>- Usage monitoring | - IAM policies<br>- Tagging strategy<br>- Access logs | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P4.2** | Retention and Disposal | DynamoDB TTL, S3 Lifecycle, KMS, Lambda, EventBridge | - Retention policy<br>- Automated deletion<br>- Cryptographic erasure | - Retention policy<br>- Lifecycle configs<br>- Deletion logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P5.1** | Data Access | API Gateway, Lambda, S3, Cognito | - Data export API<br>- Within SLA (30-45 days)<br>- Secure delivery | - DSAR procedures<br>- Request logs<br>- Response times | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P5.2** | Data Correction/Deletion | API Gateway, Lambda, Step Functions, DynamoDB | - Update API<br>- Deletion workflow<br>- Confirmation process | - Update procedures<br>- Deletion workflow<br>- Completion logs | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P6.1** | Third-Party Disclosure | CloudTrail, S3, Lambda, DynamoDB | - Third-party inventory<br>- Consent verification<br>- Disclosure logging | - Third-party list<br>- DPAs<br>- Disclosure logs | Legal / Security | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P8.1** | Data Accuracy | Lambda, API Gateway, Glue | - Input validation<br>- Data quality checks<br>- User updates allowed | - Validation rules<br>- Quality metrics<br>- Update procedures | Development | ☐ Not Started<br>☐ In Progress<br>☐ Complete |
| **P9.1** | Monitoring and Enforcement | Config, CloudWatch, Security Hub, Macie | - Compliance monitoring<br>- Privacy metrics<br>- Regular audits | - Config reports<br>- Privacy dashboards<br>- Audit logs | Security Team | ☐ Not Started<br>☐ In Progress<br>☐ Complete |

---

## Summary Statistics

**Total Controls**: [Count]  
**Completed**: [Count] ([Percentage]%)  
**In Progress**: [Count] ([Percentage]%)  
**Not Started**: [Count] ([Percentage]%)

---

## Notes

- Update this matrix as you implement controls
- Review monthly during implementation
- Use for audit preparation
- Share with auditors as evidence of control coverage

[← Back to Main Guide](../README.md)
