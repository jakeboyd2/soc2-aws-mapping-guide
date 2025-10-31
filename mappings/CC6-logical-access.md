# CC6: Logical and Physical Access Controls (Logical Access)

## Control Family Overview

CC6 focuses on restricting logical access to information assets, including data, software, and IT infrastructure. This control family ensures that only authorized users can access systems and data appropriate to their role.

## Control Description

The entity implements logical access security measures to protect against threats from sources outside its system boundaries. Key requirements include:

- **Authentication**: Verify user identity before granting access
- **Authorization**: Ensure users can only access resources appropriate to their role
- **Access Reviews**: Regularly review and update access permissions
- **Privileged Access**: Enhanced controls for administrative accounts
- **Session Management**: Secure handling of user sessions

## AWS Services That Address CC6

### Primary Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS IAM** | Identity and access management | User management, policies, MFA, roles |
| **AWS IAM Identity Center** | Centralized workforce access | SSO, permission sets, integration |
| **Amazon Cognito** | Customer identity management | User pools, federation, MFA |
| **AWS Organizations** | Account governance | SCPs, account isolation, centralized management |
| **AWS Secrets Manager** | Credential management | Secret rotation, encryption, audit trail |

### Supporting Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS CloudTrail** | Access logging | API call tracking, user activity monitoring |
| **Amazon GuardDuty** | Threat detection | Anomalous access detection, credential compromise alerts |
| **AWS IAM Access Analyzer** | Permission analysis | Unused access identification, external access detection |
| **AWS Systems Manager Session Manager** | Secure remote access | No SSH keys required, session logging |
| **Amazon CloudWatch** | Monitoring & alerting | Login metrics, access pattern analysis |
| **AWS Config** | Configuration tracking | IAM policy changes, compliance monitoring |

## Implementation Steps

### Phase 1: Foundation (Week 1-2)

#### 1. Enable AWS Organizations and Account Structure

```bash
# Create organizational structure
aws organizations create-organization --feature-set ALL

# Create OUs for environment isolation
aws organizations create-organizational-unit \
  --parent-id r-xxxx \
  --name Production

aws organizations create-organizational-unit \
  --parent-id r-xxxx \
  --name Development
```

**Why**: Provides account-level isolation and centralized governance

#### 2. Implement IAM Identity Center (AWS SSO)

```bash
# Enable IAM Identity Center
aws sso-admin create-instance \
  --name "CompanySSO"

# Create permission sets with least privilege
aws sso-admin create-permission-set \
  --instance-arn arn:aws:sso:::instance/ssoins-xxxx \
  --name "ReadOnlyAccess" \
  --session-duration "PT4H"
```

**Configuration Checklist**:
- ✅ Connect to identity provider (Okta, Azure AD, Google Workspace)
- ✅ Create role-based permission sets
- ✅ Enable MFA requirement
- ✅ Set session duration limits (4-8 hours recommended)
- ✅ Configure automatic provisioning (SCIM)

#### 3. Enforce MFA for All Users

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyAllExceptListedIfNoMFA",
      "Effect": "Deny",
      "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:GetUser",
        "iam:ListMFADevices",
        "iam:ListVirtualMFADevices",
        "iam:ResyncMFADevice",
        "sts:GetSessionToken"
      ],
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}
```

**Apply via SCP at organization root**

### Phase 2: Access Controls (Week 3-4)

#### 4. Implement Least Privilege IAM Policies

**Best Practices**:
- Start with AWS managed policies, then customize
- Use IAM Access Analyzer to identify unused permissions
- Implement permission boundaries for delegated administration
- Tag resources for attribute-based access control (ABAC)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Environment": "${aws:PrincipalTag/Environment}"
        }
      }
    }
  ]
}
```

#### 5. Configure Session Management

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT-ID:role/EngineerRole"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": ["10.0.0.0/8", "203.0.113.0/24"]
        },
        "DateGreaterThan": {
          "aws:CurrentTime": "2024-01-01T00:00:00Z"
        },
        "DateLessThan": {
          "aws:CurrentTime": "2024-12-31T23:59:59Z"
        }
      }
    }
  ]
}
```

**Session Controls**:
- Maximum session duration: 4-12 hours
- IP allowlisting for production access
- Geographic restrictions where applicable
- Time-based access windows for sensitive operations

#### 6. Implement Privileged Access Management

**For Administrative Access**:
```bash
# Create break-glass emergency access role
aws iam create-role \
  --role-name EmergencyAccess \
  --assume-role-policy-document file://emergency-trust-policy.json \
  --max-session-duration 3600

