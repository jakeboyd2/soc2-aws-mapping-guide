# CC2: Communication and Information

## Overview

**SOC 2 Criteria Reference:** CC2 – Communication and Information  
These criteria focus on how an organization obtains, generates, and uses relevant, quality information to support the operation of internal controls and effective communication throughout the entity.  
The goal is to ensure that critical security information flows between systems, teams, and stakeholders in a timely, accurate, and actionable manner.

GRC (Governance, Risk, and Compliance) teams can leverage AWS services like **Security Hub**, **SNS**, and **CloudWatch** to automate communication and strengthen information integrity across environments.

---

## Control: CC2.1 – Communication of Security Events (AWS Security Hub)

**Description:**  
The entity uses AWS Security Hub to centralize and communicate security findings across AWS accounts and regions, ensuring that information about potential security events is shared with relevant stakeholders.

**Implementation Steps:**
1. **Enable Security Hub** across all accounts and regions.  
2. **Integrate AWS Config, GuardDuty, and Inspector** for continuous findings.  
3. **Aggregate findings** into a centralized Security Hub administrator account.  
4. **Define notification channels** (e.g., Slack, email) via SNS for critical findings.  
5. **Establish review cadence** (e.g., weekly) for triaging and tracking remediation.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC2.1, CC7.2  
- **Objective:** Security information is communicated promptly and effectively to those responsible for corrective actions.  
- **Evidence Examples:**  
  - Security Hub findings report  
  - Notification configuration policy  
  - Incident response logs and meeting notes  

---

## Control: CC2.2 – Communication Channels for Alerts (AWS SNS)

**Description:**  
The entity uses **Amazon Simple Notification Service (SNS)** to deliver real-time security alerts and system notifications to appropriate teams and tools, ensuring that critical information is not delayed or lost.

**Implementation Steps:**
1. **Create SNS topics** (e.g., `security-alerts`, `audit-notifications`).  
2. **Subscribe security, operations, and compliance teams** to relevant topics via email, webhook, or Lambda.  
3. **Integrate SNS with Security Hub and CloudWatch alarms** for automated delivery.  
4. **Maintain subscriber list** and review it quarterly to ensure accuracy.  
5. **Document notification workflows** in incident response and communication plans.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC2.2, CC2.3  
- **Objective:** Internal and external communications support timely action on identified risks.  
- **Evidence Examples:**  
  - SNS topic configuration JSON  
  - Subscription policy review logs  
  - Incident response communication records  

---

## Control: CC2.3 – Monitoring and Information Feedback (AWS CloudWatch)

**Description:**  
The entity uses **Amazon CloudWatch** to collect and communicate performance and security metrics, enabling timely detection and resolution of system anomalies that could affect security and availability.

**Implementation Steps:**
1. **Enable CloudWatch metrics and logs** for all production resources.  
2. **Define alarms** for threshold breaches (e.g., CPU usage, API failures, unauthorized access).  
3. **Route CloudWatch alarms** to SNS for notification to GRC and operations teams.  
4. **Create dashboards** to visualize trends for audit and executive reporting.  
5. **Review alarm configurations quarterly** to align with current risk posture.  

**Mapping to SOC 2:**  
- **TSC Reference:** CC2.3, CC7.1, A1.2  
- **Objective:** Relevant system information is captured and communicated to management for action.  
- **Evidence Examples:**  
  - CloudWatch alarm configuration exports  
  - Notification history logs  
  - CloudWatch dashboards or PDF exports for audit period  

---

## GRC Mapping Guidance

| AWS Service | SOC 2 CC2 Criterion | Control Objective | Audit Evidence |
|--------------|---------------------|-------------------|----------------|
| **Security Hub** | CC2.1 | Centralized communication of security findings | Security Hub reports, notifications |
| **SNS** | CC2.2 | Real-time delivery of alerts to stakeholders | SNS topic configs, subscription logs |
| **CloudWatch** | CC2.3 | Monitoring and escalation of system anomalies | Alarm configs, CloudWatch dashboards |

---

### Summary

By implementing **Security Hub**, **SNS**, and **CloudWatch** in alignment with SOC 2 CC2, organizations can ensure:
- Consistent communication of critical information  
- Timely escalation of issues to the right people  
- Evidence-driven audit readiness through automated logs and reports  

