# Glossary

Comprehensive glossary of SOC 2 and AWS terms.

## SOC 2 Terms

### A

**AICPA**  
American Institute of Certified Public Accountants. The organization that defines SOC 2 standards.

**Attestation**  
A formal statement or declaration by a CPA regarding the effectiveness of controls.

**Audit Period**  
The timeframe over which controls are tested (typically 6-12 months for SOC 2 Type II).

**Auditor**  
Independent CPA who conducts the SOC 2 examination.

**Availability**  
One of the five Trust Service Categories. Ensures systems are available for operation as committed.

### C

**Common Criteria (CC)**  
Foundational controls that apply to all SOC 2 audits, covering control environment, communications, risk assessment, monitoring, control activities, access controls, operations, change management, and risk mitigation.

**Compensating Control**  
An alternative control that reduces the risk when the primary control cannot be implemented.

**Confidentiality**  
One of the five Trust Service Categories. Protects information designated as confidential.

**Control**  
A safeguard or countermeasure designed to meet control objectives and mitigate risk.

**Control Environment**  
The organizational structure, culture, and practices that create a foundation for carrying out internal control.

**Control Objective**  
A specific target or aim for internal control.

**CPA**  
Certified Public Accountant. Professional licensed to conduct SOC 2 audits.

### D

**Data Subject**  
An individual whose personal data is collected, processed, or stored.

**DSAR**  
Data Subject Access Request. A request by an individual to access their personal data (common in privacy regulations).

### E

**Evidence**  
Documentation, screenshots, logs, or other records demonstrating control operation.

**Exception**  
An instance where a control did not operate as designed.

### G

**GAPP**  
Generally Accepted Privacy Principles. Framework for privacy controls.

### I

**In-scope System**  
The systems, applications, and infrastructure included in the SOC 2 audit.

### M

**Management Assertion**  
Statement by management describing their system and the suitability of controls.

**Material Weakness**  
A significant deficiency or combination of significant deficiencies that results in more than a remote likelihood that a material misstatement will not be prevented or detected.

### O

**Observation Period**  
See Audit Period.

### P

**Privacy**  
One of the five Trust Service Categories. Addresses personal information handling.

**Processing Integrity**  
One of the five Trust Service Categories. Ensures processing is complete, valid, accurate, timely, and authorized.

### R

**Report Date**  
The date the SOC 2 report is issued.

**Risk**  
The possibility that an event will occur and adversely affect the achievement of objectives.

**Risk Assessment**  
The process of identifying, analyzing, and responding to risk.

**RPO (Recovery Point Objective)**  
The maximum acceptable amount of data loss measured in time.

**RTO (Recovery Time Objective)**  
The maximum acceptable amount of time to restore a system after a failure.

### S

**Scope**  
The boundaries of the SOC 2 examination, including systems, applications, and Trust Service Categories.

**Service Organization**  
The entity being audited (your company).

**Significant Deficiency**  
A deficiency or combination of deficiencies in internal control that is less severe than a material weakness yet important enough to merit attention.

**SOC 2**  
Service Organization Control 2. An auditing procedure for service providers storing customer data.

**SOC 2 Type I**  
Audit of controls at a specific point in time.

**SOC 2 Type II**  
Audit of controls over a period of time (6-12 months), demonstrating operational effectiveness.

**System Description**  
A document describing the organization's system, including infrastructure, software, people, procedures, and data.

### T

**Trust Service Category**  
One of five categories of controls: Security (Common Criteria), Availability, Processing Integrity, Confidentiality, or Privacy.

**Trust Service Criteria (TSC)**  
Specific criteria within each Trust Service Category that must be met.

**Type I vs Type II**  
Type I audits controls at a point in time; Type II audits operational effectiveness over time.

### U

**User Entity**  
Your customers or clients who use your services.

**User Entity Controls**  
Controls that your customers must implement for the system to be secure (complementary controls).

---

## AWS Terms

### A

**ACL (Access Control List)**  
Network ACL: Stateless firewall at the subnet level. S3 ACL: Legacy access control method for S3.

**ACM (AWS Certificate Manager)**  
Service for provisioning, managing, and deploying SSL/TLS certificates.