# Attach AdministratorAccess but with alerting
aws iam attach-role-policy \
  --role-name EmergencyAccess \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

**Create CloudWatch alarm for emergency role usage**:
```bash
aws cloudwatch put-metric-alarm \
  --alarm-name "EmergencyAccessUsed" \
  --alarm-description "Alert when emergency access role is assumed" \
  --metric-name "AssumeRoleCount" \
  --namespace "AWS/CloudTrail" \
  --statistic Sum \
  --period 60 \
  --threshold 1 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1
```

### Phase 3: Monitoring & Automation (Week 5-6)

#### 7. Enable Comprehensive Logging

```bash
# Enable CloudTrail in all regions
aws cloudtrail create-trail \
  --name org-audit-trail \
  --s3-bucket-name audit-logs-bucket \
  --is-multi-region-trail \
  --enable-log-file-validation \
  --is-organization-trail

# Enable CloudTrail for all events
aws cloudtrail put-event-selectors \
  --trail-name org-audit-trail \
  --event-selectors '[{
    "ReadWriteType": "All",
    "IncludeManagementEvents": true,
    "DataResources": []
  }]'
```

#### 8. Configure Access Reviews

**Automated Quarterly Access Review**:
```python
# Lambda function to generate access review report
import boto3
import json
from datetime import datetime, timedelta

def lambda_handler(event, context):
    iam = boto3.client('iam')
    
    # Get all users
    users = iam.list_users()['Users']
    
    inactive_users = []
    for user in users:
        # Check last activity
        try:
            access_keys = iam.list_access_keys(UserName=user['UserName'])
            for key in access_keys['AccessKeyMetadata']:
                last_used = iam.get_access_key_last_used(
                    AccessKeyId=key['AccessKeyId']
                )
                
                if 'LastUsedDate' in last_used['AccessKeyLastUsed']:
                    last_date = last_used['AccessKeyLastUsed']['LastUsedDate']
                    if (datetime.now(last_date.tzinfo) - last_date).days > 90:
                        inactive_users.append({
                            'UserName': user['UserName'],
                            'LastUsed': str(last_date),
                            'DaysInactive': (datetime.now(last_date.tzinfo) - last_date).days
                        })
        except Exception as e:
            print(f"Error checking {user['UserName']}: {str(e)}")
    
    return {
        'statusCode': 200,
        'body': json.dumps(inactive_users)
    }
```

#### 9. Deploy IAM Access Analyzer

```bash
# Create IAM Access Analyzer
aws accessanalyzer create-analyzer \
  --analyzer-name org-access-analyzer \
  --type ORGANIZATION

# Create archive rule for expected external access
aws accessanalyzer create-archive-rule \
  --analyzer-name org-access-analyzer \
  --rule-name "partner-access" \
  --filter principal.AWS=arn:aws:iam::PARTNER-ACCOUNT:root
```

## Evidence Collection Tips

### Required Evidence Types

#### 1. Access Control Policies

**What Auditors Want**:
- IAM policies and permission sets
- Service Control Policies (SCPs)
- Resource-based policies
- Trust relationships

**How to Collect**:
```bash
# Export all IAM policies
aws iam list-policies --scope Local --query 'Policies[*].[PolicyName,Arn]' \
  --output table > iam-policies.txt

# Export all roles and their policies
for role in $(aws iam list-roles --query 'Roles[*].RoleName' --output text); do
  aws iam get-role --role-name $role > "roles/${role}.json"
  aws iam list-attached-role-policies --role-name $role >> "roles/${role}.json"
done

# Export SCPs
aws organizations list-policies --filter SERVICE_CONTROL_POLICY \
  --output json > scps.json
```

#### 2. User Access Listings

**What Auditors Want**:
- Current user inventory
- Group memberships
- Role assignments
- Privileged account identification

