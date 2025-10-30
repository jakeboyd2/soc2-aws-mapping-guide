# Confidentiality Trust Service Criteria

The Confidentiality criteria addresses the protection of confidential information as committed or agreed upon with customers.

## Overview

Confidentiality ensures that:
- Information designated as confidential is protected
- Access to confidential data is restricted
- Confidential data is encrypted in transit and at rest
- Data handling meets contractual and regulatory requirements

---

## C1: Confidentiality

### C1.1 - Confidential Information Identification

**Control Description**: The entity identifies and maintains confidential information to meet the entity's objectives related to confidentiality.

**AWS Services**:
- **Amazon Macie** (Data discovery and classification)
- **AWS Resource Tags** (Data classification tagging)
- **AWS IAM** (Access based on classification)
- **AWS Organizations** (Policy-based controls)

**Implementation**:

1. **Data Classification Framework**:
   ```
   Classifications:
   - Public: No restrictions
   - Internal: Company employees only
   - Confidential: Authorized personnel only
   - Restricted: Highest sensitivity
   ```

2. **AWS Tagging Strategy**:
   ```
   Required tags for all resources:
   - DataClassification: Public|Internal|Confidential|Restricted
   - DataOwner: <team-name>
   - ComplianceScope: SOC2|HIPAA|PCI|None
   - Environment: Production|Development|Testing
   ```

3. **Amazon Macie Configuration**:
   ```
   - Enable Macie for S3 bucket scanning
   - Configure sensitive data discovery jobs
   - Identify PII, PHI, financial data
   - Review findings regularly
   - Classify data automatically
   ```

4. **Documentation**:
   ```
   - Data classification policy
   - Data handling procedures
   - Classification matrix
   - Data inventory
   ```

**Evidence Required**:
- Data classification policy document
- AWS resource tagging compliance reports
- Amazon Macie findings and configurations
- Data inventory with classifications
- Tag compliance dashboards
- Regular classification reviews

**Cost Estimation**:
- Amazon Macie: $1 per GB scanned (first scan), $0.10/GB (monthly monitoring)
- Small (1TB): $10-100/month
- Medium (10TB): $100-1,000/month
- Large (100TB): $1,000-10,000/month

---

### C1.2 - Disposal of Confidential Information

**Control Description**: The entity disposes of confidential information to meet the entity's objectives related to confidentiality.

**AWS Services**:
- **Amazon S3** (Object lifecycle policies)
- **AWS KMS** (Key deletion)
- **Amazon EBS** (Snapshot deletion)
- **AWS RDS** (Final snapshot before deletion)
- **AWS CloudTrail** (Deletion audit trail)

**Implementation**:

1. **Data Retention Policy**:
   ```
   - Define retention periods by classification
   - Confidential: 7 years
   - PII/PHI: Per regulation (GDPR, HIPAA)
   - Development/Test data: 90 days
   - Document legal hold procedures
   ```

2. **Automated Deletion**:
   ```
   S3 Lifecycle Policies:
   - Transition to Glacier: 90 days
   - Permanent deletion: 7 years
   - Delete incomplete multipart uploads: 7 days
   
   EBS Snapshots:
   - Automated deletion via Data Lifecycle Manager
   - Retain based on compliance requirements
   ```

3. **Secure Deletion Procedures**:
   ```
   - Delete encryption keys (KMS) after data deletion
   - S3 bucket emptying before deletion
   - RDS final snapshots (if needed)
   - EC2 volume deletion on termination
   - CloudTrail logging of all deletions
   ```

4. **Cryptographic Erasure**:
   ```
   - Delete KMS keys to render data unrecoverable
   - Wait mandatory 7-30 day key deletion window
   - Document key deletion procedures
   - Maintain deletion logs
   ```

**Evidence Required**:
- Data retention policy
- S3 lifecycle policy configurations
- KMS key deletion logs
- CloudTrail deletion events
- Data disposal certificates (for physical media)
- Annual retention review documentation

**Cost Estimation**:
- S3 lifecycle management: $0.01 per 1,000 requests
- Glacier storage: $0.004/GB/month
- KMS key storage: $1/key/month
- Generally minimal cost for deletion

---

### C1.3 - Confidential Information Access

**Control Description**: The entity restricts access to confidential information to authorized users.

**AWS Services**:
- **AWS IAM** (Access control)
- **AWS IAM Identity Center** (SSO)
- **Amazon S3 Access Points** (Simplified access control)
- **AWS KMS** (Encryption key access)
- **AWS CloudTrail** (Access logging)
- **AWS IAM Access Analyzer** (Permission analysis)

**Implementation**:

1. **Least Privilege Access**:
   ```
   IAM Policy Structure:
   - Deny by default
   - Grant minimum required permissions
   - Use IAM conditions for data classification
   - Require MFA for confidential data access
   ```

