# Common Criteria (CC)

The Common Criteria represent the foundational security controls that apply to all SOC 2 audits, regardless of which additional Trust Service Categories are selected.

## Overview

Common Criteria are organized into nine sections focusing on:
- Control Environment (CC1)
- Communication and Information (CC2)
- Risk Assessment (CC3)
- Monitoring Activities (CC4)
- Control Activities (CC5)
- Logical and Physical Access Controls (CC6)
- System Operations (CC7)
- Change Management (CC8)
- Risk Mitigation (CC9)

---

## CC1: Control Environment

### CC1.1 - Organization Demonstrates Commitment to Integrity and Ethical Values

**Control Description**: The entity demonstrates a commitment to integrity and ethical values.

**AWS Services**:
- AWS Organizations (Policy enforcement)
- AWS IAM (Role-based access control)
- AWS CloudTrail (Activity monitoring)

**Implementation**:
1. Create and document code of conduct
2. Implement AWS Organizations SCPs (Service Control Policies)
3. Enable CloudTrail for all accounts
4. Regular ethics and security training

**Evidence Required**:
- Code of conduct documentation
- Employee acknowledgment records
- Training completion records
- SCP configurations

**Cost**: $0-50/month (CloudTrail storage)

---

### CC1.2 - Board Independence and Oversight

**Control Description**: The board of directors demonstrates independence from management and exercises oversight.

**AWS Services**:
- AWS CloudWatch (Dashboards for board reporting)
- AWS Cost Explorer (Financial oversight)
- AWS Security Hub (Security posture overview)

**Implementation**:
1. Create executive dashboards in CloudWatch
2. Schedule regular security reviews
3. Document oversight meetings
4. Implement separation of duties

**Evidence Required**:
- Board meeting minutes
- Security review documentation
- Audit committee charter
- Dashboard screenshots

**Cost**: $0-100/month (dashboards, Security Hub)

---

### CC1.3 - Management Establishes Structures, Reporting Lines, and Authorities

**Control Description**: Management establishes structures, reporting lines, authorities, and responsibilities.

**AWS Services**:
- AWS Organizations (Account structure)
- AWS IAM (Role hierarchy)
- AWS SSO/Identity Center (Centralized access)

**Implementation**:
1. Design multi-account AWS structure
2. Create organizational chart
3. Define IAM roles per job function
4. Implement AWS SSO for centralized access

**Evidence Required**:
- Organizational chart
- AWS Organizations structure diagram
- IAM role definitions
- Responsibility matrices

**Cost**: $0-50/month (AWS SSO per user)

---

### CC1.4 - Commitment to Competence

**Control Description**: The entity demonstrates a commitment to recruit, develop, and retain competent individuals.

**AWS Services**:
- AWS Training and Certification (Skill development)
- AWS Skill Builder (Training platform)

**Implementation**:
1. Define competency requirements
2. Provide AWS training to staff
3. Track certifications
4. Annual performance reviews

**Evidence Required**:
- Job descriptions
- Training records
- AWS certification tracking
- Performance review documentation

**Cost**: $0-500/month (training subscriptions)

---

### CC1.5 - Accountability for Responsibilities

**Control Description**: The entity holds individuals accountable for their internal control responsibilities.

**AWS Services**:
- AWS CloudTrail (Activity tracking)
- AWS Config (Compliance monitoring)
- AWS IAM (User accountability)

**Implementation**:
1. Enable CloudTrail logging per user
2. Regular access reviews
3. Performance metrics tied to security
4. Incident response assignments

**Evidence Required**:
- Access review logs
- Performance metrics
- Incident response records
- CloudTrail logs

**Cost**: $50-200/month (CloudTrail, Config)

---

## CC2: Communication and Information

### CC2.1 - Internal Communication of Information

**Control Description**: The entity obtains or generates and uses relevant, quality information to support internal control.

**AWS Services**:
- Amazon SNS (Notifications)
- Amazon EventBridge (Event routing)
- AWS Chatbot (Slack/Teams integration)
- AWS CloudWatch (Metrics and logs)

