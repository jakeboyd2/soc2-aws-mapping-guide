# CC1: Control Environment

## Control Family Overview

CC1 establishes the foundation for all other controls by addressing the organization's commitment to integrity, ethical values, and competence. This control family focuses on governance, organizational structure, accountability, and the overall "tone at the top" that influences security and compliance culture.

## Control Description

The entity demonstrates a commitment to integrity and ethical values, exercises oversight responsibility, establishes structure and authority, demonstrates commitment to competence, and enforces accountability. Key requirements include:

- **Integrity and Ethics**: Documented policies and demonstrated leadership commitment
- **Board Oversight**: Board or equivalent oversight of security and compliance
- **Organizational Structure**: Clear reporting lines and segregation of duties
- **Competence**: Hiring practices, training, and professional development
- **Accountability**: Performance measures, consequences for violations

## AWS Services That Address CC1

### Primary Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS Organizations** | Governance structure | Multi-account strategy, SCPs, centralized management |
| **AWS Control Tower** | Governance automation | Landing zone, guardrails, account factory |
| **AWS IAM Identity Center** | Identity governance | Centralized access, permission sets, audit trails |
| **AWS Audit Manager** | Compliance documentation | Evidence collection, audit-ready reports, frameworks |
| **AWS CloudTrail** | Accountability tracking | User activity logs, API audit trail |

### Supporting Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS Config** | Policy enforcement | Configuration compliance, organizational rules |
| **AWS Service Catalog** | Standardized deployments | Pre-approved services, governance controls |
| **AWS Resource Access Manager** | Resource sharing governance | Cross-account sharing with controls |
| **AWS License Manager** | Software compliance | License tracking, usage monitoring |
| **AWS Trusted Advisor** | Best practice checks | Security, cost, performance recommendations |
| **AWS Systems Manager** | Operational governance | Automation documents, compliance reporting |

## Implementation Steps

### Phase 1: Governance Framework (Week 1-2)

#### 1. Establish Multi-Account AWS Organization

**Why Multi-Account Architecture**:
- **Isolation**: Separate production from development/testing
- **Security boundaries**: Limit blast radius of security incidents
- **Billing separation**: Clear cost attribution
- **Compliance**: Easier to demonstrate segregation of duties
- **Governance**: Apply different policies to different account types

**Recommended Account Structure**:
```
Root (Management Account)
├── Security OU
│   ├── Security Tooling Account (GuardDuty, Security Hub)
│   ├── Log Archive Account (centralized logs)
│   └── Audit Account (compliance tools, read-only access)
├── Infrastructure OU
│   ├── Network Account (Transit Gateway, VPC sharing)
│   ├── Shared Services Account (Active Directory, DNS)
│   └── Backup Account (centralized backups)
├── Production OU
│   ├── Production-App1 Account
│   ├── Production-App2 Account
│   └── Production-Data Account
├── Non-Production OU
│   ├── Development Account
│   ├── Staging Account
│   └── Testing Account
└── Sandbox OU
    ├── Developer Sandbox 1
    └── Developer Sandbox 2
```

**Implementation**:
```bash
# Create AWS Organization
aws organizations create-organization --feature-set ALL

# Create Organizational Units
aws organizations create-organizational-unit \
  --parent-id r-xxxx \
  --name Security

aws organizations create-organizational-unit \
  --parent-id r-xxxx \
  --name Production

aws organizations create-organizational-unit \
  --parent-id r-xxxx \
  --name Non-Production

# Create accounts
aws organizations create-account \
  --email security-tooling@company.com \
  --account-name "Security Tooling" \
  --role-name OrganizationAccountAccessRole

aws organizations create-account \
  --email log-archive@company.com \
  --account-name "Log Archive" \
  --role-name OrganizationAccountAccessRole
```

#### 2. Deploy AWS Control Tower

**Control Tower Benefits**:
- Automated landing zone setup
- Pre-configured guardrails (preventive and detective)
- Account factory for standardized account provisioning
- Dashboard for governance visibility
- Drift detection and remediation

**Setup Process**:
```bash
# Control Tower must be set up via AWS Console
# 1. Navigate to AWS Control Tower
# 2. Set up landing zone with:
#    - Log Archive account email
#    - Audit account email
#    - Home region
#    - Additional regions for governance

# After setup, configure guardrails via CLI
aws controltower enable-control \
  --target-identifier "arn:aws:organizations::ACCOUNT:ou/o-xxxx/ou-xxxx" \
  --control-identifier "arn:aws:controltower:REGION::control/AWS-GR_ENCRYPTED_VOLUMES"

# Enable mandatory guardrails
guardrails=(
  "AWS-GR_ENCRYPTED_VOLUMES"              # Require encrypted EBS volumes
  "AWS-GR_EBS_OPTIMIZED_INSTANCE"         # Require EBS-optimized instances
  "AWS-GR_RDS_INSTANCE_PUBLIC_ACCESS_CHECK"  # Disallow public RDS
  "AWS-GR_RDS_STORAGE_ENCRYPTED"          # Require encrypted RDS
  "AWS-GR_S3_BUCKET_PUBLIC_READ_PROHIBITED"  # Disallow public S3 read
  "AWS-GR_S3_BUCKET_PUBLIC_WRITE_PROHIBITED" # Disallow public S3 write
  "AWS-GR_IAM_USER_MFA_ENABLED"           # Require MFA
  "AWS-GR_ROOT_ACCOUNT_MFA_ENABLED"       # Require root MFA
  "AWS-GR_CLOUDTRAIL_ENABLED"             # Require CloudTrail
  "AWS-GR_CLOUDWATCH_ALARM_ACTION_CHECK"  # Require alarm actions
)

for guardrail in "${guardrails[@]}"; do
  aws controltower enable-control \
    --target-identifier "arn:aws:organizations::ACCOUNT:ou/o-xxxx/ou-prod" \
    --control-identifier "arn:aws:controltower:us-east-1::control/$guardrail"
done
```

#### 3. Implement Service Control Policies (SCPs)

**SCP Strategy**:
- Deny-by-default for sensitive operations
- Allow list for approved services
- Regional restrictions
- Protection of security services