**How to Collect**:
```bash
# Generate comprehensive user report
aws iam get-credential-report > credential-report.csv

# Export IAM Identity Center assignments
aws sso-admin list-account-assignments \
  --instance-arn arn:aws:sso:::instance/ssoins-xxxx \
  --account-id YOUR-ACCOUNT-ID \
  --permission-set-arn arn:aws:sso:::permissionSet/xxxx
```

#### 3. Authentication Logs

**What Auditors Want**:
- Login attempts (successful and failed)
- MFA usage
- Assume role events
- API access patterns

**How to Collect**:
```bash
# Query CloudTrail for authentication events
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=ConsoleLogin \
  --start-time 2024-01-01 \
  --end-time 2024-03-31 > console-logins.json

# Failed authentication attempts
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=ConsoleLogin \
  --start-time 2024-01-01 \
  --end-time 2024-03-31 \
  | jq '.Events[] | select(.CloudTrailEvent | fromjson | .responseElements.ConsoleLogin == "Failure")'
```

#### 4. Access Review Documentation

**What Auditors Want**:
- Quarterly access review reports
- Approval records for access changes
- Deprovisioning evidence
- Privileged access justifications

**Documentation Template**:
```markdown
# Quarterly Access Review - Q1 2024

## Review Date: 2024-03-15
## Reviewer: Jane Smith, Security Manager
## Scope: All IAM users and roles in Production accounts

### Summary
- Total Users Reviewed: 47
- Access Removed: 3 inactive users
- Access Modified: 5 users (reduced permissions)
- New Findings: 2 shared accounts identified and remediated

### Detailed Findings
| User | Current Access | Recommendation | Status |
|------|---------------|----------------|--------|
| john.doe | AdministratorAccess | Reduce to required services | Completed |
| ... | ... | ... | ... |

### Actions Taken
1. Disabled 3 inactive user accounts (>90 days no activity)
2. Removed AdministratorAccess from 2 developer accounts
3. Created new fine-grained policies for application teams
4. Documented all changes in JIRA tickets: SEC-101, SEC-102, SEC-103
```

#### 5. MFA Evidence

**What Auditors Want**:
- MFA enrollment rate
- MFA enforcement policies
- Compliance monitoring

**How to Collect**:
```bash
# Check MFA status for all users
aws iam get-credential-report --output text | \
  awk -F',' '{print $1","$4}' > mfa-status.csv

# Verify MFA enforcement via SCP
aws organizations describe-policy \
  --policy-id p-xxxxxxxx > mfa-enforcement-scp.json
```

## Common Audit Questions

### Question 1: "How do you ensure only authorized users have access to production systems?"

**Strong Answer**:
"We implement multiple layers of access control:

1. **Account Isolation**: Production workloads run in dedicated AWS accounts within our AWS Organization, separated from development and staging.

2. **Identity Federation**: All access goes through IAM Identity Center with SSO integration to our Okta identity provider. No IAM users have standing access to production.

3. **Least Privilege**: We use role-based access control (RBAC) with permission sets that grant only the minimum required permissions. For example, our 'ProductionReadOnly' permission set allows viewing resources but no modifications.

4. **Just-in-Time Access**: For operational changes, engineers request temporary elevated access through our ticketing system, which triggers a time-limited role assumption (4-hour maximum).

5. **Continuous Monitoring**: IAM Access Analyzer continuously scans for overly permissive policies or unexpected external access."

**Evidence to Provide**:
- AWS Organization structure diagram
- IAM Identity Center permission set definitions
- Sample access request ticket and approval workflow
- IAM Access Analyzer findings report (showing zero high-severity findings)

### Question 2: "How do you handle privileged access and administrative accounts?"

**Strong Answer**:
"We maintain strict controls on privileged access:

1. **No Standing Admin Access**: No user has permanent AdministratorAccess. Our base permission sets provide read-only or limited write access.

2. **Break-Glass Procedures**: We maintain two emergency access roles with full admin rights, stored in a physical safe with dual control. Usage triggers immediate alerts to security leadership.

3. **Privileged Access Workflow**: 
   - Engineers submit requests through ServiceNow
   - Manager and Security approve
   - Time-limited (1-4 hours) privileged role assigned
   - All actions logged and reviewed

4. **Separation of Duties**: Infrastructure changes require peer review in our GitOps workflow before reaching production.