**Implementation**:
1. Set up CloudWatch dashboards
2. Configure SNS topics for alerts
3. Integrate with communication tools
4. Define information flows

**Evidence Required**:
- Communication flow diagrams
- SNS topic configurations
- Dashboard screenshots
- Information security policy

**Cost**: $10-100/month (SNS, EventBridge)

---

### CC2.2 - External Communication of Information

**Control Description**: The entity communicates with external parties regarding matters affecting internal control.

**AWS Services**:
- Amazon SES (Email communication)
- AWS Artifact (Compliance reports)
- AWS Status Page (Service status)

**Implementation**:
1. Define external communication procedures
2. Set up automated compliance reporting
3. Create incident notification process
4. Maintain customer communication channels

**Evidence Required**:
- Communication policies
- Customer notification templates
- Incident communication logs
- AWS Artifact access logs

**Cost**: $10-50/month (SES)

---

### CC2.3 - Internal Communication Objectives and Responsibilities

**Control Description**: The entity communicates with internal stakeholders the objectives and responsibilities for internal control.

**AWS Services**:
- AWS Security Hub (Central security view)
- AWS Systems Manager (Policy distribution)
- Amazon WorkDocs (Documentation)

**Implementation**:
1. Document control objectives
2. Distribute security policies via SSM
3. Regular team communications
4. Security awareness training

**Evidence Required**:
- Policy distribution logs
- Training materials
- Communication records
- Acknowledgment tracking

**Cost**: $20-100/month (Security Hub, WorkDocs)

---

## CC3: Risk Assessment

### CC3.1 - Specification of Objectives

**Control Description**: The entity specifies objectives with sufficient clarity to enable identification and assessment of risks.

**AWS Services**:
- AWS Well-Architected Tool (Framework assessment)
- AWS Trusted Advisor (Best practice checks)

**Implementation**:
1. Define security and compliance objectives
2. Use Well-Architected Framework
3. Regular risk assessments
4. Document objectives clearly

**Evidence Required**:
- Documented objectives
- Well-Architected reviews
- Risk assessment reports
- Objective alignment matrix

**Cost**: $0 (included services)

---

### CC3.2 - Identification and Analysis of Risk

**Control Description**: The entity identifies risks to the achievement of its objectives and analyzes risks as a basis for determining how they should be managed.

**AWS Services**:
- AWS Security Hub (Finding aggregation)
- Amazon GuardDuty (Threat detection)
- AWS Inspector (Vulnerability assessment)
- AWS Trusted Advisor (Risk identification)

**Implementation**:
1. Enable Security Hub across accounts
2. Activate GuardDuty
3. Run Inspector assessments
4. Quarterly risk reviews

**Evidence Required**:
- Risk register
- Security Hub findings
- GuardDuty alerts
- Risk treatment plans

**Cost**: $100-500/month (GuardDuty, Inspector, Security Hub)

---

### CC3.3 - Assessment of Fraud Risk

**Control Description**: The entity considers the potential for fraud in assessing risks.

**AWS Services**:
- AWS CloudTrail (Audit logging)
- Amazon GuardDuty (Anomaly detection)
- AWS IAM Access Analyzer (Permission analysis)
- AWS Detective (Investigation)

**Implementation**:
1. Enable comprehensive logging
2. Configure fraud detection rules
3. Regular permission reviews
4. Incident response procedures

**Evidence Required**:
- Fraud risk assessment
- CloudTrail logs
- GuardDuty findings
- Investigation reports

**Cost**: $50-300/month (GuardDuty, Detective)

---

### CC3.4 - Identification and Assessment of Changes

**Control Description**: The entity identifies and assesses changes that could significantly impact internal control.

**AWS Services**:
- AWS Config (Configuration tracking)
- AWS Systems Manager Change Manager (Change tracking)
- AWS CloudFormation (Infrastructure as Code)
- AWS Service Catalog (Approved services)