**Base Protection SCP** (applies to all accounts):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyLeavingOrganization",
      "Effect": "Deny",
      "Action": [
        "organizations:LeaveOrganization"
      ],
      "Resource": "*"
    },
    {
      "Sid": "ProtectCloudTrail",
      "Effect": "Deny",
      "Action": [
        "cloudtrail:DeleteTrail",
        "cloudtrail:StopLogging",
        "cloudtrail:UpdateTrail"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotLike": {
          "aws:PrincipalArn": "arn:aws:iam::*:role/OrganizationAccountAccessRole"
        }
      }
    },
    {
      "Sid": "ProtectSecurityServices",
      "Effect": "Deny",
      "Action": [
        "guardduty:DeleteDetector",
        "guardduty:DisassociateFromMasterAccount",
        "guardduty:StopMonitoringMembers",
        "securityhub:DeleteInvitations",
        "securityhub:DisableSecurityHub",
        "securityhub:DisassociateFromMasterAccount",
        "config:DeleteConfigurationRecorder",
        "config:DeleteDeliveryChannel",
        "config:StopConfigurationRecorder"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DenyRootUserActions",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringLike": {
          "aws:PrincipalArn": "arn:aws:iam::*:root"
        }
      }
    },
    {
      "Sid": "RequireSpecificRegions",
      "Effect": "Deny",
      "NotAction": [
        "cloudfront:*",
        "iam:*",
        "route53:*",
        "support:*",
        "organizations:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": [
            "us-east-1",
            "us-west-2",
            "eu-west-1"
          ]
        }
      }
    }
  ]
}
```

**Production-Specific SCP**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyPublicS3Buckets",
      "Effect": "Deny",
      "Action": [
        "s3:PutBucketPublicAccessBlock"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "s3:BlockPublicAcls": "false"
        }
      }
    },
    {
      "Sid": "RequireEncryption",
      "Effect": "Deny",
      "Action": [
        "ec2:RunInstances"
      ],
      "Resource": "arn:aws:ec2:*:*:volume/*",
      "Condition": {
        "Bool": {
          "ec2:Encrypted": "false"
        }
      }
    },
    {
      "Sid": "RequireTagsOnResources",
      "Effect": "Deny",
      "Action": [
        "ec2:RunInstances",
        "rds:CreateDBInstance",
        "s3:CreateBucket"
      ],
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true",
          "aws:RequestTag/Owner": "true",
          "aws:RequestTag/CostCenter": "true"
        }
      }
    },
    {
      "Sid": "DenyConsoleEC2Creation",
      "Effect": "Deny",
      "Action": [
        "ec2:RunInstances"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "ec2:LaunchedByService": [
            "autoscaling.amazonaws.com",
            "ecs.amazonaws.com",
            "eks.amazonaws.com"
          ]
        },
        "StringNotLike": {
          "aws:PrincipalArn": [
            "arn:aws:iam::*:role/CICD-*",
            "arn:aws:iam::*:role/OrganizationAccountAccessRole"
          ]
        }
      }
    }
  ]
}
```

**Apply SCPs**:
```bash
# Create and attach base protection SCP
aws organizations create-policy \
  --content file://base-protection-scp.json \
  --description "Base security protections for all accounts" \
  --name BaseProtection \
  --type SERVICE_CONTROL_POLICY

aws organizations attach-policy \
  --policy-id p-xxxx \
  --target-id r-xxxx  # Attach to root

# Create and attach production SCP
aws organizations create-policy \
  --content file://production-scp.json \
  --description "Additional protections for production accounts" \
  --name ProductionControls \
  --type SERVICE_CONTROL_POLICY

aws organizations attach-policy \
  --policy-id p-yyyy \
  --target-id ou-xxxx  # Attach to Production OU
```

### Phase 2: Documentation and Policies (Week 3-4)

#### 4. Create Security Policies and Procedures

**Required Policy Documents**:

1. **Information Security Policy** (master document)
2. **Acceptable Use Policy**
3. **Access Control Policy**
4. **Change Management Policy**
5. **Incident Response Policy**
6. **Data Classification Policy**
7. **Business Continuity Policy**
8. **Vendor Management Policy**

**Information Security Policy Template**:
```markdown
# Information Security Policy

**Document Owner**: Chief Information Security Officer (CISO)
**Version**: 2.0
**Effective Date**: 2024-01-01
**Review Frequency**: Annual
**Next Review**: 2025-01-01

## 1. Purpose

This policy establishes the framework for protecting [Company Name]'s 
information assets, including data, systems, and infrastructure hosted 
in Amazon Web Services (AWS).

## 2. Scope

This policy applies to:
- All employees, contractors, and third parties with access to company systems
- All company-owned, operated, or managed information systems
- All data processed, stored, or transmitted by the company
- All AWS accounts and resources within our AWS Organization

## 3. Policy Statements

### 3.1 Information Security Program
[Company Name] maintains an information security program that:
- Aligns with industry standards (SOC 2, ISO 27001, NIST)
- Addresses risks identified through regular assessments
- Is reviewed and updated at least annually
- Has executive sponsorship and adequate resources

### 3.2 Roles and Responsibilities

**Board of Directors / Executive Leadership**:
- Provide oversight of information security program
- Review quarterly security reports
- Approve annual security budget
- Ensure adequate resources for security initiatives

**Chief Information Security Officer (CISO)**:
- Overall responsibility for information security program
- Reports to CTO and quarterly to Board
- Authority to enforce security policies
- Manages security team and budget

**Security Team**:
- Implement and maintain security controls
- Monitor security events and respond to incidents
- Conduct security assessments and audits
- Provide security training and awareness

**System Owners**:
- Responsible for security of their systems
- Implement controls appropriate to data classification
- Report security incidents immediately
- Ensure compliance with security policies

**All Personnel**:
- Comply with security policies and procedures
- Complete required security training
- Report suspected security incidents
- Protect credentials and access rights

### 3.3 AWS Governance

**Account Management**:
- All AWS accounts must be part of the company AWS Organization
- New accounts provisioned only through Control Tower Account Factory
- Account creation requires approval from Security and Finance
- No standalone AWS accounts permitted

**Service Control Policies**:
- SCPs enforce security guardrails across all accounts
- Production accounts subject to additional restrictions
- SCPs cannot be removed without CISO approval
- SCP changes require Change Advisory Board approval

**Identity and Access**:
- All AWS access must use IAM Identity Center (AWS SSO)
- Multi-factor authentication (MFA) required for all users
- No long-term IAM user access keys permitted
- Root account credentials secured and monitored

### 3.4 Compliance Requirements

All systems and processes must comply with:
- SOC 2 Type II requirements
- GDPR (for EU customer data)
- CCPA (for California customer data)
- PCI-DSS (for payment card data)
- Contractual obligations with customers

### 3.5 Security Controls

The company implements security controls across:
- Access control (CC6)
- Logical and physical access (CC6)
- System operations (CC7)
- Change management (CC8)
- Risk assessment (CC3)
- Security monitoring (CC7)
- Communications (CC2)
- Risk mitigation (CC4)
- Vendor management (CC9)

### 3.6 Training and Awareness

**Required Training**:
- Security awareness: All personnel, annually
- AWS security best practices: Technical staff, annually
- Incident response: Security team, quarterly
- Compliance training: Annual for relevant personnel

**Training Tracking**:
- HR maintains training completion records
- Automated reminders for overdue training
- Access restrictions for non-compliant users

### 3.7 Policy Violations

Violations of security policies may result in:
- Temporary or permanent access revocation
- Mandatory additional training
- Formal corrective action
- Termination of employment or contract
- Legal action where appropriate

## 4. Policy Exceptions

Exceptions to this policy:
- Must be documented in writing
- Require CISO approval
- Include compensating controls
- Have expiration dates (maximum 90 days)
- Are reviewed quarterly

## 5. Policy Review and Updates

This policy is:
- Reviewed annually by CISO and legal counsel
- Updated as needed for regulatory changes
- Approved by executive leadership
- Communicated to all personnel upon updates

## 6. Related Documents

- Acceptable Use Policy (AUP-001)
- Access Control Policy (ACP-001)
- Incident Response Plan (IRP-001)
- AWS Security Standards (AWS-STD-001)
- Employee Handbook

## 7. Approval

**Approved by**:
- CEO: __________________ Date: __________
- CISO: _________________ Date: __________
- Legal: ________________ Date: __________

## 8. Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2023-01-01 | CISO | Initial version |
| 2.0 | 2024-01-01 | CISO | Added AWS-specific requirements |
```