**ALB (Application Load Balancer)**  
Layer 7 load balancer that routes HTTP/HTTPS traffic.

**AMI (Amazon Machine Image)**  
Template for launching EC2 instances.

**API Gateway**  
Managed service for creating, deploying, and managing APIs.

**ARN (Amazon Resource Name)**  
Unique identifier for AWS resources. Format: `arn:aws:service:region:account-id:resource`

**Auto Scaling**  
Service that automatically adjusts compute capacity based on demand.

**Availability Zone (AZ)**  
Isolated location within an AWS Region with independent power, cooling, and networking.

### B

**Backup**  
AWS Backup: Centralized backup service across AWS services.

### C

**CloudFormation**  
Infrastructure as Code service for provisioning AWS resources.

**CloudFront**  
Content Delivery Network (CDN) for distributing content globally.

**CloudTrail**  
Service that logs all API calls made in your AWS account for auditing.

**CloudWatch**  
Monitoring and observability service providing metrics, logs, and alarms.

**CMK (Customer Master Key)**  
Primary resource in AWS KMS (now called KMS key).

**Cognito**  
Service for user authentication, authorization, and user management.

**Config**  
Service that tracks resource configurations and evaluates compliance.

**Cost Explorer**  
Tool for analyzing and visualizing AWS costs.

### D

**Detective**  
Service for investigating security findings and potential security issues.

**DLM (Data Lifecycle Manager)**  
Service for automating EBS snapshot lifecycle.

**DLQ (Dead Letter Queue)**  
Queue for messages that cannot be processed successfully.

**DynamoDB**  
Fully managed NoSQL database service.

### E

**EBS (Elastic Block Store)**  
Block storage for EC2 instances.

**EC2 (Elastic Compute Cloud)**  
Virtual server hosting service.

**ECR (Elastic Container Registry)**  
Docker container registry.

**ECS (Elastic Container Service)**  
Container orchestration service.

**EFS (Elastic File System)**  
Managed file storage for EC2.

**EKS (Elastic Kubernetes Service)**  
Managed Kubernetes service.

**ELB (Elastic Load Balancing)**  
Load balancing service (includes ALB, NLB, CLB).

**EventBridge**  
Serverless event bus for routing events between services.

### G

**Glacier**  
Low-cost archival storage class in S3.

**Glue**  
ETL (Extract, Transform, Load) service for data preparation.

**GuardDuty**  
Intelligent threat detection service.

### I

**IAM (Identity and Access Management)**  
Service for managing access to AWS resources.

**IAM Identity Center (formerly AWS SSO)**  
Centralized single sign-on service.

**IAM Policy**  
JSON document defining permissions.

**IAM Role**  
Set of permissions that can be assumed by users, applications, or services.

**Inspector**  
Automated security assessment service for vulnerabilities and deviations.

### K

**Kinesis**  
Service for real-time data streaming.

**KMS (Key Management Service)**  
Managed service for creating and controlling encryption keys.

### L

**Lambda**  
Serverless compute service for running code without managing servers.

### M

**Macie**  
Data security service for discovering, classifying, and protecting sensitive data.

**MFA (Multi-Factor Authentication)**  
Additional authentication factor beyond username and password.

**Multi-AZ**  
Deployment across multiple Availability Zones for high availability.

### N

**Network Firewall**  
Managed firewall service for VPCs.

**NLB (Network Load Balancer)**  
Layer 4 load balancer for TCP/UDP traffic.

### O

**OpsCenter**  
Systems Manager feature for operational issue management.

**Organizations**  
Service for centrally managing multiple AWS accounts.

### P

**Parameter Store**  
Systems Manager feature for storing configuration data and secrets.

**PrivateLink**  
Service for private connectivity between VPCs and services.

### R

**RDS (Relational Database Service)**  
Managed relational database service (MySQL, PostgreSQL, etc.).

**Region**  
Geographic area containing multiple Availability Zones.

**Resource**  
An AWS entity you can work with (EC2 instance, S3 bucket, etc.).

**Route 53**  
DNS web service.

### S

**S3 (Simple Storage Service)**  
Object storage service.

