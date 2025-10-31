
Control: CC6.1 - Logical Access Security

Description: 
We restrict logical access to AWS resources through AWS IAM, enforcing multi-factor authentication and least-privilege access principles.

Implementation:
- All users authenticate via IAM Identity Center (SSO) with mandatory MFA
- Access is granted through IAM roles, not long-term credentials
- IAM Access Analyzer continuously monitors for overly permissive policies
- Access is reviewed quarterly by resource owners

Ownership:
- Security Team: IAM policy standards and monitoring
- Engineering Managers: Access approvals for their teams
- HR: Notification of terminations

Evidence of Operation:
- IAM credential reports showing MFA enforcement
- Quarterly access review sign-offs
- IAM Access Analyzer findings (remediated)
- CloudTrail logs of access activities