#### 5. Implement AWS Audit Manager

**Setup Audit Manager for SOC 2**:
```bash
# Enable Audit Manager
aws auditmanager update-settings \
  --default-assessment-reports-destination-type S3 \
  --default-assessment-reports-destination s3://audit-reports-bucket/

# Create assessment for SOC 2
aws auditmanager create-assessment \
  --name "SOC2-Type2-2024" \
  --description "SOC 2 Type II assessment for 2024 audit period" \
  --assessment-reports-destination destinationType=S3,destination=s3://audit-reports-bucket/soc2-2024 \
  --scope '{
    "awsAccounts": [
      {
        "id": "111111111111",
        "emailAddress": "production@company.com"
      },
      {
        "id": "222222222222",
        "emailAddress": "security@company.com"
      }
    ],
    "awsServices": [
      {"serviceName": "AWS IAM"},
      {"serviceName": "Amazon EC2"},
      {"serviceName": "Amazon RDS"},
      {"serviceName": "Amazon S3"},
      {"serviceName": "AWS CloudTrail"},
      {"serviceName": "Amazon GuardDuty"},
      {"serviceName": "AWS Config"}
    ]
  }' \
  --framework-id "arn:aws:auditmanager:us-east-1::framework/SOC2"

# Set up delegations
aws auditmanager update-assessment \
  --assessment-id assessment-id \
  --roles '[
    {
      "roleType": "PROCESS_OWNER",
      "roleArn": "arn:aws:iam::ACCOUNT:role/SecurityLead"
    },
    {
      "roleType": "RESOURCE_OWNER", 
      "roleArn": "arn:aws:iam::ACCOUNT:role/DevOpsLead"
    }
  ]'
```

**Automated Evidence Collection Configuration**:
```python
import boto3
import json

auditmanager = boto3.client('auditmanager')

def configure_evidence_collection():
    """
    Configure automated evidence collection for controls
    """
    
    # Map controls to data sources
    control_data_sources = {
        'CC6.1': [  # Logical access controls
            'AWS::IAM::Policy',
            'AWS::IAM::User',
            'AWS::IAM::Role'
        ],
        'CC7.2': [  # System monitoring
            'AWS::CloudTrail::Trail',
            'AWS::Config::ConfigurationRecorder',
            'AWS::GuardDuty::Detector'
        ],
        'CC8.1': [  # Change management
            'AWS::CloudFormation::Stack',
            'AWS::Config::ResourceCompliance'
        ]
    }
    
    for control_id, data_sources in control_data_sources.items():
        print(f"Configuring evidence for {control_id}")
        
        # Audit Manager automatically collects evidence from:
        # - AWS Config
        # - CloudTrail
        # - Security Hub
        # - AWS API calls
        
        # Custom evidence can be uploaded
        upload_custom_evidence(control_id)

def upload_custom_evidence(control_id):
    """
    Upload custom evidence (policies, procedures, screenshots)
    """
    # Evidence examples:
    evidence_files = {
        'CC1.1': [
            's3://audit-evidence/policies/information-security-policy.pdf',
            's3://audit-evidence/policies/board-approval.pdf'
        ],
        'CC1.2': [
            's3://audit-evidence/org-charts/security-org-chart.pdf',
            's3://audit-evidence/job-descriptions/ciso-job-description.pdf'
        ],
        'CC1.3': [
            's3://audit-evidence/training/security-training-completion.csv',
            's3://audit-evidence/background-checks/background-check-policy.pdf'
        ]
    }
    
    if control_id in evidence_files:
        for evidence_url in evidence_files[control_id]:
            print(f"Evidence ready: {evidence_url}")
            # Audit Manager can reference S3 objects as evidence

configure_evidence_collection()
```

### Phase 3: Personnel and Training (Week 5-6)

#### 6. Implement Background Check and Onboarding Process

**Background Check Policy**:
```markdown
# Background Check and Screening Policy

## Scope
All employees and contractors with:
- Access to production systems
- Access to customer data
- Administrative privileges
- Remote work arrangements

## Requirements

### Pre-Employment Screening
1. **Criminal background check** (7-year history)
2. **Employment verification** (previous 3 employers)
3. **Education verification** (degrees claimed)
4. **Reference checks** (minimum 2 professional references)
5. **Credit check** (for financial system access roles)

### Ongoing Screening
- Annual re-screening for privileged access roles
- Immediate screening if role changes to higher risk level

### International Considerations
- Country-specific screening where permitted by law
- Alternative verification methods where background checks prohibited

## Process Flow

1. **Conditional Offer Extended**
   - Contingent on background check completion
   - Candidate signs authorization form

2. **Background Check Initiated**
   - Processed by approved third-party vendor
   - Typically completes in 5-7 business days

3. **Results Review**
   - HR and hiring manager review results
   - Adverse findings reviewed by legal team
   - Candidate given opportunity to dispute inaccuracies

4. **Final Determination**
   - Approval or withdrawal of offer
   - Documentation maintained securely

## AWS Implementation

Track background checks in HR system with automated alerts:
```

**Automated Background Check Tracking**:
```python
import boto3
from datetime import datetime, timedelta

dynamodb = boto3.resource('dynamodb')
sns = boto3.client('sns')

def check_background_screening_status():
    """
    Check for employees due for re-screening
    """
    table = dynamodb.Table('EmployeeCompliance')
    
    # Get all employees with privileged access
    response = table.scan(
        FilterExpression='privileged_access = :val',
        ExpressionAttributeValues={':val': True}
    )
    
    today = datetime.now()
    alerts = []
    
    for employee in response['Items']:
        last_screening = datetime.fromisoformat(employee['last_background_check'])
        months_since = (today - last_screening).days / 30
        
        if months_since >= 12:
            alerts.append({
                'employee_id': employee['employee_id'],
                'name': employee['name'],
                'last_screening': last_screening.isoformat(),
                'status': 'OVERDUE'
            })
        elif months_since >= 11:
            alerts.append({
                'employee_id': employee['employee_id'],
                'name': employee['name'],
                'last_screening': last_screening.isoformat(),
                'status': 'DUE_SOON'
            })
    
    # Send alerts
    if alerts:
        sns.publish(
            TopicArn='arn:aws:sns:REGION:ACCOUNT:hr-compliance',
            Subject='Background Check Renewals Required',
            Message=json.dumps(alerts, indent=2)
        )
    
    return alerts
```