2. **Data Classification-Based Access**:
   ```
   Example IAM condition:
   {
     "Condition": {
       "StringEquals": {
         "aws:PrincipalTag/DataClearance": [
           "Confidential",
           "Restricted"
         ],
         "s3:ExistingObjectTag/DataClassification": "Confidential"
       }
     }
   }
   ```

3. **S3 Bucket Policies**:
   ```
   - Block public access (all buckets)
   - Require encryption in transit (HTTPS only)
   - Require encryption at rest
   - Use S3 Access Points for application access
   - Enable S3 Object Lock for immutability
   ```

4. **Monitoring and Alerts**:
   ```
   - CloudTrail logs for data access
   - S3 access logs
   - CloudWatch alarms for unauthorized attempts
   - GuardDuty for anomalous access
   - IAM Access Analyzer for policy validation
   ```

**Evidence Required**:
- IAM policies with data classification conditions
- S3 bucket policy configurations
- Access review logs (quarterly)
- CloudTrail access logs
- MFA enforcement evidence
- IAM Access Analyzer findings

**Cost Estimation**:
- IAM: No cost
- CloudTrail: $2 per 100,000 events
- S3 access logs: Storage costs only
- IAM Access Analyzer: $0.20 per analyzer/month
- Total: $50-200/month

---

## C2: Confidentiality - Encryption

### C2.1 - Encryption at Rest

**Control Description**: The entity protects confidential information at rest through encryption.

**AWS Services**:
- **AWS KMS** (Key management)
- **Amazon S3** (Bucket encryption)
- **Amazon EBS** (Volume encryption)
- **Amazon RDS** (Database encryption)
- **Amazon DynamoDB** (Table encryption)
- **Amazon EFS** (File system encryption)
- **AWS Secrets Manager** (Secret encryption)
- **AWS CloudTrail** (Encryption for logs)

**Implementation**:

1. **KMS Key Architecture**:
   ```
   Key Hierarchy:
   - Separate keys per environment (Prod/Dev/Test)
   - Separate keys per data classification
   - Separate keys per application
   - Enable automatic key rotation (annual)
   - Multi-region keys for DR scenarios
   ```

2. **Service-by-Service Encryption**:
   ```
   S3 Buckets:
   - Default encryption enabled (SSE-KMS)
   - Bucket keys for cost optimization
   - Deny unencrypted object uploads
   
   EBS Volumes:
   - Encryption by default enabled
   - Use customer-managed CMKs
   - Copy snapshots with encryption
   
   RDS/Aurora:
   - Enable encryption at creation
   - Use AWS KMS CMKs
   - Encrypted automated backups
   
   DynamoDB:
   - AWS owned, AWS managed, or Customer managed keys
   - Encryption at rest enabled by default
   
   EFS:
   - Enable encryption at creation
   - Use KMS CMKs
   ```

3. **Encryption Policy Enforcement**:
   ```
   AWS Config Rules:
   - encrypted-volumes (EBS)
   - s3-bucket-server-side-encryption-enabled
   - rds-storage-encrypted
   - dynamodb-table-encrypted-kms
   - efs-encrypted-check
   ```

4. **Key Management**:
   ```
   - Document key management procedures
   - Define key administrators vs. key users
   - Implement key rotation schedules
   - Monitor key usage via CloudTrail
   - Set up key deletion protection
   ```

**Evidence Required**:
- KMS key configurations and policies
- AWS Config compliance reports for encryption
- Service encryption settings (screenshots)
- Key rotation schedules and logs
- Encryption policy documentation
- CloudTrail logs of key usage

**Cost Estimation**:
- KMS: $1/key/month + $0.03 per 10,000 requests
- S3 encryption: No additional cost (with S3 Bucket Keys)
- EBS encryption: No additional cost
- RDS encryption: No additional cost
- Small: $20-50/month (10-20 keys)
- Medium: $50-200/month (50-100 keys)
- Large: $200-500/month (200-500 keys)

---

### C2.2 - Encryption in Transit

**Control Description**: The entity protects confidential information during transmission.

**AWS Services**:
- **AWS Certificate Manager** (TLS certificates)
- **Amazon CloudFront** (HTTPS CDN)
- **Application Load Balancer** (SSL/TLS termination)
- **Network Load Balancer** (TLS termination)
- **AWS VPN** (Encrypted tunnels)
- **AWS Direct Connect** (with MACsec)
- **AWS PrivateLink** (Private connections)

**Implementation**:

1. **TLS/SSL Configuration**:
   ```
   - Use ACM for certificate management
   - Enforce TLS 1.2 minimum (TLS 1.3 preferred)
   - Configure strong cipher suites
   - Enable HSTS (HTTP Strict Transport Security)
   - Redirect HTTP to HTTPS
   ```

