# Risk Assessment Template

Template for conducting SOC 2 risk assessments on AWS infrastructure.

## Risk Assessment Overview

**Assessment Date**: [Date]  
**Assessment Period**: [Start Date] - [End Date]  
**Conducted By**: [Name/Team]  
**Reviewed By**: [Name]  
**Approved By**: [Leadership Name]  

**Scope**: [Description of systems, applications, and data in scope]

---

## Risk Rating Matrix

### Likelihood Scale
- **1 - Rare**: May occur only in exceptional circumstances (< 5% probability)
- **2 - Unlikely**: Could occur at some time (5-25% probability)
- **3 - Possible**: Might occur at some time (25-50% probability)
- **4 - Likely**: Will probably occur in most circumstances (50-75% probability)
- **5 - Almost Certain**: Expected to occur in most circumstances (> 75% probability)

### Impact Scale
- **1 - Negligible**: Minimal impact, easily absorbed
- **2 - Minor**: Some impact, manageable within normal operations
- **3 - Moderate**: Noticeable impact, requires management attention
- **4 - Major**: Significant impact, requires immediate attention
- **5 - Severe**: Critical impact, threatens organization viability

### Risk Score Calculation
**Risk Score = Likelihood × Impact**

### Risk Levels
- **Low (1-4)**: Accept or monitor
- **Medium (5-9)**: Mitigate or transfer
- **High (10-14)**: Mitigate immediately
- **Critical (15-25)**: Urgent mitigation required

---

## Risk Register

### Category: Infrastructure & Availability

#### RISK-001: AWS Region Outage

**Description**: Complete outage of primary AWS region affecting all services.

**Current Controls**:
- Multi-AZ deployment within region
- CloudWatch monitoring and alerting
- AWS Health Dashboard notifications

**Likelihood**: 2 (Unlikely)  
**Impact**: 5 (Severe)  
**Risk Score**: 10 (High)  
**Risk Level**: **High**

**Treatment Plan**:
- Implement multi-region architecture
- Set up cross-region replication
- Conduct quarterly DR tests
- Target: Reduce to Medium (Score 6-8)

**Residual Risk**: Medium (after treatment)  
**Owner**: Operations Team  
**Target Date**: Q2 2024  
**Status**: In Progress

---

#### RISK-002: Data Loss

**Description**: Permanent loss of customer data due to deletion, corruption, or disaster.

**Current Controls**:
- AWS Backup with 35-day retention
- S3 versioning enabled
- RDS automated backups
- Cross-region backup replication
- Quarterly restore testing

**Likelihood**: 1 (Rare)  
**Impact**: 5 (Severe)  
**Risk Score**: 5 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Implement S3 Object Lock (immutable backups)
- Enable Backup Vault Lock
- Increase backup retention to 90 days
- Monthly restore testing
- Target: Maintain Medium or reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Operations Team  
**Target Date**: Q1 2024  
**Status**: Planned

---

### Category: Security & Access Control

#### RISK-003: Unauthorized Access to Production

**Description**: Unauthorized individual gains access to production AWS account or resources.

**Current Controls**:
- IAM with least privilege
- MFA required for all users
- CloudTrail logging all access
- GuardDuty threat detection
- Quarterly access reviews
- No standing administrative access

**Likelihood**: 2 (Unlikely)  
**Impact**: 4 (Major)  
**Risk Score**: 8 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Implement just-in-time access with break-glass procedures
- Add behavioral analytics
- Require hardware MFA for privileged access
- Weekly access reviews for privileged accounts
- Target: Reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Security Team  
**Target Date**: Q2 2024  
**Status**: In Progress

---

#### RISK-004: Data Breach / Exfiltration

**Description**: Sensitive customer data is accessed and exfiltrated by attacker.

**Current Controls**:
- Data encrypted at rest (KMS)
- Data encrypted in transit (TLS)
- VPC with private subnets
- Security groups (least privilege)
- WAF protecting web applications
- GuardDuty monitoring for anomalies
- VPC Flow Logs
- CloudTrail logging all data access
- Macie scanning for sensitive data exposure

**Likelihood**: 2 (Unlikely)  
**Impact**: 5 (Severe)  
**Risk Score**: 10 (High)  
**Risk Level**: **High**

**Treatment Plan**:
- Implement DLP controls
- Add data classification tags
- Restrict data access by classification
- Implement AWS Network Firewall
- Add AWS Detective for investigations
- Enhanced monitoring for data egress
- Target: Reduce to Medium

**Residual Risk**: Medium (after treatment)  
**Owner**: Security Team  
**Target Date**: Q3 2024  
**Status**: Planned

---

#### RISK-005: Compromised Credentials

**Description**: User or service credentials are compromised and used maliciously.

**Current Controls**:
- MFA for all human users
- IAM roles for services (no long-term credentials)
- Secrets Manager with rotation
- Password policy (14+ chars, 90-day rotation)
- Failed login monitoring
- CloudTrail logging credential usage

**Likelihood**: 3 (Possible)  
**Impact**: 4 (Major)  
**Risk Score**: 12 (High)  
**Risk Level**: **High**

**Treatment Plan**:
- Implement credential abuse detection
- Require hardware MFA for production access
- 30-day secret rotation for critical systems
- Automated credential rotation
- Session timeouts (12 hours)
- Target: Reduce to Medium

**Residual Risk**: Medium (after treatment)  
**Owner**: Security Team  
**Target Date**: Q2 2024  
**Status**: In Progress

---

### Category: Compliance & Legal

#### RISK-006: SOC 2 Audit Failure

**Description**: Failure to achieve or maintain SOC 2 Type II certification.

