# C1: Confidentiality 
### Reference: AICPA Trust Services Criteria 2017 (C1.1–C1.3)

## Overview
The **Confidentiality principle (C1)** ensures that information designated as confidential is **protected from unauthorized access, use, and disclosure** throughout its lifecycle — from creation and storage to final disposition.  
Per AICPA Trust Services Criteria (TSP Section 100, C1 series), organizations must implement controls that maintain confidentiality in accordance with contractual, regulatory, or internal commitments.

---

## AWS Services Supporting Confidentiality Controls

AWS provides several native security services that directly support SOC 2 **C1** confidentiality requirements.  
The following **five mapped controls** demonstrate how AWS Key Management Service (KMS), Amazon S3 Encryption, and Virtual Private Cloud (VPC) enhance data confidentiality and compliance readiness.

---

### **Control 1: AWS Key Management Service (KMS) – Encryption Key Governance**
**SOC 2 Mapping:** C1.1 – Protect Confidential Information During Storage and Transmission  
**Description:**  
AWS KMS provides centralized control over cryptographic keys used to protect data across AWS services.  
It enforces encryption, decryption, and key rotation policies aligned with confidentiality requirements.

**Implementation Guidance:**  
- Enable KMS-managed encryption for S3, EBS, RDS, and Lambda.  
- Configure key rotation policies for customer-managed CMKs (Customer Master Keys).  
- Use AWS CloudTrail to log key usage and monitor unauthorized activity.  

**GRC Mapping Insight:**  
Documented KMS policies and rotation schedules demonstrate compliance with **C1.1 controls for data confidentiality**.  
GRC teams should retain key management logs as audit evidence for SOC 2 verification.

---

### **Control 2: Amazon S3 – Server-Side Encryption (SSE)**
**SOC 2 Mapping:** C1.1 – Safeguard Confidential Data in Storage  
**Description:**  
S3 offers server-side encryption (SSE-S3, SSE-KMS, SSE-C) to automatically encrypt data at rest, ensuring protection from unauthorized access.

**Implementation Guidance:**  
- Enforce bucket-level encryption with AWS KMS integration.  
- Disable public access and apply IAM or bucket policies to restrict access.  
- Enable versioning and Object Lock for data immutability in compliance scenarios.  

**GRC Mapping Insight:**  
S3 encryption configuration and IAM policy documentation serve as **control evidence for confidentiality**, ensuring data remains encrypted and access-controlled per SOC 2 C1.

---

### **Control 3: Amazon S3 – Encryption in Transit (TLS/SSL)**
**SOC 2 Mapping:** C1.1 – Protect Data During Transmission  
**Description:**  
AWS S3 supports HTTPS/TLS encryption to protect data in transit between clients and AWS storage endpoints.

**Implementation Guidance:**  
- Require HTTPS for all S3 data transfers via IAM policies or CloudFront distributions.  
- Use AWS Certificate Manager (ACM) for SSL certificate management.  
- Test endpoints for TLS 1.2+ compliance.  

**GRC Mapping Insight:**  
CloudFront and S3 access logs validating HTTPS usage provide **audit evidence for C1 encryption-in-transit requirements**, ensuring confidentiality of data flow.

---

### **Control 4: AWS VPC – Network Segmentation and Isolation**
**SOC 2 Mapping:** C1.2 – Restrict Access to Confidential Information  
**Description:**  
VPC enables logical isolation of AWS resources, restricting data flow to authorized networks and systems.

**Implementation Guidance:**  
- Deploy workloads in private subnets and enforce Security Group and Network ACL rules.  
- Use VPC endpoints for private connections to AWS services, eliminating public exposure.  
- Integrate with AWS Firewall Manager for network policy consistency.  

**GRC Mapping Insight:**  
Network segmentation and access control documentation form **evidence of logical separation**, a key aspect of confidentiality management per C1.2.

---

### **Control 5: AWS KMS + VPC – Data Flow and Access Control Integration**
**SOC 2 Mapping:** C1.3 – Restrict Data Access and Monitor Use  
**Description:**  
Combining AWS KMS encryption with VPC private networking enforces layered confidentiality — encrypting data while also controlling where it flows.

**Implementation Guidance:**  
- Restrict KMS key access to specific IAM roles within a private VPC.  
- Implement AWS Config rules to monitor for unencrypted or publicly exposed resources.  
- Automate alerting for key misuse via AWS CloudWatch and Security Hub.  

**GRC Mapping Insight:**  
GRC teams can map **KMS + VPC integration evidence** to SOC 2 confidentiality controls, showing how encryption and access governance jointly protect data from unauthorized disclosure.

---

## GRC Audit Preparation Steps

1. **Document Data Flow and Encryption Scope**  
   Define which data is considered confidential and how AWS encryption protects it at rest, in use, and in transit.  

2. **Collect Configuration Evidence**  
   Retain screenshots or exports of KMS key policies, S3 encryption settings, and VPC network ACLs.  

3. **Establish Key and Access Management Policies**  
   Maintain key lifecycle policies, rotation schedules, and access control documentation.  

4. **Perform Continuous Compliance Monitoring**  
   Use AWS Config, CloudTrail, and Security Hub to monitor deviations from confidentiality baselines.  

5. **Integrate with Policy Frameworks**  
   Map AWS technical controls to corporate confidentiality policies, vendor NDAs, and SOC 2 C1.1–C1.3 requirements.

---

## Summary Table

| AWS Service | SOC 2 Control | Primary Objective | Evidence for Auditors | Key Confidentiality Function |
|--------------|----------------|-------------------|------------------------|-------------------------------|
| **KMS** | C1.1 | Encrypt data and manage keys | KMS key policies, rotation logs | Data-at-rest protection |
| **S3 SSE** | C1.1 | Automatic encryption of stored data | S3 encryption configs, bucket policies | Encrypted data storage |
| **S3 TLS** | C1.1 | Secure data in transit | Access logs, TLS enforcement policy | Encryption in transit |
| **VPC** | C1.2 | Restrict network access | VPC flow logs, ACL and SG rules | Logical network isolation |
| **KMS + VPC** | C1.3 | Combine encryption and access control | Config and CloudTrail reports | Controlled, encrypted data flow |

---

### References
- **AICPA Trust Services Criteria 2017 (TSP Section 100, C1.1–C1.3)**:contentReference[oaicite:1]{index=1}  
- **AWS Security Documentation:**  
  - [AWS Key Management Service (KMS)](https://docs.aws.amazon.com/kms/latest/developerguide/)  
  - [Amazon S3 Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)  
  - [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/)  

---

**Prepared for:** GRC & Compliance Teams  
**Purpose:** SOC 2 Audit Preparation — C1 Confidentiality Controls Mapped to AWS Services