2. **Service-by-Service Encryption**:
   ```
   CloudFront:
   - Require HTTPS for viewer connections
   - Use custom SSL certificates via ACM
   - Security policy: TLSv1.2_2021
   
   Application Load Balancer:
   - HTTPS listener with ACM certificate
   - Redirect HTTP to HTTPS
   - Security policy: ELBSecurityPolicy-TLS13-1-2-2021-06
   
   API Gateway:
   - Custom domain with ACM certificate
   - Enforce HTTPS only
   - Enable mutual TLS (mTLS) if needed
   
   RDS:
   - Require SSL connections
   - Download and use RDS CA certificates
   - Configure application to verify certificates
   ```

3. **VPC Encryption**:
   ```
   - Enable VPN connections for remote access
   - Use AWS PrivateLink for service access
   - Enable encryption for Direct Connect (MACsec)
   - VPC Peering (encrypted by default)
   ```

4. **Policy Enforcement**:
   ```
   S3 Bucket Policy (HTTPS only):
   {
     "Effect": "Deny",
     "Principal": "*",
     "Action": "s3:*",
     "Resource": "arn:aws:s3:::bucket/*",
     "Condition": {
       "Bool": {
         "aws:SecureTransport": "false"
       }
     }
   }
   ```

**Evidence Required**:
- ACM certificate list
- TLS policy configurations
- Load balancer listener configurations
- CloudFront distribution settings
- RDS SSL enforcement settings
- S3 bucket policies requiring HTTPS
- VPN/Direct Connect configurations

**Cost Estimation**:
- ACM: Free for AWS-integrated services
- Load Balancer SSL: No additional cost
- CloudFront HTTPS: No additional cost
- VPN: $0.05/connection hour + data transfer
- Direct Connect with MACsec: Standard DC rates
- Total: $0-500/month (mostly VPN/DC costs)

---

## C3: Confidentiality - Network Protection

### C3.1 - Network Segmentation

**Control Description**: The entity uses network segmentation to protect confidential information.

**AWS Services**:
- **Amazon VPC** (Network isolation)
- **Security Groups** (Instance-level firewalls)
- **Network ACLs** (Subnet-level firewalls)
- **AWS Network Firewall** (Advanced filtering)
- **AWS PrivateLink** (Private connectivity)
- **VPC Endpoints** (Private AWS service access)

**Implementation**:

1. **VPC Architecture**:
   ```
   Network Tiers:
   - Public Subnets: Load balancers, NAT gateways
   - Private Subnets: Application servers (no internet access)
   - Data Subnets: Databases, confidential data stores
   - Management Subnet: Jump hosts, admin access
   ```

2. **Security Group Design**:
   ```
   Principle: Deny all by default, allow specific
   
   Example: Database security group
   - Inbound: Port 5432 from app-sg only
   - Outbound: None (or specific as needed)
   
   Tag all security groups:
   - DataClassification
   - Purpose
   - Owner
   ```

3. **Network ACLs**:
   ```
   - Use as subnet-level defense
   - Deny known bad IPs/ports
   - Allow necessary traffic only
   - Document all rules
   ```

4. **AWS Network Firewall**:
   ```
   - Deploy for advanced inspection
   - Deep packet inspection
   - Block malicious traffic
   - Monitor encrypted traffic metadata
   ```

5. **Private Connectivity**:
   ```
   - VPC Endpoints for AWS services (S3, DynamoDB)
   - PrivateLink for third-party services
   - No data traverses public internet
   ```

**Evidence Required**:
- VPC architecture diagram
- Security group configurations
- Network ACL rules
- Network Firewall policies
- VPC Flow Logs
- Network segmentation documentation

**Cost Estimation**:
- VPC: Free
- VPC Endpoints: $0.01/GB + $0.01/hour
- Network Firewall: $0.395/hour + $0.065/GB processed
- Small: $50-200/month
- Medium: $200-1,000/month
- Large: $1,000-5,000/month

---

## Implementation Checklist

### Phase 1: Discovery & Classification (Week 1-2)
- [ ] Define data classification framework
- [ ] Enable Amazon Macie
- [ ] Run data discovery scans
- [ ] Implement tagging strategy
- [ ] Document data inventory

### Phase 2: Access Controls (Week 3-4)
- [ ] Review and update IAM policies
- [ ] Implement data classification conditions
- [ ] Enable MFA for confidential data access
- [ ] Configure S3 bucket policies
- [ ] Set up IAM Access Analyzer

### Phase 3: Encryption (Week 5-6)
- [ ] Create KMS key hierarchy
- [ ] Enable S3 default encryption
- [ ] Enable EBS encryption by default
- [ ] Encrypt existing RDS databases (requires recreation)
- [ ] Configure TLS/SSL for all services
- [ ] Enable AWS Config encryption rules

