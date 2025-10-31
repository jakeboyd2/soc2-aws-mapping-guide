# SOC 2 Pre-Audit Readiness Checklist

## Purpose
Complete this checklist 2–4 weeks before your SOC 2 audit kickoff to ensure you are fully prepared.  
This checklist assumes year-round evidence collection has been performed in accordance with the evidence collection guide.

---

## Timeline: 4 Weeks Before Audit

### Week 1: Evidence Review and Gap Analysis

#### Step 1: Validate Automated Evidence Collection
- [ ] Confirm evidence was consistently collected for the full audit period  
- [ ] Review S3 evidence repository for completeness and naming accuracy  
- [ ] Verify daily/weekly evidence snapshots are intact  
- [ ] Identify and document any missing or incomplete evidence  

#### Step 2: Control-by-Control Evidence Validation

**CC1 – Control Environment**
- [ ] Current organization chart (updated within last 3 months)  
- [ ] Approved information security and governance policies  
- [ ] Board meeting minutes documenting security oversight  
- [ ] Security awareness training completion records  
- [ ] Background check documentation for all hires during the audit period  

**CC6 – Logical and Physical Access Controls**
- [ ] IAM credential report for last day of audit period  
- [ ] Quarterly access reviews with documented approvals  
- [ ] Onboarding/offboarding evidence (25 samples each)  
- [ ] MFA enforcement and identity provider configurations  
- [ ] Root account access logs and restrictions  

**CC7 – System Operations**
- [ ] AWS Security Hub dashboards showing continuous monitoring  
- [ ] GuardDuty findings and remediation documentation  
- [ ] Vulnerability scan reports covering the full audit period  
- [ ] Sample incident response tickets with full lifecycle documentation  
- [ ] Monthly patch compliance summaries  

**CC8 – Change Management**
- [ ] Approved change management policy and procedures  
- [ ] Change tickets and approvals for 25 sampled changes  
- [ ] Configuration timelines for infrastructure modifications  
- [ ] Emergency change examples and review evidence  

**A1 – Availability**
- [ ] Backup success rate and retention validation (>99%)  
- [ ] Disaster recovery test results (quarterly)  
- [ ] RTO/RPO documentation and business impact alignment  
- [ ] AWS CloudWatch dashboards for capacity and availability monitoring  

#### Step 3: Address Missing Evidence
- [ ] Collect any missing screenshots, reports, or documentation  
- [ ] Note any evidence that cannot be regenerated (e.g., historical IAM state)  
- [ ] Document compensating controls and improvement actions  

---

### Week 2: Documentation and Narrative Preparation

#### System Description
Ensure `/documentation/system-description.md` is complete and current:
- [ ] Architecture and infrastructure overview diagram  
- [ ] Defined system boundaries and in-scope services  
- [ ] Data flow diagrams (including AWS components)  
- [ ] Third-party vendor list and dependency mapping  
- [ ] Inventory of key technologies and tools  

#### Control Narratives
Prepare narratives for each control area describing:
1. Purpose of the control  
2. How it is implemented (AWS, process, or tool)  
3. Responsible owner(s)  
4. How effectiveness is validated  

**Example: CC6.1 – Logical Access Security**
- Access restricted via AWS IAM and SSO (MFA required)  
- Least-privilege principles enforced via IAM roles  
- IAM Access Analyzer monitors for excessive permissions  
- Access reviewed quarterly by data owners  

#### Third-Party Risk Documentation
- [ ] Vendor inventory with access classifications  
- [ ] SOC 2 or equivalent assurance reports from critical vendors (AWS, etc.)  
- [ ] Vendor risk assessments and questionnaires  
- [ ] Security clauses in contracts verified  
- [ ] Annual vendor access review completed  

**Critical Vendors to Include:**
- AWS (SOC 2 report via AWS Artifact)  
- GitHub / GitLab  
- Datadog or monitoring provider  
- Payment processors  
- Email service provider (SES, SendGrid, etc.)  
- Identity provider (Okta, Auth0, etc.)  

#### Incident Response
- [ ] Current incident response policy and procedures  
- [ ] Incident response team contact roster  
- [ ] Tabletop exercise results (annual)  
- [ ] Documented playbooks/runbooks for common scenarios  

---

### Week 3: Sampling Preparation

#### Access Management (CC6)
Prepare lists of:
- All IAM user and role creations during the audit period  
- All IAM user and role deletions during the audit period  

For each sampled user:
- [ ] HR access request ticket  
- [ ] Manager approval evidence  
- [ ] IAM creation/deletion event record  
- [ ] Role and permission assignments  
- [ ] Most recent access review evidence  

