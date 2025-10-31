# CC6: Logical and Physical Access Controls

## Overview
The **CC6** criteria under the AICPA Trust Services Criteria address **logical and physical access controls** that restrict unauthorized access to systems and data.  
This includes identity management, authentication, authorization, privilege control, and the protection of sensitive credentials and infrastructure.  
In cloud environments, these controls are primarily achieved through AWS identity and access services, audit monitoring, and secure key management.

The following controls focus on AWS **IAM**, **Cognito**, and **Secrets Manager**, aligned to **CC6.1–CC6.8**.

---

## **Control 1: Identity and Access Management (IAM) Governance**

**SOC 2 Reference:** CC6.1 – Logical access to information assets is restricted to authorized users.

**AWS Service:** AWS IAM

**Description:**  
IAM enables centralized access management across AWS accounts and resources. Proper governance ensures only authorized individuals can access sensitive data and infrastructure.

**Implementation Steps:**
1. Establish IAM roles and groups based on the principle of least privilege.  
2. Assign permissions using managed or custom IAM policies.  
3. Prohibit use of root account for daily operations.  
4. Perform quarterly access reviews and document results.  
5. Enable IAM Access Analyzer to detect excessive permissions or policy deviations.

---

## **Control 2: Multi-Factor Authentication (MFA)**

**SOC 2 Reference:** CC6.2 – System access requires authentication mechanisms commensurate with risk.

**AWS Service:** AWS IAM + AWS SSO

**Description:**  
MFA adds an extra layer of security to AWS accounts, reducing risk from credential compromise.

**Implementation Steps:**
1. Require MFA for all IAM users and federated accounts with administrative privileges.  
2. Enforce MFA via AWS Organizations Service Control Policies (SCPs).  
3. Use hardware or virtual MFA devices for account access.  
4. Monitor MFA compliance using AWS Config rules.  
5. Periodically test MFA functionality across critical roles.

---

## **Control 3: Credential and Secret Management**

**SOC 2 Reference:** CC6.3 – Credentials are protected through secure management and rotation.

**AWS Service:** AWS Secrets Manager

**Description:**  
Secrets Manager securely stores, rotates, and controls access to credentials, API keys, and database passwords.

**Implementation Steps:**
1. Store application and system credentials in Secrets Manager instead of environment variables or code.  
2. Enable automatic rotation for database and service credentials.  
3. Encrypt all secrets using AWS KMS.  
4. Define IAM roles that allow least-privilege access to specific secrets.  
5. Monitor secret access logs through CloudTrail for anomalies.

---

## **Control 4: Federated Identity and Single Sign-On**

**SOC 2 Reference:** CC6.2, CC6.4 – Authentication is enforced consistently across systems and applications.

**AWS Service:** AWS Cognito + AWS IAM Identity Center (AWS SSO)

**Description:**  
Federated authentication allows centralized access control using corporate identity providers (e.g., Okta, Azure AD), ensuring consistent user identity management across AWS and applications.

**Implementation Steps:**
1. Integrate Cognito or IAM Identity Center with corporate SSO solutions.  
2. Enforce strong password and session management policies.  
3. Limit user pools to verified domains or directories.  
4. Monitor user authentication logs via CloudWatch or CloudTrail.  
5. Deactivate inactive accounts automatically after a defined period.

---

## **Control 5: Role-Based Access Control (RBAC)**

**SOC 2 Reference:** CC6.1, CC6.4 – Access to information assets is based on job responsibilities.

**AWS Service:** AWS IAM

**Description:**  
RBAC ensures that access rights are assigned based on functional roles rather than individuals, minimizing human error and unauthorized privilege escalation.

**Implementation Steps:**
1. Create IAM roles that reflect functional responsibilities (e.g., Developer, Auditor, Admin).  
2. Assign access through roles rather than individual user permissions.  
3. Use IAM Groups to simplify management and enforcement.  
4. Periodically audit IAM role assignments and policy scopes.  
5. Document all role-to-function mappings for auditor review.

---

## **Control 6: Logging and Monitoring of Access**