**S3 Glacier**  
See Glacier.

**SCP (Service Control Policy)**  
Policy in AWS Organizations that controls service usage.

**Secret**  
Confidential information stored in Secrets Manager.

**Secrets Manager**  
Service for managing, retrieving, and rotating secrets.

**Security Group**  
Virtual firewall for EC2 instances (stateful).

**Security Hub**  
Service for aggregating and prioritizing security findings.

**SES (Simple Email Service)**  
Email sending and receiving service.

**Shield**  
DDoS protection service. Shield Standard is free; Shield Advanced is paid.

**SNS (Simple Notification Service)**  
Pub/sub messaging service.

**SQS (Simple Queue Service)**  
Message queuing service.

**SSE (Server-Side Encryption)**  
Encryption of data at rest in S3.

**SSO (Single Sign-On)**  
See IAM Identity Center.

**Step Functions**  
Service for orchestrating distributed applications and microservices.

**Subnet**  
Range of IP addresses in a VPC.

**Systems Manager**  
Service for operational insights and task automation.

### T

**Tag**  
Key-value pair for labeling AWS resources.

**Terraform**  
Third-party Infrastructure as Code tool (not AWS, but commonly used).

**Trusted Advisor**  
Service providing best practice recommendations.

**TTL (Time To Live)**  
DynamoDB feature for automatically deleting expired items.

### V

**VPC (Virtual Private Cloud)**  
Isolated network within AWS.

**VPC Endpoint**  
Private connection from VPC to AWS services.

**VPC Flow Logs**  
Logs of network traffic in VPC.

**VPN (Virtual Private Network)**  
Encrypted connection between your network and AWS.

### W

**WAF (Web Application Firewall)**  
Firewall for protecting web applications.

**Well-Architected Framework**  
AWS framework for evaluating architectures.

### X

**X-Ray**  
Distributed tracing service for debugging applications.

---

## Compliance Terms

### C

**CCPA (California Consumer Privacy Act)**  
California privacy law giving consumers rights over their data.

**CIS (Center for Internet Security)**  
Organization that publishes security best practices. CIS Benchmarks are security configuration guides.

**Cryptographic Erasure**  
Deleting encryption keys to make encrypted data unrecoverable.

### F

**FIPS 140-2**  
U.S. government security standard for cryptographic modules.

### G

**GDPR (General Data Protection Regulation)**  
EU privacy regulation protecting personal data.

**GRC (Governance, Risk, and Compliance)**  
Integrated approach to managing governance, risk management, and compliance.

### H

**HIPAA (Health Insurance Portability and Accountability Act)**  
U.S. law for protecting health information.

### I

**ISO 27001**  
International standard for information security management.

### N

**NIST (National Institute of Standards and Technology)**  
U.S. agency that publishes cybersecurity frameworks and standards.

### P

**PCI-DSS (Payment Card Industry Data Security Standard)**  
Security standard for organizations handling credit card data.

**PII (Personally Identifiable Information)**  
Information that can identify an individual.

**PHI (Protected Health Information)**  
Health information covered by HIPAA.

### S

**SSAE 18**  
Statement on Standards for Attestation Engagements No. 18. U.S. auditing standard for SOC reports.

---

## Architecture Terms

### H

**High Availability (HA)**  
System design that ensures agreed level of operational performance.

### I

**IaC (Infrastructure as Code)**  
Managing infrastructure through code and automation.

**Idempotency**  
Property where an operation produces the same result regardless of how many times it's executed.

### M

**Microservices**  
Architectural style where application is composed of small, independent services.

### S

**Stateful**  
Retains information about previous interactions (e.g., Security Groups).

**Stateless**  
Does not retain information about previous interactions (e.g., Network ACLs).

---

## Security Terms

### L

**Least Privilege**  
Principle of granting minimum permissions necessary.

### M

**MFA (Multi-Factor Authentication)**  
Authentication using two or more factors.

### Z

**Zero Trust**  
Security model that assumes no implicit trust and verifies every request.

---

[← Back to Quick Reference](quick-reference.md) | [View FAQ →](faq.md) | [Back to Main Guide](../README.md)
