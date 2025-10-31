# CC4: Monitoring Activities

## Overview

**SOC 2 Criteria Reference:** CC4 – Monitoring Activities  
These criteria focus on how an organization selects, develops, and performs ongoing and separate evaluations to ascertain whether internal controls are present and functioning.  
GRC (Governance, Risk, and Compliance) teams use monitoring mechanisms to identify control breakdowns, analyze deviations, and ensure timely remediation.

AWS services such as **CloudWatch**, **EventBridge**, and **Security Hub** enable organizations to automate monitoring activities, generate actionable alerts, and maintain an evidence trail aligned with SOC 2 requirements.

---

## Control: CC4.1 – Continuous Control Monitoring (AWS CloudWatch)

**Description:**  
The entity uses **Amazon CloudWatch** to continuously monitor system metrics, logs, and performance indicators to detect deviations from expected behavior that may impact the security, availability, or confidentiality of systems.

**Implementation Steps:**
1. **Enable CloudWatch metrics and logs** across all production environments.  
2. **Define key performance and security metrics** (e.g., API error rates, failed login attempts, CPU thresholds).  
3. **Set CloudWatch alarms** for threshold breaches and abnormal patterns.  
4. **Forward alerts** to Amazon SNS or EventBridge for automated notifications.  
5. **Create dashboards** for control owners and auditors to visualize trends and exceptions.  
6. **Review and tune alerts quarterly** to reflect changes in infrastructure and risks.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC4.1, CC7.2  
- **Objective:** Ongoing monitoring identifies control deficiencies in real time.  
- **Evidence Examples:**  
  - CloudWatch dashboards and alarm configurations  
  - SNS notification history  
  - Quarterly monitoring review logs  

---

## Control: CC4.2 – Automated Event Correlation and Response (AWS EventBridge)

**Description:**  
The entity uses **Amazon EventBridge** to correlate events across AWS services and initiate automated responses or workflows that support timely detection and remediation of control failures or incidents.

**Implementation Steps:**
1. **Create EventBridge rules** to capture specific CloudTrail, Security Hub, or Config events.  
2. **Define actions** (e.g., invoke Lambda functions or send SNS alerts) for remediation or notification.  
3. **Tag events** by severity and service to prioritize alerts for the GRC team.  
4. **Integrate EventBridge with ticketing systems** (e.g., Jira, ServiceNow) to log and track findings.  
5. **Periodically review event rules** to ensure continued relevance and alignment with audit scope.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC4.2, CC7.3  
- **Objective:** Separate and ongoing evaluations are automated to ensure timely response to deviations.  
- **Evidence Examples:**  
  - EventBridge rule configurations  
  - Event logs and automation scripts  
  - Change management records for rule updates  

---

## Control: CC4.3 – Security Findings Review and Governance (AWS Security Hub)

**Description:**  
The entity uses **AWS Security Hub** to aggregate and evaluate security findings from multiple AWS services (e.g., GuardDuty, Config, Inspector) to monitor the effectiveness of implemented controls.

**Implementation Steps:**
1. **Enable Security Hub** across all AWS accounts and link them to a central administrator account.  
2. **Ingest findings** from AWS-native and third-party security tools.  
3. **Set up automated rules** to prioritize and assign findings to responsible control owners.  
4. **Conduct weekly reviews** of Security Hub reports and remediation status.  
5. **Generate monthly compliance summaries** for GRC and audit teams.  
6. **Document false positives and exceptions** as part of continuous improvement.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC4.3, CC7.4  
- **Objective:** The organization evaluates control performance and communicates deficiencies to responsible parties.  
- **Evidence Examples:**  
  - Security Hub findings reports  
  - Remediation tracking dashboards  
  - Governance review meeting notes  

---

## GRC Mapping Guidance

| AWS Service | SOC 2 CC4 Criterion | Control Objective | Audit Evidence |
|--------------|---------------------|-------------------|----------------|
| **CloudWatch** | CC4.1 | Ongoing monitoring of system health and security | Dashboards, alarms, and logs |
| **EventBridge** | CC4.2 | Automated correlation and response to control deviations | Rule configurations, automation logs |
| **Security Hub** | CC4.3 | Aggregated review of security findings and governance oversight | Findings reports, review documentation |

---

### Summary

By leveraging **CloudWatch**, **EventBridge**, and **Security Hub**, GRC teams can:
- Automate continuous monitoring of control operations  
- Identify and respond to control deficiencies in near real-time  
- Maintain an audit-ready repository of monitoring evidence  

This approach strengthens operational resilience and supports compliance with **SOC 2 CC4 Monitoring Activities**.

**File Path:** `/controls/CC4-Monitoring-Activities.md`