**Implementation**:
1. Enable AWS Config rules
2. Track all infrastructure changes
3. Implement change approval process
4. Regular change reviews

**Evidence Required**:
- Change logs
- AWS Config timeline
- Approval records
- Impact assessments

**Cost**: $50-200/month (Config, Change Manager)

---

## CC4: Monitoring Activities

### CC4.1 - Ongoing and Periodic Evaluations

**Control Description**: The entity selects, develops, and performs ongoing and/or separate evaluations to ascertain whether components of internal control are present and functioning.

**AWS Services**:
- AWS Config (Continuous compliance)
- AWS Security Hub (Security standards)
- AWS Audit Manager (Audit automation)
- AWS CloudWatch (Continuous monitoring)

**Implementation**:
1. Enable AWS Config continuous monitoring
2. Implement Security Hub standards (CIS, PCI-DSS)
3. Set up Audit Manager assessments
4. Schedule periodic reviews

**Evidence Required**:
- Config compliance reports
- Security Hub scores
- Audit Manager assessments
- Review meeting minutes

**Cost**: $100-400/month (Config, Security Hub, Audit Manager)

---

### CC4.2 - Evaluation and Communication of Deficiencies

**Control Description**: The entity evaluates and communicates internal control deficiencies in a timely manner.

**AWS Services**:
- AWS Security Hub (Finding management)
- Amazon SNS (Alert notifications)
- AWS Systems Manager OpsCenter (Incident tracking)
- AWS Service Catalog (Remediation automation)

**Implementation**:
1. Define deficiency escalation process
2. Configure automated alerting
3. Track findings to resolution
4. Regular deficiency reviews

**Evidence Required**:
- Deficiency logs
- Remediation records
- Communication logs
- Resolution timelines

**Cost**: $20-100/month (SNS, OpsCenter)

---

## CC5: Control Activities

### CC5.1 - Selection and Development of Control Activities

**Control Description**: The entity selects and develops control activities that contribute to the mitigation of risks.

**AWS Services**:
- AWS Config Rules (Automated controls)
- AWS Lambda (Custom controls)
- AWS Security Hub (Control frameworks)
- AWS Systems Manager (Automation)

**Implementation**:
1. Map controls to risks
2. Implement AWS Config rules
3. Create custom control automation
4. Regular effectiveness testing

**Evidence Required**:
- Control mapping matrix
- Config rule configurations
- Testing results
- Effectiveness reports

**Cost**: $50-300/month (Config, Lambda execution)

---

### CC5.2 - Selection and Development of Technology Controls

**Control Description**: The entity selects and develops general control activities over technology.

**AWS Services**:
- AWS Systems Manager (Patch management)
- AWS Security Hub (Security controls)
- AWS WAF (Application protection)
- AWS Shield (DDoS protection)

**Implementation**:
1. Enable automated patching
2. Implement WAF rules
3. Configure Shield protection
4. Regular vulnerability scanning

**Evidence Required**:
- Patch compliance reports
- WAF rule configurations
- Security scanning results
- Control test results

**Cost**: $100-500/month (WAF, Shield, Systems Manager)

---

### CC5.3 - Deployment of Control Activities Through Policies

**Control Description**: The entity deploys control activities through policies and procedures.

**AWS Services**:
- AWS Organizations (Service Control Policies)
- AWS IAM (Permission policies)
- AWS Systems Manager (Policy distribution)
- AWS Config (Policy enforcement)

**Implementation**:
1. Document all policies and procedures
2. Implement SCPs
3. Deploy IAM policies
4. Enforce via Config rules

**Evidence Required**:
- Policy documentation
- SCP configurations
- IAM policies
- Deployment records

**Cost**: $50-150/month (Systems Manager, Config)

---

## CC6: Logical and Physical Access Controls

### CC6.1 - Logical Access - Authentication

**Control Description**: The entity implements logical access security software, infrastructure, and architectures to support authorized access and segregation of duties.

**AWS Services**:
- AWS IAM (Identity management)
- AWS IAM Identity Center (SSO)
- Amazon Cognito (User authentication)
- AWS Secrets Manager (Credential management)
- AWS KMS (Key management)