### Phase 4: Network Security (Week 7-8)
- [ ] Review VPC architecture
- [ ] Implement network segmentation
- [ ] Configure security groups (least privilege)
- [ ] Deploy VPC endpoints
- [ ] Enable VPC Flow Logs
- [ ] Consider AWS Network Firewall

### Phase 5: Monitoring & Compliance (Week 9-10)
- [ ] Configure CloudTrail for data access logging
- [ ] Set up CloudWatch alarms
- [ ] Enable GuardDuty
- [ ] Schedule quarterly access reviews
- [ ] Document all procedures

---

## Reference Architecture: Confidential Data Processing

```
                    ┌──────────────────────┐
                    │   AWS Organizations   │
                    │  Service Control      │
                    │  Policies (SCPs)      │
                    └──────────┬───────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
┌───────▼────────┐    ┌────────▼───────┐    ┌────────▼────────┐
│  Production    │    │  Development   │    │   Security      │
│   Account      │    │    Account     │    │   Account       │
└───────┬────────┘    └────────────────┘    └─────────────────┘
        │
        │  VPC (10.0.0.0/16)
        │
        ├─── Public Subnets (10.0.1.0/24, 10.0.2.0/24)
        │    └── ALB (HTTPS only, ACM certificate)
        │
        ├─── Private Subnets (10.0.10.0/24, 10.0.11.0/24)
        │    ├── EC2 (confidential data processing)
        │    ├── Security Group: App-SG
        │    └── VPC Endpoints (S3, DynamoDB, KMS)
        │
        └─── Data Subnets (10.0.20.0/24, 10.0.21.0/24)
             ├── RDS Multi-AZ (encrypted with KMS)
             ├── Security Group: DB-SG (inbound from App-SG only)
             └── No internet access
        
┌──────────────────────────────────────────────────────────────┐
│                    Encryption & Monitoring                    │
├──────────────────────────────────────────────────────────────┤
│  KMS (Customer Managed Keys)                                 │
│  • Key per environment and classification                    │
│  • Annual automatic rotation                                 │
│  • CloudTrail logging of all key usage                       │
├──────────────────────────────────────────────────────────────┤
│  Amazon Macie                                                │
│  • S3 bucket scanning for PII/confidential data             │
│  • Automated classification                                  │
│  • Alerts on policy violations                               │
├──────────────────────────────────────────────────────────────┤
│  CloudTrail + CloudWatch                                     │
│  • All API calls logged                                      │
│  • Alerts on confidential data access                        │
│  • GuardDuty for anomalous access                           │
└──────────────────────────────────────────────────────────────┘
```

---

## AWS Service Costs Summary

| Service | Purpose | Monthly Cost Range |
|---------|---------|-------------------|
| Amazon Macie | Data classification | $1/GB scanned, $0.10/GB monitoring |
| AWS KMS | Encryption keys | $1/key + $0.03/10K requests |
| AWS IAM | Access control | Free |
| CloudTrail | Audit logging | $2/100K events |
| AWS Config | Compliance monitoring | $0.003/config item |
| Network Firewall | Advanced protection | $0.395/hour + $0.065/GB |
| VPC Endpoints | Private connectivity | $0.01/hour + $0.01/GB |
| GuardDuty | Threat detection | $4.46/GB CloudTrail, $1.18/GB VPC Flow |

**Total Estimated Monthly Cost**: 
- Small organization: $200-500
- Medium organization: $500-2,000
- Large organization: $2,000-10,000+

---

## Common Audit Findings

### Finding 1: Unencrypted Data at Rest
**Issue**: S3 buckets or EBS volumes without encryption
**Remediation**: Enable default encryption, use AWS Config to enforce

### Finding 2: Overly Permissive Access
**Issue**: IAM policies grant broad access to confidential data
**Remediation**: Implement least privilege, use IAM Access Analyzer

### Finding 3: No Data Classification
**Issue**: Unable to identify what is confidential
**Remediation**: Implement tagging strategy, use Amazon Macie

### Finding 4: Weak TLS Configuration
**Issue**: Older TLS versions or weak ciphers enabled
**Remediation**: Update security policies to TLS 1.2+

### Finding 5: Public S3 Buckets
**Issue**: S3 buckets accessible publicly
**Remediation**: Enable S3 Block Public Access, review bucket policies

---

## Next Steps

1. Classify your data using Amazon Macie
2. Implement encryption at rest and in transit
3. Review and restrict access to confidential data
4. Enable comprehensive logging and monitoring
5. Begin evidence collection for audit

[← Back to Availability](availability.md) | [View Processing Integrity Controls →](processing-integrity.md) | [Back to Main Guide](../README.md)