#### 7. Security Training Program

**Training Matrix**:

| Role | Training Required | Frequency | Delivery Method |
|------|-------------------|-----------|-----------------|
| All Employees | Security Awareness | Annual | Online, 45 min |
| All Employees | Phishing Awareness | Quarterly | Simulated phishing |
| Technical Staff | AWS Security Best Practices | Annual | Instructor-led, 4 hours |
| Developers | Secure Coding | Annual | Online + lab, 8 hours |
| DevOps/SRE | Infrastructure Security | Annual | Workshop, 8 hours |
| Security Team | Advanced Threat Detection | Quarterly | Varies |
| Managers | Security Leadership | Annual | Workshop, 4 hours |
| New Hires | Security Orientation | Within 7 days | Online, 2 hours |

**Training Tracking System**:
```python
import boto3
from datetime import datetime, timedelta

dynamodb = boto3.resource('dynamodb')
ses = boto3.client('ses')

def track_training_compliance():
    """
    Monitor training completion and send reminders
    """
    table = dynamodb.Table('TrainingCompliance')
    
    # Query all active employees
    response = table.scan(
        FilterExpression='status = :active',
        ExpressionAttributeValues={':active': 'ACTIVE'}
    )
    
    today = datetime.now()
    overdue = []
    upcoming = []
    
    for employee in response['Items']:
        # Check security awareness training
        if 'security_awareness_date' in employee:
            training_date = datetime.fromisoformat(employee['security_awareness_date'])
            days_since = (today - training_date).days
            
            if days_since > 365:
                overdue.append({
                    'employee': employee['email'],
                    'training': 'Security Awareness',
                    'days_overdue': days_since - 365
                })
            elif days_since > 335:  # 30-day warning
                upcoming.append({
                    'employee': employee['email'],
                    'training': 'Security Awareness',
                    'due_date': (training_date + timedelta(days=365)).isoformat()
                })
        else:
            # New hire missing training
            overdue.append({
                'employee': employee['email'],
                'training': 'Security Awareness',
                'days_overdue': (today - datetime.fromisoformat(employee['hire_date'])).days
            })
        
        # Check role-specific training
        if employee['role'] in ['developer', 'devops', 'sre']:
            if 'aws_security_date' not in employee:
                overdue.append({
                    'employee': employee['email'],
                    'training': 'AWS Security Best Practices',
                    'days_overdue': (today - datetime.fromisoformat(employee['hire_date'])).days
                })
    
    # Send notifications
    send_training_reminders(overdue, upcoming)
    
    # Restrict access for overdue training (>30 days)
    for item in overdue:
        if item['days_overdue'] > 30:
            restrict_aws_access(item['employee'])
    
    return {'overdue': len(overdue), 'upcoming': len(upcoming)}

def send_training_reminders(overdue, upcoming):
    """Send email reminders for training"""
    # Overdue notifications
    for item in overdue:
        ses.send_email(
            Source='hr@company.com',
            Destination={'ToAddresses': [item['employee']]},
            Message={
                'Subject': {'Data': f"URGENT: {item['training']} Training Overdue"},
                'Body': {
                    'Text': {'Data': f"""
Your {item['training']} training is {item['days_overdue']} days overdue.

Please complete this training immediately to maintain system access.

Access may be restricted if training is not completed within 5 business days.

Login to training portal: https://training.company.com
                    """}
                }
            }
        )
    
    # Upcoming notifications
    for item in upcoming:
        ses.send_email(
            Source='hr@company.com',
            Destination={'ToAddresses': [item['employee']]},
            Message={
                'Subject': {'Data': f"{item['training']} Training Due Soon"},
                'Body': {
                    'Text': {'Data': f"""
Your {item['training']} training is due on {item['due_date']}.

Please schedule time to complete this training before the due date.

Login to training portal: https://training.company.com
                    """}
                }
            }
        )

def restrict_aws_access(email):
    """Restrict AWS access for non-compliant users"""
    # Implementation depends on your identity provider
    # Example: Remove user from AWS SSO groups
    print(f"Restricting access for {email} due to training non-compliance")
```

**Security Awareness Training Content**:
```markdown
# Security Awareness Training - 2024

## Module 1: Introduction to Information Security (10 min)
- Why security matters
- Company security policy overview
- Your role in security
- Consequences of security incidents

## Module 2: Password and Authentication Security (10 min)
- Strong password requirements
- Multi-factor authentication (MFA)
- Password managers
- Protecting your credentials
- Hands-on: Enable MFA on your accounts

## Module 3: Recognizing Social Engineering (10 min)
- Phishing emails
- Spear phishing
- Vishing (voice phishing)
- Pretexting
- Interactive: Identify phishing emails

## Module 4: Data Protection (5 min)
- Data classification
- Handling sensitive data
- Encryption requirements
- Data sharing guidelines

## Module 5: Cloud Security Basics (5 min)
- AWS security responsibilities
- Proper use of cloud resources
- API key and credential security
- Reporting security concerns

## Module 6: Incident Response (5 min)
- Recognizing security incidents
- Reporting procedures
- What happens after you report
- Hands-on: Practice reporting

## Assessment (10 questions, 80% passing)
- Must complete assessment to finish training
- Unlimited attempts allowed
- Certificate issued upon completion

## Post-Training Requirements
- Acknowledge security policy
- Configure MFA (if not already done)
- Complete security profile in HR system
```

## Evidence Collection Tips

### Required Evidence Types

#### 1. Organizational Structure and Governance

**What Auditors Want**:
- Organizational chart showing security reporting structure
- Board meeting minutes discussing security/compliance
- CISO job description and qualifications
- Security team structure and responsibilities
- Segregation of duties documentation

**How to Collect**:
```markdown
# Organizational Evidence Checklist

## Documentation to Prepare:
1. **Org Chart** (company-org-chart.pdf)
   - Shows CISO reporting to CTO/CEO
   - Security team structure
   - Clear separation between operations and security
   - Updated within last 90 days

2. **Board/Executive Meeting Minutes** (board-minutes-security-review-Q*.pdf)
   - Quarterly security reports to board/executives
   - Discussion of security budget
   - Approval of security initiatives
   - Review of security incidents
   - Minimum: Last 4 quarters

3. **Role Descriptions** (folder: role-descriptions/)
   - CISO role description with qualifications
   - Security team member roles
   - System owner responsibilities
   - Developer security responsibilities
   - All role descriptions include security duties

4. **Segregation of Duties Matrix** (segregation-of-duties-matrix.xlsx)
   | Function | Role A | Role B | Role C | Segregated? |
   |----------|--------|--------|--------|-------------|
   | Code Development | Developer | - | - | - |
   | Code Review | Senior Dev | ✓ | - | ✓ Yes |
   | Production Deployment | DevOps | ✓ | - | ✓ Yes |
   | Production Access Approval | Manager | - | ✓ | ✓ Yes |
   | Security Monitoring | Security Team | - | - | ✓ Yes |
   | Audit/Compliance | Audit Team | - | - | ✓ Yes |

5. **AWS Organization Structure** (aws-organization-structure.json)
   ```bash
   aws organizations describe-organization > aws-org-structure.json
   aws organizations list-organizational-units-for-parent \
     --parent-id r-xxxx > aws-organizational-units.json
   ```

6. **Control Tower Governance** (control-tower-config.json)
   - Landing zone configuration
   - Guardrails enabled
   - Account factory settings
   ```bash
   # Export Control Tower configuration
   aws controltower list-enabled-controls \
     --target-identifier "arn:aws:organizations::ACCOUNT:ou/..." \
     > control-tower-guardrails.json
   ```
```