**SOC 2 Reference:** CC6.6 – Access to systems and data is logged and monitored for inappropriate activity.

**AWS Service:** AWS CloudTrail + CloudWatch

**Description:**  
Access activity is continuously monitored and logged to identify unauthorized actions or anomalies.

**Implementation Steps:**
1. Enable organization-wide CloudTrail logging for all AWS accounts.  
2. Forward logs to a centralized S3 bucket with restricted access.  
3. Integrate CloudWatch alarms to detect policy violations or suspicious access.  
4. Use AWS GuardDuty or Security Hub to detect anomalies.  
5. Retain and review logs as part of quarterly audit readiness checks.

---

## **Control 7: Secrets and Key Access Review**

**SOC 2 Reference:** CC6.3, CC6.8 – Access to encryption keys and secrets is limited and reviewed regularly.

**AWS Service:** AWS Secrets Manager + AWS KMS

**Description:**  
Protecting cryptographic material ensures that only authorized users can access encryption keys or sensitive data secrets.

**Implementation Steps:**
1. Use KMS for key generation and encryption of secrets.  
2. Restrict key usage to specific IAM roles or AWS services.  
3. Enable automatic key rotation for sensitive encryption keys.  
4. Conduct quarterly access reviews for Secrets Manager and KMS.  
5. Document rotation and review evidence for audit verification.

---

## **Control 8: GRC Oversight and SOC 2 Mapping**

**SOC 2 Reference:** CC6.1–CC6.8 – Logical access controls are defined, implemented, and monitored.

**AWS and GRC Integration:** AWS Audit Manager, AWS Config, GRC Platforms (Drata, Archer, Vanta)

**Description:**  
GRC teams maintain a mapping between implemented AWS controls and SOC 2 requirements to ensure continuous compliance visibility and audit readiness.

**Implementation Steps:**
1. Use **AWS Audit Manager** SOC 2 framework to collect and link evidence.  
2. Map IAM, Cognito, and Secrets Manager controls to CC6.x criteria.  
3. Automate compliance checks using AWS Config rules.  
4. Store access logs and evidence in a secure, version-controlled repository.  
5. Conduct internal readiness assessments before annual SOC 2 audits.

---

## **GRC Mapping Table**

| AWS Service / Control Area   | SOC 2 Reference | Supporting Artifacts | GRC Mapping Objective |
|-------------------------------|-----------------|----------------------|------------------------|
| AWS IAM Governance            | CC6.1, CC6.4    | IAM policies, access review reports | Manage least privilege and access segregation |
| MFA Enforcement               | CC6.2           | MFA policy, AWS Config compliance | Demonstrate secure authentication control |
| Secrets Manager               | CC6.3, CC6.8    | Secret rotation logs, KMS policies | Protect and manage credentials securely |
| Cognito / SSO Integration     | CC6.2, CC6.4    | Cognito settings, SSO configs | Provide federated authentication and SSO control |
| RBAC Implementation           | CC6.1, CC6.4    | Role definitions, IAM group mappings | Enforce functional access structure |
| Access Logging & Monitoring   | CC6.6           | CloudTrail, GuardDuty alerts | Detect and respond to unauthorized access |
| Key Access Review             | CC6.3, CC6.8    | KMS key rotation evidence | Ensure secure management of cryptographic materials |
| GRC Oversight (Audit Manager) | CC6.1–CC6.8     | SOC 2 control mappings, evidence matrix | Maintain compliance visibility and audit readiness |

---

Implementing robust logical access controls through AWS **IAM**, **Cognito**, and **Secrets Manager** strengthens the organization’s SOC 2 CC6 compliance posture.  
Combined with GRC oversight and evidence automation, these controls demonstrate accountability, secure access, and effective protection of sensitive systems and data.

---

**References:**  
- AICPA Trust Services Criteria (2017, updated 2020)  
- COSO Internal Control — Integrated Framework (2013)  
- AWS Well-Architected Framework: Security Pillar  
- AWS Audit Manager: SOC 2 Framework Template