**Implementation**:
1. Enforce MFA for all users
2. Implement least privilege access
3. Use IAM roles instead of long-term credentials
4. Rotate credentials regularly
5. Enable password policies

**Evidence Required**:
- IAM user list with MFA status
- IAM policies
- Password policy configuration
- Secrets rotation logs

**Cost**: $50-200/month (Secrets Manager, SSO)

---

### CC6.2 - Prior to Issuing System Credentials

**Control Description**: Prior to issuing system credentials and granting access, the entity registers and authorizes new users.

**AWS Services**:
- AWS IAM (User provisioning)
- AWS IAM Identity Center (Centralized onboarding)
- AWS CloudTrail (Access logging)

**Implementation**:
1. Document user provisioning process
2. Implement approval workflow
3. Use IAM Identity Center for onboarding
4. Log all access grants

**Evidence Required**:
- User provisioning procedures
- Approval records
- CloudTrail logs of user creation
- Access request forms

**Cost**: $10-50/month (CloudTrail storage)

---

### CC6.3 - User Access Removal and Modification

**Control Description**: The entity removes system access when appropriate and reviews access on a regular basis.

**AWS Services**:
- AWS IAM Access Analyzer (Permission review)
- AWS CloudTrail (Access tracking)
- AWS Config (Access monitoring)
- AWS Lambda (Automated deprovisioning)

**Implementation**:
1. Quarterly access reviews
2. Automated inactive user detection
3. Immediate termination deprovisioning
4. Document all access changes

**Evidence Required**:
- Access review reports
- User deprovisioning logs
- CloudTrail evidence
- Review sign-offs

**Cost**: $50-150/month (Config, Access Analyzer)

---

### CC6.4 - Credentials Restricted to Authorized Users

**Control Description**: System credentials are restricted to authorized users.

**AWS Services**:
- AWS Secrets Manager (Credential management)
- AWS Systems Manager Parameter Store (Secure storage)
- AWS IAM (Access control)

**Implementation**:
1. Store all credentials in Secrets Manager
2. No hardcoded credentials
3. Least privilege access to secrets
4. Audit secret access

**Evidence Required**:
- Secrets Manager audit logs
- IAM policies for secret access
- Code review results
- Secret usage reports

**Cost**: $20-100/month (Secrets Manager)

---

### CC6.5 - Credential Lifecycle Management

**Control Description**: The entity identifies, authenticates, and authorizes users based on risk.

**AWS Services**:
- AWS IAM (Identity lifecycle)
- AWS IAM Identity Center (Centralized management)
- AWS CloudTrail (Authentication logging)

**Implementation**:
1. Risk-based authentication
2. Regular credential rotation
3. Session timeout policies
4. Conditional access policies

**Evidence Required**:
- Credential rotation logs
- IAM policy configurations
- Authentication logs
- Risk assessment documentation

**Cost**: $30-100/month (IAM Identity Center)

---

### CC6.6 - Network Security

**Control Description**: The entity implements controls to protect against unauthorized access through network communications.

**AWS Services**:
- Amazon VPC (Network isolation)
- AWS Network Firewall (Network protection)
- AWS WAF (Application firewall)
- AWS Shield (DDoS protection)
- Security Groups (Instance firewalls)
- Network ACLs (Subnet firewalls)

**Implementation**:
1. Implement VPC with private subnets
2. Configure security groups (least privilege)
3. Enable VPC Flow Logs
4. Deploy Network Firewall for inspection
5. Implement WAF for web applications

**Evidence Required**:
- VPC architecture diagram
- Security group configurations
- Network ACL rules
- VPC Flow Logs
- WAF rule sets

**Cost**: $200-1,000/month (Network Firewall, WAF, Flow Logs)

---

### CC6.7 - Data Transmission Security

**Control Description**: The entity protects information during transmission.