**AWS Organizations Evidence**:
```bash
# Generate comprehensive org evidence
# 1. Organization structure
aws organizations describe-organization > evidence/org-structure.json

# 2. All accounts with metadata
aws organizations list-accounts > evidence/all-accounts.json

# 3. Organizational units
aws organizations list-roots > evidence/roots.json
ROOT_ID=$(jq -r '.Roots[0].Id' evidence/roots.json)
aws organizations list-organizational-units-for-parent \
  --parent-id $ROOT_ID > evidence/organizational-units.json

# 4. Service Control Policies
aws organizations list-policies \
  --filter SERVICE_CONTROL_POLICY > evidence/scps.json

# For each SCP, get the content
for policy_id in $(jq -r '.Policies[].Id' evidence/scps.json); do
  aws organizations describe-policy \
    --policy-id $policy_id > "evidence/scp-${policy_id}.json"
done

# 5. Policy attachments
for ou in $(jq -r '.OrganizationalUnits[].Id' evidence/organizational-units.json); do
  aws organizations list-policies-for-target \
    --target-id $ou \
    --filter SERVICE_CONTROL_POLICY > "evidence/policies-for-${ou}.json"
done
```

#### 2. Policies and Procedures

**What Auditors Want**:
- Written security policies (approved and dated)
- Evidence of policy communication to employees
- Policy acknowledgment records
- Policy review and update history

**Document Management System**:
```markdown
# Policy Document Management

## Required Metadata for Each Policy:
- Document number (e.g., SEC-POL-001)
- Version number
- Effective date
- Review frequency
- Document owner (role, not person)
- Approval signatures
- Revision history

## Storage Location:
- Master copies: s3://company-policies/security/
- Employee-facing: Company intranet
- Version control: Git repository

## Policy Distribution Process:
1. Policy approved by CISO and Legal
2. Announced via company email
3. Posted to intranet
4. Employees acknowledge via HR system
5. New hires acknowledge during onboarding

## Evidence Files:
- policy-approval-signatures.pdf
- policy-communication-email.pdf
- employee-acknowledgment-report.csv
- policy-review-schedule.xlsx
```

**Policy Acknowledgment Tracking**:
```python
import boto3
from datetime import datetime

dynamodb = boto3.resource('dynamodb')
ses = boto3.client('ses')

def track_policy_acknowledgments(policy_id, policy_version):
    """
    Track which employees have acknowledged updated policies
    """
    table = dynamodb.Table('PolicyAcknowledgments')
    employees_table = dynamodb.Table('Employees')
    
    # Get all active employees
    employees = employees_table.scan(
        FilterExpression='status = :active',
        ExpressionAttributeValues={':active': 'ACTIVE'}
    )['Items']
    
    # Check acknowledgment status
    pending = []
    acknowledged = []
    
    for employee in employees:
        try:
            ack = table.get_item(
                Key={
                    'employee_id': employee['employee_id'],
                    'policy_id': policy_id
                }
            )
            
            if 'Item' in ack and ack['Item']['version'] == policy_version:
                acknowledged.append({
                    'employee': employee['email'],
                    'name': employee['name'],
                    'date': ack['Item']['acknowledgment_date']
                })
            else:
                pending.append({
                    'employee': employee['email'],
                    'name': employee['name']
                })
        except:
            pending.append({
                'employee': employee['email'],
                'name': employee['name']
            })
    
    # Send reminders to pending
    for employee in pending:
        send_policy_acknowledgment_reminder(
            employee['employee'], 
            policy_id,
            policy_version
        )
    
    # Generate compliance report
    report = {
        'policy_id': policy_id,
        'version': policy_version,
        'total_employees': len(employees),
        'acknowledged': len(acknowledged),
        'pending': len(pending),
        'compliance_rate': f"{(len(acknowledged)/len(employees)*100):.1f}%",
        'timestamp': datetime.now().isoformat()
    }
    
    # Store report
    table.put_item(Item={
        'policy_id': f"{policy_id}#REPORT",
        'employee_id': datetime.now().isoformat(),
        'report_data': report
    })
    
    return report

def send_policy_acknowledgment_reminder(email, policy_id, version):
    """Send reminder email to acknowledge policy"""
    ses.send_email(
        Source='hr@company.com',
        Destination={'ToAddresses': [email]},
        Message={
            'Subject': {'Data': f'ACTION REQUIRED: Acknowledge Updated Policy {policy_id}'},
            'Body': {
                'Text': {'Data': f"""
An important company policy has been updated and requires your acknowledgment.

Policy: {policy_id} (Version {version})

Please log in to the employee portal and review and acknowledge this policy:
https://portal.company.com/policies/{policy_id}

Acknowledgment must be completed within 5 business days.

If you have questions, contact hr@company.com.
                """}
            }
        }
    )

# Generate monthly report
report = track_policy_acknowledgments('SEC-POL-001', '2.0')
print(f"Policy acknowledgment compliance: {report['compliance_rate']}")
```

#### 3. Personnel Records

**What Auditors Want**:
- Background check completion records
- Training completion records
- Performance reviews mentioning security responsibilities
- Termination/offboarding records

**Evidence Collection**:
```bash
# Export training compliance report
aws dynamodb scan \
  --table-name TrainingCompliance \
  --filter-expression "attribute_exists(security_awareness_date)" \
  > evidence/training-compliance.json

# Generate summary report
python3 << 'EOF'
import json

with open('evidence/training-compliance.json') as f:
    data = json.load(f)

employees = data['Items']
total = len(employees)
compliant = len([e for e in employees 
                 if is_training_current(e['security_awareness_date']['S'])])

print(f"Training Compliance Report")
print(f"Total Employees: {total}")
print(f"Compliant: {compliant}")
print(f"Non-Compliant: {total - compliant}")
print(f"Compliance Rate: {(compliant/total*100):.1f}%")

def is_training_current(date_str):
    from datetime import datetime, timedelta
    training_date = datetime.fromisoformat(date_str)
    return (datetime.now() - training_date).days < 365
EOF
```