5. **Monitoring**: CloudWatch alarms alert on any privileged role assumption. GuardDuty monitors for credential compromise."

**Evidence to Provide**:
- Break-glass account procedures document
- Privileged access request workflow diagram
- Sample approval chain for administrative access
- CloudWatch alarm configuration for privileged access
- Last 90 days of privileged access audit log

### Question 3: "What is your process for periodic access reviews?"

**Strong Answer**:
"We conduct comprehensive access reviews quarterly:

1. **Automated Reporting**: Lambda functions generate reports of:
   - All active users and their permissions
   - Last activity date for each user
   - Users with no activity in 90+ days
   - Changes to privileged roles

2. **Review Process**:
   - Security team distributes reports to managers
   - Managers verify each user's access is still appropriate
   - Security team remediates identified issues within 5 business days

3. **Continuous Monitoring**: Between quarterly reviews:
   - IAM Access Analyzer runs daily to detect unused permissions
   - Automated alerts for new external access
   - Monthly reporting on credential age and MFA compliance

4. **Immediate Termination**: When employees leave, HR triggers automated deprovisioning within 4 hours via our SCIM integration."

**Evidence to Provide**:
- Q1, Q2, Q3, Q4 access review reports with manager sign-offs
- Automated access review Lambda function code
- Sample remediation tickets from access reviews
- Employee offboarding automation workflow
- IAM Access Analyzer continuous monitoring configuration

### Question 4: "How do you enforce multi-factor authentication?"

**Strong Answer**:
"MFA is mandatory for all access methods:

1. **Policy Enforcement**: We apply an SCP at the organization root that denies all API calls unless MFA is present. There are zero exceptions.

2. **Federated Access**: IAM Identity Center integration with Okta requires MFA before federation. Users cannot reach AWS without satisfying Okta's MFA requirement.

3. **Monitoring**: We track MFA compliance daily:
   - Credential report shows 100% MFA enrollment
   - Alerts fire if any user without MFA is detected
   - Annual attestation by all users

4. **Hardware Tokens**: For our 5 break-glass accounts, we use hardware MFA devices (YubiKey) rather than virtual MFA."

**Evidence to Provide**:
- SCP policy requiring MFA
- IAM credential report showing 100% MFA enrollment
- Okta MFA configuration screenshots
- Hardware token inventory and assignment log
- MFA compliance monitoring dashboard

### Question 5: "How do you detect and respond to unauthorized access attempts?"

**Strong Answer**:
"We have multiple detection and response mechanisms:

1. **GuardDuty**: Continuously monitors for:
   - Compromised credentials (unusual API calls)
   - Brute force attacks
   - Access from malicious IPs
   - Unusual data exfiltration patterns

2. **CloudTrail Analysis**: All API calls logged and analyzed for:
   - Failed authentication attempts
   - Privilege escalation attempts
   - Unusual access patterns
   - Access from unexpected geolocations

3. **Automated Response**: 
   - EventBridge rules trigger Lambda for immediate response
   - Auto-disable credentials showing compromise indicators
   - Auto-quarantine resources accessed by suspicious activity
   - Immediate alerts to security team via PagerDuty

4. **Investigation Workflow**: Security team investigates all alerts within defined SLAs (critical: 15 min, high: 1 hour)."

**Evidence to Provide**:
- GuardDuty findings from last 90 days
- Sample incident response ticket showing detection and remediation
- EventBridge + Lambda auto-response code
- CloudWatch dashboard showing failed auth attempts
- Incident response runbooks for access-related incidents

## Continuous Compliance

### Monthly Tasks
- [ ] Review IAM Access Analyzer findings
- [ ] Check credential report for inactive users
- [ ] Verify MFA compliance remains 100%
- [ ] Review privileged access usage

### Quarterly Tasks
- [ ] Conduct formal access reviews with manager sign-offs
- [ ] Review and update IAM policies
- [ ] Test break-glass account procedures
- [ ] Update access control documentation

### Annual Tasks
- [ ] Complete user access attestation
- [ ] Review and update access control procedures
- [ ] Conduct tabletop exercise for unauthorized access scenario
- [ ] Penetration testing of authentication mechanisms

## Additional Resources

- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)
- [NIST SP 800-53 AC Controls](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)

---