**Current Controls**:
- Comprehensive control implementation
- Continuous evidence collection
- Monthly compliance reviews
- Quarterly internal audits
- AWS Audit Manager for continuous monitoring
- Dedicated compliance resources

**Likelihood**: 2 (Unlikely)  
**Impact**: 4 (Major)  
**Risk Score**: 8 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Conduct readiness assessment before official audit
- Address any gaps identified
- Engage auditor early for guidance
- Implement GRC platform for continuous monitoring
- Target: Reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Compliance Team  
**Target Date**: Q1 2024  
**Status**: In Progress

---

#### RISK-007: GDPR/CCPA Non-Compliance

**Description**: Violation of GDPR or CCPA privacy requirements.

**Current Controls**:
- Privacy policy published and followed
- Consent collection at signup
- Data subject rights APIs (access, deletion)
- Data retention policies
- DPA with third-party vendors
- Privacy by design in development

**Likelihood**: 2 (Unlikely)  
**Impact**: 4 (Major)  
**Risk Score**: 8 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Conduct Privacy Impact Assessment
- Implement automated retention enforcement
- Add consent management platform
- Regular privacy training for developers
- Legal review of privacy practices
- Target: Reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Legal / Compliance  
**Target Date**: Q2 2024  
**Status**: Planned

---

### Category: Operations

#### RISK-008: Failed Deployment / Change

**Description**: Code or infrastructure deployment causes outage or data corruption.

**Current Controls**:
- Change approval process
- Infrastructure as Code (version control)
- CI/CD with automated testing
- Blue/green deployments
- Automated rollback on failure
- Config tracking changes

**Likelihood**: 3 (Possible)  
**Impact**: 3 (Moderate)  
**Risk Score**: 9 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Add canary deployments
- Implement feature flags
- Enhanced smoke testing
- Staging environment mirrors production
- Mandatory change windows
- Target: Reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Development / DevOps  
**Target Date**: Q2 2024  
**Status**: In Progress

---

#### RISK-009: Insufficient Logging / Monitoring

**Description**: Security incident or system issue not detected due to inadequate logging.

**Current Controls**:
- CloudTrail enabled (all regions)
- CloudWatch Logs for applications
- VPC Flow Logs
- GuardDuty for threat detection
- Security Hub for finding aggregation
- Automated alerting on critical events

**Likelihood**: 2 (Unlikely)  
**Impact**: 3 (Moderate)  
**Risk Score**: 6 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Implement AWS X-Ray for all services
- Add CloudWatch Synthetics for availability
- Log aggregation and correlation
- SIEM integration (optional)
- Defined log retention (1+ years)
- Target: Reduce to Low

**Residual Risk**: Low (after treatment)  
**Owner**: Operations Team  
**Target Date**: Q3 2024  
**Status**: Planned

---

### Category: Third-Party / Vendor

#### RISK-010: Vendor Security Breach

**Description**: Security breach at third-party vendor compromises our data or systems.

**Current Controls**:
- Vendor risk assessment process
- SOC 2 reports required from critical vendors
- Data Processing Agreements
- Limited data sharing
- Vendor inventory maintained
- Annual vendor reviews

**Likelihood**: 2 (Unlikely)  
**Impact**: 4 (Major)  
**Risk Score**: 8 (Medium)  
**Risk Level**: **Medium**

**Treatment Plan**:
- Enhanced vendor due diligence
- Continuous vendor monitoring
- Contractual security requirements
- Vendor breach notification requirements
- Alternative vendor identification
- Target: Maintain Medium (external risk)

**Residual Risk**: Medium (inherent external risk)  
**Owner**: Procurement / Legal  
**Target Date**: Ongoing  
**Status**: In Progress

---

## Risk Summary

| Risk Level | Count | Percentage |
|-----------|-------|------------|
| Critical (15-25) | 0 | 0% |
| High (10-14) | 3 | 30% |
| Medium (5-9) | 7 | 70% |
| Low (1-4) | 0 | 0% |
| **Total** | **10** | **100%** |

---

## Treatment Summary

| Treatment Strategy | Count | Percentage |
|-------------------|-------|------------|
| Mitigate | 9 | 90% |
| Accept | 1 | 10% |
| Transfer | 0 | 0% |
| Avoid | 0 | 0% |

---

## Action Plan

### Q1 2024
- [ ] Complete backup immutability implementation (RISK-002)
- [ ] Conduct SOC 2 readiness assessment (RISK-006)

### Q2 2024
- [ ] Implement multi-region architecture (RISK-001)
- [ ] Deploy just-in-time access (RISK-003)
- [ ] Implement hardware MFA for production (RISK-005)
- [ ] Conduct Privacy Impact Assessment (RISK-007)
- [ ] Enhance deployment safety (RISK-008)

### Q3 2024
- [ ] Implement DLP controls (RISK-004)
- [ ] Enhance observability (RISK-009)

### Ongoing
- [ ] Continuous vendor monitoring (RISK-010)

---

## Next Review

**Scheduled Date**: [Date + 1 Year]  
**Frequency**: Annual (with quarterly updates for new risks)

---

## Approval

**Prepared By**: [Name] ________________  Date: __________  
**Reviewed By**: [Name] ________________  Date: __________  
**Approved By**: [Leadership] __________  Date: __________  

---

## Notes

- This risk assessment should be reviewed annually
- New risks should be added as they're identified
- Risk scores should be re-evaluated quarterly
- Treatment plans should be tracked to completion
- Residual risk should be reviewed after treatment implementation

[← Back to Implementation Guide](../implementation-guide.md) | [Back to Main Guide](../../README.md)