**AWS Services**:
- AWS Certificate Manager (SSL/TLS certificates)
- AWS VPN (Encrypted connections)
- AWS Direct Connect (Private connections)
- Amazon CloudFront (HTTPS delivery)
- Elastic Load Balancing (SSL termination)

**Implementation**:
1. Enforce HTTPS/TLS for all communications
2. Use ACM for certificate management
3. Enable encryption in transit for all services
4. VPN for remote access

**Evidence Required**:
- TLS/SSL configurations
- ACM certificate inventory
- CloudFront configurations
- ELB listener configurations

**Cost**: $50-300/month (VPN, CloudFront, certificates are free)

---

### CC6.8 - Physical Access Controls

**Control Description**: The entity restricts physical access to facilities and protected information assets.

**AWS Services**:
- AWS (inherits AWS data center controls)
- AWS Outposts (for on-premises deployments)

**Implementation**:
1. Review AWS SOC 2 reports (via AWS Artifact)
2. Document reliance on AWS physical security
3. Implement controls for any on-premises equipment
4. Badge access systems for offices

**Evidence Required**:
- AWS SOC 2 reports from Artifact
- Office access control documentation
- Visitor logs (if applicable)
- Equipment inventory

**Cost**: $0 (AWS responsibility)

---

## CC7: System Operations

### CC7.1 - Detection of Incidents

**Control Description**: The entity identifies and responds to potential and actual security incidents.

**AWS Services**:
- Amazon GuardDuty (Threat detection)
- AWS Security Hub (Finding aggregation)
- Amazon Detective (Investigation)
- AWS CloudWatch (Monitoring)
- Amazon EventBridge (Event response)

**Implementation**:
1. Enable GuardDuty across all accounts
2. Configure Security Hub
3. Set up automated alerting
4. Document incident response procedures

**Evidence Required**:
- GuardDuty findings
- Security Hub alerts
- Incident response playbooks
- Incident logs

**Cost**: $100-500/month (GuardDuty, Detective)

---

### CC7.2 - Logging and Monitoring

**Control Description**: The entity monitors system components and operations for anomalies.

**AWS Services**:
- AWS CloudTrail (API logging)
- AWS CloudWatch (Metrics and logs)
- Amazon CloudWatch Logs Insights (Log analysis)
- AWS Config (Configuration logging)
- VPC Flow Logs (Network logging)

**Implementation**:
1. Enable CloudTrail in all regions
2. Centralize logs to S3
3. Set up CloudWatch dashboards
4. Configure log retention (1+ year)
5. Enable VPC Flow Logs

**Evidence Required**:
- CloudTrail configurations
- Log retention policies
- CloudWatch dashboards
- Sample log exports

**Cost**: $100-500/month (CloudTrail, CloudWatch, log storage)

---

### CC7.3 - Security Incident Response

**Control Description**: The entity responds to identified security incidents.

**AWS Services**:
- AWS Systems Manager Incident Manager (Incident coordination)
- AWS Lambda (Automated response)
- Amazon SNS (Notifications)
- AWS Step Functions (Response orchestration)

**Implementation**:
1. Document incident response plan
2. Configure automated responses
3. Set up escalation procedures
4. Regular incident drills

**Evidence Required**:
- Incident response plan
- Incident logs and timelines
- Response automation configurations
- Post-incident reviews

**Cost**: $50-200/month (Incident Manager, SNS)

---

### CC7.4 - Backup and Recovery

**Control Description**: The entity performs backup and restoration procedures.

**AWS Services**:
- AWS Backup (Centralized backup)
- Amazon S3 (Backup storage)
- AWS RDS Automated Backups
- Amazon EBS Snapshots
- AWS DLM (Data Lifecycle Manager)

**Implementation**:
1. Configure AWS Backup policies
2. Define RPO/RTO requirements
3. Regular restore testing
4. Cross-region backup replication

**Evidence Required**:
- Backup policy configurations
- Backup success logs
- Restore test results
- RPO/RTO documentation

**Cost**: $100-1,000/month (backup storage and data transfer)

---

### CC7.5 - Continuity of Operations

