# CC3: Risk Assessment 
 
### Reference: AICPA Trust Services Criteria 2017 (CC3.1–CC3.4)

## Overview
The **Risk Assessment** principle (C3/CC3) requires that organizations identify, analyze, and manage risks that could affect the achievement of system objectives.  
According to the AICPA Trust Services Criteria (TSP Section 100, CC3 series), entities should:
- Specify objectives clearly (CC3.1)
- Identify and analyze risks (CC3.2)
- Consider fraud risk (CC3.3)
- Assess changes that impact controls (CC3.4)

AWS provides several managed security services that can automate continuous risk detection, vulnerability analysis, and configuration management — all core components of a strong SOC 2 risk assessment program.

---

## AWS Services Supporting Risk Assessment Controls

Below are **four controls** leveraging **Amazon GuardDuty**, **AWS Inspector**, and **AWS Config** to strengthen SOC 2 Risk Assessment alignment.

---

### **Control 1: Amazon GuardDuty – Continuous Threat Detection**
**SOC 2 Mapping:** CC3.2 – Identify and Analyze Risks  
**Description:**  
Amazon GuardDuty continuously monitors AWS accounts, workloads, and data sources for malicious or unauthorized activity.  
It detects anomalies such as privilege escalation, compromised credentials, and reconnaissance behavior.

**Implementation Guidance:**  
- Enable GuardDuty organization-wide via AWS Organizations.  
- Integrate findings into AWS Security Hub or SIEM tools for triage.  
- Define severity thresholds and incident response workflows.  

**GRC Mapping Insight:**  
GuardDuty supports **ongoing risk identification** and threat assessment.  
GRC teams can map GuardDuty findings and response documentation as **evidence of continuous risk analysis** under CC3.2.

---

### **Control 2: AWS Inspector – Vulnerability and Exposure Analysis**
**SOC 2 Mapping:** CC3.2 / CC3.4 – Assess Risks and Changes  
**Description:**  
AWS Inspector automatically scans EC2 instances, Lambda functions, and container workloads for known vulnerabilities and misconfigurations.  
It assesses risk severity and prioritizes remediation efforts based on exposure.

**Implementation Guidance:**  
- Activate Amazon Inspector for all AWS accounts and regions.  
- Integrate findings into Jira or ServiceNow for remediation tracking.  
- Schedule automated rescans after significant system or patching changes.  

**GRC Mapping Insight:**  
Inspector provides **quantitative vulnerability metrics** that directly support SOC 2 risk assessments.  
Its reports help GRC teams demonstrate compliance with CC3.4 by evidencing how system changes are assessed for new or increased risks.

---

### **Control 3: AWS Config – Configuration Compliance and Risk Monitoring**
**SOC 2 Mapping:** CC3.2 / CC3.4 – Risk Identification and Change Assessment  
**Description:**  
AWS Config continuously records configuration changes to AWS resources and evaluates them against defined compliance baselines or frameworks (e.g., CIS AWS Foundations Benchmark).

**Implementation Guidance:**  
- Enable Config in all accounts and aggregate data with AWS Config Aggregator.  
- Define Config Rules aligned to internal security baselines or SOC 2 policies.  
- Enable AWS Config Conformance Packs for automated compliance auditing.  

**GRC Mapping Insight:**  
Config supports **risk identification and tracking** by providing a history of configuration changes.  
For SOC 2 audits, Config reports demonstrate that the entity **monitors and evaluates changes** affecting system risk (CC3.4).

---

### **Control 4: Combined GuardDuty + Inspector + Config – Integrated Risk Visibility**
**SOC 2 Mapping:** CC3.2 / CC3.3 – Analyze and Correlate Risks, Including Fraud or Misuse  
**Description:**  
Integrating GuardDuty, Inspector, and Config into a unified Security Hub or GRC dashboard provides holistic visibility into operational risks, configuration drift, and security incidents.

**Implementation Guidance:**  
- Centralize all findings in AWS Security Hub or a GRC platform.  
- Correlate data between GuardDuty (threats), Inspector (vulnerabilities), and Config (compliance deviations).  
- Automate incident creation and escalation for high-severity findings.  

**GRC Mapping Insight:**  
The integrated view allows GRC teams to **correlate and prioritize risks** across multiple domains — technical, operational, and compliance.  
This supports evidence collection for CC3.2 and CC3.3 by demonstrating a structured and automated approach to risk analysis.

---

## GRC Audit Preparation Steps

1. **Define Risk Management Objectives**  
   Align AWS service configurations with enterprise risk appetite and SOC 2 objectives.  

2. **Document Control Ownership**  
   Assign control owners for GuardDuty, Inspector, and Config findings review processes.  

3. **Collect and Retain Evidence**  
   Export GuardDuty findings, Inspector reports, and Config compliance snapshots.  

4. **Integrate into GRC Systems**  
   Map AWS service outputs to risk registers and remediation workflows.  

5. **Perform Periodic Reviews**  
   Conduct quarterly risk reviews based on AWS security service outputs and update control mappings.  

---

## Summary Table

| AWS Service | SOC 2 Control | Primary Objective | Evidence for Auditors | Key Risk Function |
|--------------|----------------|-------------------|------------------------|--------------------|
| **GuardDuty** | CC3.2 | Continuous threat detection | GuardDuty findings, Security Hub reports | Identify and analyze security risks |
| **Inspector** | CC3.2 / CC3.4 | Vulnerability management | Inspector reports, remediation logs | Assess changes and system exposure |
| **Config** | CC3.2 / CC3.4 | Configuration monitoring | Config rule evaluations, compliance reports | Detect risk from configuration drift |
| **Integrated Services** | CC3.2 / CC3.3 | Unified risk visibility | Security Hub dashboards, audit reports | Correlate and prioritize risk indicators |

---

### References
- **AICPA Trust Services Criteria (TSP Section 100, CC3.1–CC3.4)**:contentReference[oaicite:1]{index=1}  
- **AWS Security Documentation:**  
  - [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/)  
  - [Amazon Inspector](https://docs.aws.amazon.com/inspector/latest/user/)  
  - [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/)  
  - [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/)  

---

**Prepared for:** GRC & Compliance Teams  
**Purpose:** SOC 2 Audit Preparation — C3 Risk Assessment Controls Mapped to AWS Services
