# CC8: Change Management  
**Aligned with AICPA Trust Services Criteria (TSP Section 100)**  
**Focus AWS Services:** CodePipeline | Config | CloudTrail  

---

## Overview
The **Change Management** component of the Trust Services Criteria (CC8) establishes controls for identifying, authorizing, testing, implementing, and documenting changes to systems and configurations.  
It ensures that only approved and properly tested changes are deployed to production environments, reducing the risk of unauthorized modifications or operational disruptions.

The AICPA CC8 criteria emphasize:
- Controlled and approved system changes  
- Proper testing and documentation of changes  
- Prevention of unauthorized modifications  
- Monitoring and rollback mechanisms  

---

## **Control 1: Change Authorization and Workflow Management**

**AWS Service:** AWS CodePipeline  
**SOC 2 Mapping:** CC8.1 — The entity authorizes, designs, develops, and implements changes using a controlled change management process.  

**Description:**  
AWS CodePipeline automates build, test, and deployment workflows. It enforces consistency, tracks approvals, and provides a clear audit trail of changes deployed to production environments.

**Implementation Steps:**
1. Create pipelines that require **manual approval stages** before deployment.  
2. Configure **IAM policies** to restrict who can approve or modify pipeline stages.  
3. Enable **AWS CloudTrail logging** for pipeline executions to track changes.  
4. Integrate **AWS CodeCommit** or **GitHub** repositories for version-controlled source management.  
5. Review **pipeline execution history** regularly to confirm adherence to change policies.  

**GRC Mapping Tip:**  
- Document the approval process as evidence of change authorization.  
- Use CodePipeline audit logs to demonstrate change traceability and compliance.  

---

## **Control 2: Configuration Baseline and Drift Detection**

**AWS Service:** AWS Config  
**SOC 2 Mapping:** CC8.1 & CC8.2 — The entity tests, approves, and implements system changes while maintaining configuration integrity.  

**Description:**  
AWS Config records configuration changes and evaluates them against predefined rules, ensuring configurations remain compliant with approved baselines.

**Implementation Steps:**
1. Enable **AWS Config** across all accounts and regions.  
2. Define **Config Rules** (e.g., “restricted S3 public access,” “EC2 encryption enabled”).  
3. Establish **remediation actions** for noncompliant resources.  
4. Use **Config snapshots** for historical configuration baselines.  
5. Integrate **Config findings** into a central GRC tool for monitoring and reporting.  

**GRC Mapping Tip:**  
- Map Config Rules to SOC 2 CC8 controls as evidence of configuration governance.  
- Periodically review drift reports to validate adherence to approved configurations.  

---

## **Control 3: Change Logging and Audit Trail**

**AWS Service:** AWS CloudTrail  
**SOC 2 Mapping:** CC8.3 — The entity prevents unauthorized system changes through monitoring and logging of change activities.  

**Description:**  
AWS CloudTrail provides an immutable audit log of all API and console actions across AWS environments. It is essential for detecting unauthorized configuration or deployment changes.

**Implementation Steps:**
1. Enable **organization-wide CloudTrail logging**.  
2. Store logs in an **S3 bucket** with encryption and restricted access.  
3. Integrate **CloudTrail logs with CloudWatch** for real-time change detection.  
4. Set up **alerts for critical events** (e.g., IAM policy modifications, security group updates).  
5. Retain CloudTrail logs according to your organization’s **data retention policy**.  

**GRC Mapping Tip:**  
- CloudTrail logs serve as primary audit evidence for SOC 2 CC8 controls.  
- Cross-reference event history with change approval records from CodePipeline.  

---

## **Mapping AWS Services to SOC 2 Change Management Controls**

| **SOC 2 CC8 Criteria** | **AWS Service** | **Control Objective** | **Evidence Examples** |
|--------------------------|----------------|------------------------|------------------------|
| CC8.1 - Authorized and tested changes | CodePipeline | Ensure only approved and tested changes are deployed. | Pipeline execution logs, approval actions |
| CC8.1 & CC8.2 - Configuration integrity maintained | Config | Detect and prevent unauthorized or noncompliant changes. | Config rule evaluation reports |
| CC8.3 - Unauthorized changes prevented | CloudTrail | Log and monitor all configuration and deployment changes. | CloudTrail audit logs, alert reports |

---

## **GRC Team Implementation Guidance**
GRC teams preparing for SOC 2 audits should:
1. **Define Control Owners:** Assign AWS account owners for each service.  
2. **Centralize Evidence:** Export and archive CloudTrail, Config, and CodePipeline logs quarterly.  
3. **Maintain Policy Documents:** Include AWS change control policies in the SOC 2 evidence binder.  
4. **Perform Periodic Reviews:** Validate that AWS Config and CodePipeline workflows are active and functioning as intended.  
5. **Link to Risk Register:** Map changes identified through AWS Config or CloudTrail to corresponding risk entries.  

---

## **References**
- AICPA Trust Services Criteria (2017, TSP Section 100) — CC8: Change Management  
- AWS Security and Compliance Whitepapers  
- AWS Documentation for CodePipeline, Config, and CloudTrail  
- COSO Internal Control — Integrated Framework (2013)  

---