#### Change Management (CC8)
Prepare population lists of infrastructure and application changes:
- [ ] Change ticket and CAB approval  
- [ ] CloudTrail or Config change event  
- [ ] Configuration snapshot before/after change  
- [ ] Test results and deployment validation evidence  

---

### Week 4: Final Audit Readiness

#### Audit Logistics
- [ ] Schedule and confirm audit kickoff meeting  
- [ ] Reserve meeting spaces or establish virtual audit environment  
- [ ] Create a dedicated audit communication channel (Slack/Teams)  
- [ ] Set up secure evidence-sharing folder for auditor requests  
- [ ] Confirm VPN or access methods for auditors (if required)  
- [ ] Publish audit calendar with milestones and responsibilities  

#### Stakeholder Preparation
Brief all relevant teams:
- [ ] Engineering and operations (technical interviews, evidence walkthroughs)  
- [ ] HR (onboarding/offboarding process discussion)  
- [ ] Finance (transaction controls if applicable)  
- [ ] Customer support (data handling processes)  

**Key reminders:**
- Auditors may request direct interviews — honesty and clarity are expected  
- Redirect technical questions to designated SMEs  
- Reinforce that audits validate process maturity, not blame  

#### Audit Response Team Setup
- [ ] Assign primary contact for auditor communication  
- [ ] Confirm availability of all Subject Matter Experts (SMEs)  
- [ ] Establish rapid-response process for auditor requests  
- [ ] Maintain centralized evidence and documentation access  

---

## Final Evidence Package Assembly

**Objective:**  
Compile all documentation and evidence in clearly labeled folders for auditor access.

**Packages to Assemble:**

### Package 1: Company Overview
- [ ] Organization chart  
- [ ] System description document  
- [ ] Network and data flow diagrams  
- [ ] Technology inventory  
- [ ] Vendor list  

### Package 2: Policies and Procedures
- [ ] Information Security Policy  
- [ ] Access Control Policy  
- [ ] Change Management Policy  
- [ ] Incident Response Policy  
- [ ] Business Continuity Policy  
- [ ] Acceptable Use Policy  
- [ ] Data Classification Policy  

### Package 3: Control Matrices
- [ ] SOC 2 control matrix (criteria → controls → evidence)  
- [ ] Risk assessment documentation  
- [ ] Control test results summary  

### Package 4: Access Control Evidence
- [ ] IAM credential reports (quarterly)  
- [ ] Access review results  
- [ ] Onboarding/offboarding tickets (sampled)  
- [ ] MFA configuration screenshots  

### Package 5: System Operations Evidence
- [ ] Security Hub dashboards  
- [ ] Vulnerability scan results  
- [ ] Patch compliance reports  
- [ ] Sample security incidents with response documentation  

### Package 6: Change Management Evidence
- [ ] Change management procedures  
- [ ] Sample change tickets (25)  
- [ ] Configuration timelines  
- [ ] Emergency change documentation  

### Package 7: Availability Evidence
- [ ] Backup configurations  
- [ ] Disaster recovery test results  
- [ ] RTO/RPO documentation  
- [ ] Capacity monitoring dashboards  

---

## Auditor FAQ Document

**File:** `/documentation/auditor-faq.md`  
**Purpose:** Anticipate and prepare responses to common auditor questions.

### Example Q&A

#### Access Control
**Q:** How do you ensure MFA is enforced?  
**A:** IAM Identity Center enforces MFA at authentication. Evidence is provided in Package 4 (CC6.1-MFA-config screenshots).

**Q:** How often do you review access?  
**A:** Quarterly. Access review records are included in Package 4 by quarter.

#### System Operations
**Q:** How do you detect security incidents?  
**A:** GuardDuty, Security Hub, and CloudWatch alarms provide layered detection. See Package 5 for monitoring configuration and incidents.

**Q:** Walk me through a recent security incident.  
**A:** See Package 5, “Sample-Incidents” folder for full lifecycle documentation.

#### Change Management
**Q:** How do you manage emergency changes?  
**A:** Emergency changes require VP approval and are reviewed post-implementation. Procedures and evidence are in Package 6.

**Q:** How do you detect unauthorized changes?  
**A:** AWS Config monitors for configuration drift. Alerts trigger reviews by the security team. Config rules are included in Package 6.

#### Availability
**Q:** How do you verify your backups work?  
**A:** Backups are tested quarterly with documented restore validation. See Package 7 for DR test reports.

**Q:** What are your RTO/RPO objectives?  
**A:**  
- Critical systems: RTO 4h / RPO 1h  
- Standard systems: RTO 24h / RPO 24h  
- Non-critical systems: RTO 48h / RPO 48h  
See Package 7 for RTO/RPO matrix.

---

**End of SOC 2 Pre-Audit Readiness Checklist**