**Background Check Evidence Template**:
```markdown
# Background Check Summary Report - Q1 2024

## Overview
- Total Employees: 150
- Employees Requiring Background Checks: 147 (98%)
- Background Checks Completed: 147 (100% of required)
- Background Checks Current: 145 (98.6%)
- Background Checks Due for Renewal: 2

## Details

### New Hires (Q1 2024): 12
| Employee ID | Name | Hire Date | Check Completed | Check Date | Status |
|-------------|------|-----------|----------------|------------|--------|
| EMP-1234 | [Redacted] | 2024-01-15 | Yes | 2024-01-10 | Clear |
| EMP-1235 | [Redacted] | 2024-02-01 | Yes | 2024-01-28 | Clear |
| ... | ... | ... | ... | ... | ... |

### Annual Renewals Due (Next 90 Days): 8
| Employee ID | Name | Current Check Date | Renewal Due | Status |
|-------------|------|-------------------|-------------|--------|
| EMP-0123 | [Redacted] | 2023-04-15 | 2024-04-15 | Scheduled |
| EMP-0234 | [Redacted] | 2023-05-01 | 2024-05-01 | Scheduled |

### Overdue Renewals: 2
| Employee ID | Name | Last Check | Days Overdue | Action |
|-------------|------|------------|--------------|--------|
| EMP-0345 | [Redacted] | 2023-02-28 | 15 days | Access restricted, renewal in progress |
| EMP-0456 | [Redacted] | 2023-03-10 | 8 days | Reminder sent, scheduled for next week |

## Vendor Information
- Background Check Provider: [Provider Name]
- Contract Number: BGC-2023-001
- Services: Criminal, employment, education verification
- Average Completion Time: 5.2 business days
- SLA: 7 business days

## Compliance Notes
- All employees with production access have current background checks
- No adverse findings requiring escalation this quarter
- 100% compliance with background check policy
- Process audit completed: No findings

---
Report Generated: 2024-03-31
Report Owner: HR Manager
Next Review: 2024-06-30
```

#### 4. Board/Executive Oversight

**What Auditors Want**:
- Evidence of regular security reporting to board/executives
- Documentation of board review and approval
- Security budget approval
- Incident escalation to leadership

**Board Report Template**:
```markdown
# Quarterly Security Report to Board of Directors
## Q1 2024 - Presented March 28, 2024

**Presented by**: Jane Smith, Chief Information Security Officer
**Attendees**: Board members, CEO, CTO, CFO

---

## Executive Summary

The security program remains strong with no material incidents this quarter. 
We successfully completed our SOC 2 Type II audit with zero findings and 
implemented several key improvements to our AWS security posture.

**Key Metrics**:
- Security Incidents: 3 (all low severity, quickly resolved)
- SOC 2 Audit: Passed with 0 findings
- Security Training Completion: 98%
- Mean Time to Detect (MTTD): 12 minutes
- Mean Time to Respond (MTTR): 45 minutes

---

## 1. Security Program Status

### SOC 2 Compliance
- **Status**: ✅ Compliant
- Type II audit completed February 2024
- Report issued March 15, 2024
- Zero findings or exceptions
- Report available for customer requests

### Other Compliance
- GDPR: Compliant (annual assessment completed)
- CCPA: Compliant (privacy policy updated)
- PCI-DSS: N/A (no card processing)

### Security Metrics Dashboard
| Metric | Q1 2024 | Q4 2023 | Trend |
|--------|---------|---------|-------|
| Security Incidents | 3 | 2 | → |
| Mean Time to Detect | 12 min | 15 min | ↑ Improving |
| Mean Time to Respond | 45 min | 62 min | ↑ Improving |
| Vulnerability Patching SLA | 98% | 95% | ↑ |
| Security Training Compliance | 98% | 96% | ↑ |
| Failed Login Attempts | 47 | 52 | ↑ |
| GuardDuty Findings | 12 | 18 | ↑ |

---

## 2. Security Incidents (Q1 2024)

### Incident Summary
- **Total Incidents**: 3
- **Critical/High**: 0
- **Medium**: 1
- **Low**: 2
- **Customer Impact**: None

### Incident INC-2024-003 (Medium Severity)
- **Date**: February 15, 2024
- **Type**: SSH brute force attack detected
- **Impact**: None (attack blocked by security controls)
- **Response**: Automated response blocked source IP within 2 minutes
- **Root Cause**: Security group briefly allowed SSH from internet
- **Remediation**: 
  - Immediately restricted security group
  - Implemented Config rule to prevent public SSH
  - No data accessed or systems compromised

### Other Incidents
- INC-2024-001: Failed phishing simulation (employee clicked, reported correctly)
- INC-2024-002: Unauthorized API call (new employee using old API key, revoked)

---

## 3. Key Accomplishments

### AWS Security Improvements
- ✅ Implemented AWS Control Tower across all accounts
- ✅ Deployed automated security scanning in CI/CD pipeline
- ✅ Enhanced GuardDuty with Kubernetes audit logs
- ✅ Implemented automated incident response playbooks
- ✅ Achieved 100% CloudTrail coverage across all accounts

### Security Program Enhancements
- ✅ Completed penetration test (no high findings)
- ✅ Launched security champion program (15 champions trained)
- ✅ Implemented automated vulnerability scanning
- ✅ Enhanced security awareness training program
- ✅ Documented all security runbooks

---

## 4. Risk Assessment

### Top Security Risks

**1. Third-Party Vendor Risk (Medium)**
- **Description**: Expanding vendor ecosystem increases supply chain risk
- **Mitigation**: Implementing vendor security assessment program
- **Status**: 75% of vendors assessed, completion target Q2 2024

**2. Cloud Misconfigurations (Medium)**
- **Description**: Rapid cloud adoption could lead to misconfigurations
- **Mitigation**: Automated guardrails via SCPs and Config rules
- **Status**: 98% compliance with security policies

**3. Insider Threat (Low)**
- **Description**: Employees with elevated access could cause harm
- **Mitigation**: Least privilege, access reviews, monitoring
- **Status**: Quarterly access reviews completed, no anomalies detected

---

## 5. Upcoming Initiatives (Q2 2024)

### Planned Projects
1. **Zero Trust Architecture** (Q2-Q3)
   - Implement service-to-service authentication
   - Deploy network segmentation enhancements
   - Budget: $150K

2. **Security Automation Expansion** (Q2)
   - Additional automated response playbooks
   - Enhanced threat detection rules
   - Budget: $50K

3. **Enhanced Data Loss Prevention** (Q2-Q3)
   - Deploy Amazon Macie for sensitive data discovery
   - Implement automated data classification
   - Budget: $75K

### Total Security Budget Request (2024)
- **Approved Budget**: $2.5M
- **Q1 Spend**: $580K (23% of annual)
- **Remaining**: $1.92M
- **On Track**: Yes

---

## 6. Regulatory & Industry Updates

### New Requirements
- **GDPR**: Updates to data transfer mechanisms (addressed)
- **SOC 2**: New criteria for supply chain risk (implementing)
- **Cyber Insurance**: Policy renewal Q2 (no issues anticipated)

### Industry Trends
- Increased ransomware targeting cloud environments
- Focus on supply chain security
- Enhanced cloud security posture management (CSPM) requirements

---

## 7. Recommendations for Board Approval

### 1. Security Budget Increase (Q3 2024)
**Request**: Additional $200K for advanced threat detection
**Justification**: Expanding infrastructure requires enhanced monitoring
**ROI**: Reduce MTTD by 50%, prevent potential breaches

### 2. Third-Party Security Assessment Program
**Request**: Approve vendor security assessment policy
**Impact**: All vendors with data access require security review
**Timeline**: Implement Q2 2024

### 3. Cyber Insurance Policy Renewal
**Request**: Renew policy with $5M coverage limit
**Premium**: $125K annually (10% increase from 2023)
**Recommendation**: Approve renewal

---

## 8. Questions & Discussion

### Board Questions:
**Q**: "What is our exposure if a major cloud provider has an outage?"
**A**: Multi-region architecture provides resilience; RTO: 4 hours, RPO: 15 minutes

**Q**: "How do we compare to industry benchmarks?"
**A**: MTTD and MTTR in top quartile; incident rate below industry average

**Q**: "What's our biggest security concern?"
**A**: Supply chain risk as we grow vendor relationships; mitigation program underway

---

## Board Actions

**Motion**: Approve Q2 security initiatives and budget allocation
**Moved by**: Director Smith
**Seconded by**: Director Jones
**Vote**: Unanimous approval

**Motion**: Approve cyber insurance renewal
**Moved by**: Director Johnson  
**Seconded by**: Director Williams
**Vote**: Unanimous approval

---

**Next Security Report**: June 27, 2024 (Q2 2024)

**Signatures**:
- CISO: Jane Smith, March 28, 2024
- Board Chair: [Name], March 28, 2024

**Distribution**: Board members, CEO, CTO, CFO, General Counsel
**Classification**: Board Confidential
```