**Control Description**: The entity implements business continuity and disaster recovery procedures.

**AWS Services**:
- AWS Elastic Disaster Recovery (DRS)
- Route 53 (DNS failover)
- Multi-AZ deployments
- Auto Scaling
- CloudFormation (Infrastructure as Code)

**Implementation**:
1. Design multi-AZ architecture
2. Implement automated failover
3. Document DR procedures
4. Annual DR testing

**Evidence Required**:
- DR plan documentation
- Architecture diagrams
- DR test results
- RTO/RPO metrics

**Cost**: $200-2,000/month (DRS, multi-AZ resources)

---

## CC8: Change Management

### CC8.1 - Infrastructure and Software Changes

**Control Description**: The entity authorizes, designs, develops, tests, approves, and implements changes to infrastructure and software.

**AWS Services**:
- AWS Systems Manager Change Manager (Change tracking)
- AWS CodePipeline (CI/CD)
- AWS CloudFormation (Infrastructure as Code)
- AWS CodeCommit (Version control)
- AWS CodeBuild (Build automation)

**Implementation**:
1. Implement change approval workflow
2. Use Infrastructure as Code
3. Automated testing in pipelines
4. Document all changes

**Evidence Required**:
- Change management procedures
- Change request records
- Approval documentation
- Deployment logs

**Cost**: $50-300/month (Change Manager, CodePipeline)

---

## CC9: Risk Mitigation

### CC9.1 - Risk Mitigation Activities

**Control Description**: The entity identifies, selects, and develops risk mitigation activities.

**AWS Services**:
- AWS Security Hub (Risk visibility)
- AWS Trusted Advisor (Best practices)
- AWS Well-Architected Tool (Risk assessment)

**Implementation**:
1. Regular risk assessments
2. Prioritize findings by severity
3. Implement compensating controls
4. Document risk acceptance

**Evidence Required**:
- Risk register
- Mitigation plans
- Security Hub findings
- Risk acceptance documentation

**Cost**: $50-200/month (Security Hub)

---

### CC9.2 - Vendor and Business Partner Management

**Control Description**: The entity assesses and manages risks associated with vendors and business partners.

**AWS Services**:
- AWS Artifact (Compliance documentation)
- AWS Service Catalog (Approved services)

**Implementation**:
1. Vendor risk assessment process
2. Review AWS third-party assessments
3. Due diligence documentation
4. Regular vendor reviews

**Evidence Required**:
- Vendor risk assessments
- AWS compliance reports
- Vendor contracts with security terms
- Review documentation

**Cost**: $0-50/month (Service Catalog)

---

## Quick Reference: Common Criteria Controls

| Control | Description | Primary AWS Services | Monthly Cost Est. |
|---------|-------------|---------------------|-------------------|
| CC1 | Control Environment | IAM, Organizations, CloudTrail | $50-200 |
| CC2 | Communication | SNS, EventBridge, SES | $20-150 |
| CC3 | Risk Assessment | Security Hub, GuardDuty, Inspector | $100-500 |
| CC4 | Monitoring | Config, Security Hub, Audit Manager | $100-400 |
| CC5 | Control Activities | Config Rules, WAF, Systems Manager | $150-800 |
| CC6 | Access Controls | IAM, VPC, Network Firewall, KMS | $300-1,500 |
| CC7 | System Operations | CloudTrail, CloudWatch, GuardDuty, Backup | $400-2,000 |
| CC8 | Change Management | Change Manager, CodePipeline, CloudFormation | $50-300 |
| CC9 | Risk Mitigation | Security Hub, Trusted Advisor | $50-200 |

**Total Estimated Cost for Common Criteria**: $1,220-6,050/month

---

## Next Steps

1. Review applicable controls for your organization
2. Implement controls systematically (start with CC6 and CC7)
3. Begin evidence collection immediately
4. Schedule periodic control testing
5. Prepare for audit by organizing evidence

[← Back to Main Guide](../README.md) | [View Availability Controls →](availability.md)