#### 5. AWS Governance Evidence

**What Auditors Want**:
- Service Control Policies (SCPs)
- Control Tower guardrails
- Account provisioning process
- Compliance with policies

**Comprehensive AWS Governance Export**:
```python
import boto3
import json
from datetime import datetime

organizations = boto3.client('organizations')
controltower = boto3.client('controltower')
config = boto3.client('config')

def generate_governance_evidence_package():
    """
    Generate comprehensive AWS governance evidence
    """
    evidence = {
        'generated_date': datetime.now().isoformat(),
        'organization': {},
        'accounts': [],
        'scps': [],
        'guardrails': [],
        'compliance': {}
    }
    
    # 1. Organization structure
    org = organizations.describe_organization()['Organization']
    evidence['organization'] = {
        'id': org['Id'],
        'arn': org['Arn'],
        'feature_set': org['FeatureSet'],
        'master_account_id': org['MasterAccountId']
    }
    
    # 2. All accounts
    accounts = organizations.list_accounts()['Accounts']
    for account in accounts:
        evidence['accounts'].append({
            'id': account['Id'],
            'name': account['Name'],
            'email': account['Email'],
            'status': account['Status'],
            'joined_method': account['JoinedMethod']
        })
    
    # 3. Service Control Policies
    policies = organizations.list_policies(
        Filter='SERVICE_CONTROL_POLICY'
    )['Policies']
    
    for policy in policies:
        policy_detail = organizations.describe_policy(
            PolicyId=policy['Id']
        )['Policy']
        
        # Get policy targets
        targets = organizations.list_targets_for_policy(
            PolicyId=policy['Id']
        )['Targets']
        
        evidence['scps'].append({
            'id': policy['Id'],
            'name': policy['Name'],
            'description': policy['Description'],
            'content': json.loads(policy_detail['Content']),
            'targets': [t['TargetId'] for t in targets]
        })
    
    # 4. Config compliance
    compliance_summary = config.describe_compliance_by_config_rule()
    evidence['compliance'] = {
        'total_rules': len(compliance_summary['ComplianceByConfigRules']),
        'compliant': len([r for r in compliance_summary['ComplianceByConfigRules'] 
                         if r['Compliance']['ComplianceType'] == 'COMPLIANT']),
        'non_compliant': len([r for r in compliance_summary['ComplianceByConfigRules']
                             if r['Compliance']['ComplianceType'] == 'NON_COMPLIANT'])
    }
    
    # 5. Control Tower guardrails (if using Control Tower)
    try:
        # Note: Control Tower API availability may vary
        evidence['guardrails'] = get_control_tower_guardrails()
    except:
        evidence['guardrails'] = 'Control Tower API not available'
    
    # Save evidence package
    filename = f"aws-governance-evidence-{datetime.now().strftime('%Y%m%d')}.json"
    with open(filename, 'w') as f:
        json.dump(evidence, f, indent=2, default=str)
    
    print(f"Evidence package generated: {filename}")
    return evidence

def get_control_tower_guardrails():
    """Get enabled Control Tower guardrails"""
    # This would query Control Tower API when available
    # For now, document manually or via AWS Config
    return []

# Generate evidence
evidence_package = generate_governance_evidence_package()

# Generate summary report
print("\n=== AWS Governance Summary ===")
print(f"Total Accounts: {len(evidence_package['accounts'])}")
print(f"Active SCPs: {len(evidence_package['scps'])}")
print(f"Config Compliance: {evidence_package['compliance']['compliant']}/{evidence_package['compliance']['total_rules']} rules compliant")
```

## Common Audit Questions

### Question 1: "How does your organization demonstrate commitment to security from the top down?"

**Strong Answer**:
"Security commitment starts with our Board of Directors and flows through all levels:

**Board Oversight**:
- Board receives quarterly security reports from our CISO
- Security is a standing agenda item at board meetings
- Board includes members with cybersecurity expertise
- Board approved our $2.5M annual security budget
- Board reviews and approves security strategy annually

**Executive Leadership**:
- CISO reports directly to CTO with dotted line to CEO
- CISO presents monthly to executive team
- Security metrics included in executive dashboards
- Executives complete same security training as all employees
- CEO communicates security importance in all-hands meetings

**Organizational Structure**:
- Dedicated security team (8 FTEs) separate from IT operations
- Security team has authority to enforce policies
- Security involved in all major technology decisions
- Security champions program across engineering teams
- Clear escalation path from security team to executives

**Resource Commitment**:
- $2.5M annual security budget (5% of IT budget)
- Ongoing investment in AWS security tools
- Employee security training program
- Regular third-party security assessments
- Competitive compensation for security roles

**Cultural Indicators**:
- Security violations have consequences (documented in policy)
- Security concerns can be raised without retaliation
- 'Security first' mindset embedded in development process
- Regular security communications from leadership
- Security performance metrics in employee reviews"

**Evidence to Provide**:
- Board meeting minutes showing security discussions
- Organizational chart with security reporting structure
- CISO job description and qualifications
- Security budget approval documentation
- CEO communications about security
- Security team charter and authority documentation

### Question 2: "How do you ensure segregation of duties and prevent conflicts of interest?"

**Strong Answer**:
"We implement segregation of duties through organizational structure, AWS architecture, and technical controls:

**Organizational Segregation**:
- Development, operations, and security are separate teams
- No individual has end-to-end control over critical processes
- Code authors cannot approve their own code
- Production deployment requires multiple approvals
- Security team independent from development/operations

**AWS Account Segregation**:
- Production accounts separate from development
- Security tooling in dedicated security account
- Audit functions in separate audit account
- No individual has admin access to multiple critical accounts
- Cross-account access requires approval and logging

**Technical Controls**:
- All production changes go through CI/CD with approval gates
- Break-glass accounts require dual authorization
- Root account credentials physically secured with dual control
- Service Control Policies enforce separation
- IAM policies implement least privilege

**Specific Examples**:
1. **Code Deployment**:
   - Developer writes code (development account)
   - Senior developer reviews (required, different person)
   - Automated security scanning runs
   - DevOps team deploys after approval (production account)
   - Security team monitors (security account)

2. **Financial Systems**:
   - Finance team manages billing accounts
   - Engineering team cannot see billing details
   - Security team cannot modify billing
   - CFO approval required for budget changes

3. **Security Monitoring**:
   - Security team monitors but cannot change infrastructure
   - Infrastructure team cannot modify security tools
   - Audit team has read-only access to everything
   - All actions logged and cannot be deleted

**Documentation**:
- Segregation of duties matrix maintained and reviewed quarterly
- Job descriptions clearly define boundaries
- Conflicts of interest identified and mitigated
- Exception process for small team constraints"

**Evidence to Provide**:
- Segregation of duties matrix
- AWS Organizations account structure diagram
- IAM policies showing separation
- CI/CD pipeline approval requirements
- Security team charter showing independence
- Sample code review showing different author/reviewer

### Question 3: "What is your process for hiring and onboarding employees with security responsibilities?"

**Strong Answer**:
"We have a comprehensive hiring and onboarding process for all employees, with enhanced requirements for security-sensitive roles:

**Pre-Employment Screening**:
- All technical employees: Criminal background check (7 years)
- Production access roles: Employment and education verification
- Security team: Enhanced background check including credit check
- Contractor screening: Same standards as employees
- International: Country-appropriate screening where legally permitted

**Hiring Process**:
1. Job description includes security responsibilities
2. Interview includes security awareness questions
3. References checked (minimum 2 professional references)
4. Background check initiated before offer
5. Offer conditional on satisfactory background check
6. Results reviewed by HR and hiring manager
7. Adverse findings reviewed by legal team

**Onboarding Process** (Day 1-7):
1. **Day 1 Morning**:
   - HR orientation including security policy overview
   - Sign security policy acknowledgment
   - Sign acceptable use policy
   - Review data classification guidelines

2. **Day 1 Afternoon**:
   - Security awareness training (2 hours, must complete)
   - Setup MFA on all accounts
   - Password manager training
   - Phishing awareness training

3. **Within 7 Days**:
   - Role-specific security training (for technical roles)
   - AWS security best practices (for cloud roles)
   - Secure coding training (for developers)
   - Shadow experienced team member

4. **Access Provisioning**:
   - Manager submits access request via ticketing system
   - Security team reviews and approves
   - Least privilege access granted initially
   - Additional access requires justification
   - All access logged and reviewed

**New Hire Monitoring** (First 90 Days):
- Enhanced monitoring of account activity
- Regular check-ins with manager
- Probationary period with access restrictions
- Security team reviews access patterns
- Full access after successful probation

**Training Tracking**:
- HR system tracks all training completion
- Cannot receive full production access until training complete
- Annual refresher training required
- Role changes trigger additional training requirements

**Metrics**:
- 100% of new hires complete security training within 7 days
- Average time to full productivity: 21 days
- Background check completion rate: 100%
- Training compliance: 98%"

**Evidence to Provide**:
- Hiring process flowchart
- Background check policy
- Sample background check completion (redacted)
- New hire security training agenda
- Onboarding checklist with security items
- Training completion report for recent hires
- Access provisioning ticket examples

### Question 4: "How do you handle terminations and ensure access is removed promptly?"

**Strong Answer**:
"We have an automated offboarding process that ensures all access is removed within 4 hours:

**Termination Process**:

1. **Planning Phase** (for planned terminations):
   - HR notifies security team 24 hours in advance
   - Security team identifies all access
   - IT prepares account disable scripts
   - Knowledge transfer planned

2. **Termination Day** (H-Hour = termination conversation):
   - **H+0 minutes**: HR initiates offboarding workflow in HR system
   - **H+5 minutes**: Automated process begins:
     - Azure AD/Okta account disabled
     - AWS IAM Identity Center access removed
     - VPN access revoked
     - Physical badge deactivated
     - Email account converted to shared mailbox (not deleted)
   - **H+30 minutes**: Manual verification by security team
   - **H+4 hours**: Complete access removal certified

3. **Post-Termination**:
   - Manager reviews employee's files and access history
   - Company property retrieval (laptop, phone, badge)
   - Exit interview includes security reminders
   - NDA and confidentiality obligations reinforced

**Immediate Terminations** (security incidents):
- Security team can immediately disable all access
- Process completes in under 15 minutes
- Physical security notified immediately
- Law enforcement contacted if necessary

**AWS-Specific Offboarding**:
- IAM Identity Center permission sets removed immediately
- Any standalone IAM users disabled (should not exist)
- Access keys revoked and validated non-functional
- Service accounts owned by terminated employee identified and rotated
- CloudTrail reviewed for last 30 days of activity
- Any resources created by employee reviewed

**Contractor Offboarding**:
- Same process as employees
- Contract end date triggers automatic offboarding
- 30-day warning to manager before contract expiration
- Vendor company notified of access removal

**Monitoring**:
- Weekly report of all terminations and access removal
- Quarterly audit of inactive accounts
- Annual validation that terminated employees have zero access
- Automated alerts if terminated employee attempts access

**Metrics**:
- Average access removal time: 22 minutes
- 100% of terminations processed within 4-hour SLA
- Zero instances of terminated employee retaining access
- Zero security incidents from former employees

**Special Cases**:
- Contractors with long-term engagements: 90-day access reviews
- Temporary leave: Access suspended, not deleted
- Role changes: Access reviewed and adjusted
- Rehires: New background check, new onboarding process"

**Evidence to Provide**:
- Offboarding process flowchart
- HR system automated workflow configuration
- Sample termination checklist (completed, redacted)
- Access removal completion certificates
- Quarterly inactive account audit
- Termination metrics dashboard
- Automated access removal scripts

### Question 5: "How do you ensure employees maintain competence in security?"

**Strong Answer**:
"We maintain employee security competence through continuous training, assessment, and professional development:

**Mandatory Training Program**:

1. **All Employees**:
   - Annual security awareness training (45 minutes)
   - Quarterly phishing simulations
   - Policy updates as they occur
   - Incident response basics

2. **Technical Staff**:
   - Annual AWS security best practices (4 hours)
   - Secure coding training (8 hours annually)
   - Cloud security fundamentals
   - Specific training for tools they use

3. **Security Team**:
   - Advanced threat detection (quarterly)
   - Incident response drills (quarterly)
   - AWS security specialty certification (encouraged)
   - Security conferences (annual attendance)
   - Industry
