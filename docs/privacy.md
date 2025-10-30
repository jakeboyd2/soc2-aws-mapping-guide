# Privacy Trust Service Criteria

The Privacy criteria addresses the collection, use, retention, disclosure, and disposal of personal information in conformity with the entity's privacy notice and with criteria set forth in Generally Accepted Privacy Principles (GAPP).

## Overview

Privacy ensures that:
- Personal information is collected, used, retained, disclosed, and disposed of in conformity with commitments
- Privacy notices are provided
- Choices and consent are obtained
- Collection is limited to stated purposes
- Data subjects can access and update their information
- Disclosure to third parties follows privacy commitments
- Security for privacy is maintained
- Data quality is ensured
- Monitoring and enforcement are in place

⚠️ **Important**: Privacy requirements vary by jurisdiction (GDPR, CCPA, etc.). This guide provides AWS implementation guidance; consult legal counsel for compliance requirements.

---

## P1: Notice and Communication

### P1.1 - Privacy Notice

**Control Description**: The entity provides notice to data subjects about its privacy practices.

**AWS Services**:
- **Amazon S3** (Host privacy policy)
- **Amazon CloudFront** (Deliver privacy policy)
- **AWS Certificate Manager** (HTTPS for privacy page)
- **Amazon Cognito** (Privacy notice during signup)
- **Amazon SES** (Email privacy notifications)

**Implementation**:

1. **Privacy Policy Creation**:
   ```
   Required Elements:
   - What data is collected
   - How data is used
   - Who data is shared with
   - How data is protected
   - User rights (access, deletion, portability)
   - Cookie and tracking policies
   - Data retention periods
   - Contact information
   - Effective date and version
   ```

2. **Privacy Policy Delivery**:
   ```
   - Host on publicly accessible URL
   - HTTPS only (via CloudFront + ACM)
   - Version control with effective dates
   - Archive old versions for records
   - Multi-language support if needed
   ```

3. **Consent Collection**:
   ```
   Amazon Cognito:
   - Add privacy policy link to signup
   - Require acceptance checkbox
   - Store consent timestamp
   - Version consent with policy version
   ```

4. **Change Notifications**:
   ```
   Amazon SES:
   - Email users on privacy policy changes
   - Require re-consent if material changes
   - Log notification delivery
   - Track acknowledgments
   ```

**Evidence Required**:
- Current privacy policy document
- Historical privacy policy versions
- Privacy policy URL and access logs
- Consent records (timestamp, version, user)
- Change notification logs
- Multi-language versions (if applicable)

**Cost Estimation**:
- S3 hosting: $0.023/GB
- CloudFront: $0.085/GB + requests
- SES: $0.10 per 1,000 emails
- Total: $10-50/month (minimal traffic)

---

### P1.2 - Privacy Choices

**Control Description**: The entity provides notice and choice regarding the collection, use, retention, disclosure, and disposal of personal information.

**AWS Services**:
- **Amazon Cognito** (User preferences)
- **Amazon DynamoDB** (Preference storage)
- **AWS Lambda** (Preference processing)
- **Amazon SES** (Communication preferences)
- **Amazon SNS** (Notification preferences)

**Implementation**:

1. **Preference Management System**:
   ```
   DynamoDB Table: user_preferences
   - user_id (partition key)
   - email_marketing (boolean)
   - data_sharing (boolean)
   - analytics_tracking (boolean)
   - third_party_sharing (boolean)
   - updated_at (timestamp)
   ```

2. **Consent Granularity**:
   ```
   Allow users to opt in/out of:
   - Marketing communications
   - Product updates
   - Analytics and tracking
   - Third-party data sharing
   - Location tracking
   - Cookie preferences
   ```

3. **Preference Management UI**:
   ```
   - Self-service preference portal
   - API for programmatic updates
   - Email with preference links
   - Granular controls per data type
   - "Do Not Sell" option (CCPA)
   ```

4. **Honoring Preferences**:
   ```
   Lambda Functions:
   - Check preferences before processing
   - Respect opt-outs immediately
   - Log preference changes
   - Audit preference enforcement
   ```

**Evidence Required**:
- Preference management system documentation
- User preference data model
- Preference UI screenshots
- Logs of preference changes
- Evidence of preference enforcement
- Opt-out processing procedures

**Cost Estimation**:
- DynamoDB: $0.25/GB + read/write capacity
- Lambda: $0.20 per million requests
- API Gateway: $3.50 per million requests
- Total: $20-100/month

---

## P2: Choice and Consent

### P2.1 - Informed Consent

**Control Description**: The entity obtains implicit or explicit consent at or before the time personal information is collected.

**AWS Services**:
- **Amazon Cognito** (User authentication)
- **AWS Lambda** (Consent logic)
- **Amazon DynamoDB** (Consent records)
- **AWS CloudTrail** (Audit trail)

**Implementation**:

1. **Consent Collection**:
   ```
   Cognito Pre-Signup Lambda:
   - Present privacy policy
   - Require acceptance checkbox
   - Capture consent timestamp
   - Record policy version
   - Store IP address and user agent
   ```

2. **Consent Storage**:
   ```
   DynamoDB Table: consent_records
   - user_id (partition key)
   - consent_type (sort key: "privacy_policy", "marketing", etc.)
   - consent_given (boolean)
   - policy_version (string)
   - timestamp (ISO 8601)
   - ip_address (string)
   - user_agent (string)
   ```

3. **Consent Types**:
   ```
   - Privacy Policy Acceptance (required)
   - Terms of Service (required)
   - Marketing Communications (optional)
   - Data Sharing (optional)
   - Analytics Tracking (optional)
   - Cookie Usage (optional in some jurisdictions)
   ```

4. **Consent Withdrawal**:
   ```
   - Easy withdrawal mechanism
   - Process within 30 days (GDPR) or 45 days (CCPA)
   - Confirmation of withdrawal
   - Log withdrawal actions
   ```

**Evidence Required**:
- Consent collection process documentation
- Consent records database schema
- Sample consent records
- Withdrawal process documentation
- Audit logs of consent changes
- Consent UI/UX screenshots

**Cost Estimation**:
- DynamoDB: $0.25/GB storage
- Lambda: $0.20 per million requests
- CloudTrail: $2 per 100,000 events
- Total: $10-50/month

---

## P3: Collection

### P3.1 - Collection Limited to Purpose

**Control Description**: The entity collects personal information only for the purposes identified in the notice.

**AWS Services**:
- **AWS Lambda** (Collection validation)
- **Amazon API Gateway** (Request validation)
- **AWS WAF** (Input filtering)
- **Amazon Macie** (Data discovery)
- **AWS Config** (Compliance monitoring)

**Implementation**:

1. **Data Minimization**:
   ```
   API Gateway Request Validation:
   {
     "type": "object",
     "properties": {
       "email": {"type": "string"},
       "name": {"type": "string"}
       // Only collect what's needed
     },
     "required": ["email"],
     "additionalProperties": false  // Reject extra data
   }
   ```

2. **Purpose Documentation**:
   ```
   Data Collection Matrix:
   | Data Element | Purpose | Legal Basis | Retention |
   |--------------|---------|-------------|-----------|
   | Email | Account creation | Contract | Account lifetime |
   | Name | Personalization | Consent | Account lifetime |
   | IP Address | Security | Legitimate interest | 90 days |
   ```

3. **Collection Audit**:
   ```
   Amazon Macie:
   - Scan S3 buckets for PII
   - Identify unexpected data collection
   - Alert on policy violations
   - Regular data discovery scans
   ```

4. **Validation Logic**:
   ```
   Lambda Functions:
   - Validate data against documented purposes
   - Reject unnecessary data fields
   - Log collection attempts
   - Alert on policy violations
   ```

**Evidence Required**:
- Data collection documentation
- Purpose mapping matrix
- API validation configurations
- Macie scan results
- Collection audit logs
- Data minimization evidence

**Cost Estimation**:
- Macie: $1/GB first scan, $0.10/GB monthly
- API Gateway: Included
- Lambda: $0.20 per million requests
- Total: $50-500/month (depending on data volume)

---

## P4: Use, Retention, and Disposal

### P4.1 - Personal Information Use

**Control Description**: The entity limits the use of personal information to the purposes identified in the notice.

**AWS Services**:
- **AWS IAM** (Access control)
- **Amazon DynamoDB** (Purpose tagging)
- **AWS CloudTrail** (Access audit)
- **Amazon Macie** (Usage monitoring)
- **AWS Lambda** (Purpose validation)

**Implementation**:

1. **Purpose-Based Access Control**:
   ```
   IAM Policy with Conditions:
   {
     "Effect": "Allow",
     "Action": ["dynamodb:GetItem"],
     "Resource": "arn:aws:dynamodb:*:*:table/Users",
     "Condition": {
       "StringEquals": {
         "aws:PrincipalTag/DataPurpose": "marketing",
         "dynamodb:LeadingKeys": ["${aws:userid}"]
       }
     }
   }
   ```

2. **Data Tagging**:
   ```
   DynamoDB Items with Purpose Tags:
   {
     "user_id": "123",
     "email": "user@example.com",
     "data_purposes": ["account", "marketing"],
     "consent": {
       "marketing": true,
       "analytics": false
     }
   }
   ```

3. **Usage Monitoring**:
   ```
   CloudTrail Analysis:
   - Monitor who accesses personal data
   - Verify access aligns with purpose
   - Alert on unauthorized access patterns
   - Regular access reviews
   ```

**Evidence Required**:
- Purpose limitation policy
- IAM policies with purpose conditions
- Data tagging implementation
- CloudTrail access logs
- Usage audit reports
- Purpose validation code

**Cost Estimation**:
- IAM: Free
- CloudTrail: $2 per 100,000 events
- DynamoDB: Standard storage costs
- Total: $20-100/month

---

### P4.2 - Data Retention and Disposal

**Control Description**: The entity retains personal information for the period necessary to fulfill the stated purpose or as required by law, then securely disposes of it.

**AWS Services**:
- **Amazon S3** (Lifecycle policies)
- **Amazon DynamoDB** (TTL)
- **AWS RDS** (Backup retention)
- **AWS KMS** (Key deletion for crypto-erasure)
- **AWS Lambda** (Automated deletion)
- **Amazon EventBridge** (Scheduled deletion)

**Implementation**:

1. **Retention Policy**:
   ```
   Data Retention Matrix:
   | Data Type | Retention Period | Legal Basis | Disposal Method |
   |-----------|------------------|-------------|-----------------|
   | Account data | Account lifetime + 30 days | Contract | Secure deletion |
   | Marketing data | Until opt-out | Consent | Immediate deletion |
   | Logs | 90 days | Legitimate interest | Automated deletion |
   | Backups | 7 years | Legal requirement | Crypto-erasure |
   ```

2. **DynamoDB TTL**:
   ```
   DynamoDB Item with TTL:
   {
     "user_id": "123",
     "session_data": "...",
     "ttl": 1735689600  // Unix timestamp for expiration
   }
   
   - Enable TTL on table
   - Automatic deletion within 48 hours
   - No cost for deletions
   ```

3. **S3 Lifecycle Policies**:
   ```
   S3 Lifecycle Rule:
   - Transition to Glacier: 90 days
   - Permanent deletion: 7 years
   - Delete markers for versioned objects
   - Multipart upload cleanup
   ```

4. **Cryptographic Erasure**:
   ```
   For encrypted backups:
   1. Delete KMS key
   2. Wait 7-30 day deletion window
   3. Data becomes unrecoverable
   4. Document key deletion
   ```

5. **User-Initiated Deletion**:
   ```
   Lambda Function: delete_user_data()
   - Delete from all databases (DynamoDB, RDS)
   - Delete from S3 buckets
   - Delete from backups (or crypto-erase)
   - Delete from logs (where feasible)
   - Send deletion confirmation
   - Log deletion action
   ```

6. **Scheduled Cleanup**:
   ```
   EventBridge Rule:
   - Run daily cleanup Lambda
   - Check for expired data
   - Execute deletions
   - Log cleanup actions
   - Alert on failures
   ```

**Evidence Required**:
- Data retention policy document
- S3 lifecycle configurations
- DynamoDB TTL settings
- User deletion procedures
- Deletion logs and confirmations
- KMS key deletion records
- Automated cleanup job logs

**Cost Estimation**:
- S3 lifecycle: $0.01 per 1,000 requests
- DynamoDB TTL: Free
- Lambda: $0.20 per million requests
- EventBridge: $1 per million events
- Total: $10-50/month

---

## P5: Access

### P5.1 - Data Subject Access

**Control Description**: The entity provides data subjects with access to their personal information upon request.

**AWS Services**:
- **Amazon API Gateway** (Access request API)
- **AWS Lambda** (Data retrieval)
- **Amazon DynamoDB** (Data storage)
- **Amazon S3** (Data export)
- **Amazon Cognito** (User authentication)

**Implementation**:

1. **Access Request Process**:
   ```
   User Flow:
   1. User logs in (Cognito)
   2. Requests data via API or UI
   3. Lambda retrieves data from all sources
   4. Generate export file (JSON/CSV)
   5. Upload to S3 with pre-signed URL
   6. Email user with download link
   7. Auto-delete export after 7 days
   ```

2. **Data Export Lambda**:
   ```python
   def export_user_data(user_id):
       data = {
           'profile': get_dynamodb_data(user_id),
           'activity': get_activity_logs(user_id),
           's3_objects': list_user_s3_objects(user_id),
           'preferences': get_user_preferences(user_id)
       }
       
       # Generate export file
       export_file = json.dumps(data, indent=2)
       
       # Upload to S3 with expiration
       s3.put_object(
           Bucket='exports',
           Key=f'export-{user_id}-{timestamp}.json',
           Body=export_file,
           Tags=[{'Key': 'expiration', 'Value': '7days'}]
       )
       
       # Generate pre-signed URL
       return generate_presigned_url(...)
   ```

3. **Data Sources to Include**:
   ```
   - DynamoDB tables (all user data)
   - S3 buckets (user files)
   - RDS databases (transactions)
   - CloudWatch logs (activity logs)
   - Cognito user attributes
   - Third-party integrations
   ```

4. **Response Time SLA**:
   ```
   - Acknowledge request: Within 24 hours
   - Fulfill request: Within 30 days (GDPR) or 45 days (CCPA)
   - Automated if possible, manual if complex
   - Log all requests and fulfillments
   ```

**Evidence Required**:
- Access request procedures
- Data export implementation
- Request and fulfillment logs
- Response time metrics
- Sample export files
- User authentication logs

**Cost Estimation**:
- Lambda: $0.20 per million requests
- S3: $0.023/GB storage
- API Gateway: $3.50 per million requests
- Total: $20-100/month (low request volume)

---

### P5.2 - Data Correction and Deletion

**Control Description**: The entity provides data subjects with the ability to update or delete their personal information.

**AWS Services**:
- **Amazon API Gateway** (Update/delete API)
- **AWS Lambda** (CRUD operations)
- **Amazon DynamoDB** (Data storage)
- **Amazon S3** (File storage)
- **AWS Step Functions** (Deletion workflow)

**Implementation**:

1. **Data Update API**:
   ```
   PUT /api/user/profile
   {
     "name": "New Name",
     "email": "newemail@example.com"
   }
   
   - Authenticate user
   - Validate input
   - Update all data stores
   - Log update action
   - Return confirmation
   ```

2. **Right to Deletion ("Right to be Forgotten")**:
   ```
   Step Functions Workflow:
   1. Verify user identity
   2. Delete from DynamoDB tables
   3. Delete from S3 buckets
   4. Delete from RDS (if applicable)
   5. Purge from backups (or crypto-erase)
   6. Delete from CloudWatch logs (where feasible)
   7. Notify third parties of deletion
   8. Send confirmation email
   9. Log deletion completion
   ```

3. **Deletion Exceptions**:
   ```
   Data that may be retained:
   - Legal obligations (tax records)
   - Fraud prevention (anonymous)
   - Security logs (anonymous)
   - Document exceptions in privacy policy
   ```

4. **Anonymization vs. Deletion**:
   ```
   For some data:
   - Anonymize instead of delete
   - Remove PII but keep aggregates
   - Use hashing for pseudonymization
   - Document anonymization procedures
   ```

**Evidence Required**:
- Update/deletion procedures
- Step Functions workflow definitions
- Deletion logs with timestamps
- Confirmation emails
- Exception handling documentation
- Anonymization procedures

**Cost Estimation**:
- Lambda: $0.20 per million requests
- Step Functions: $25 per million state transitions
- DynamoDB: Write capacity costs
- Total: $20-100/month

---

## P6: Disclosure to Third Parties

### P6.1 - Third-Party Disclosure

**Control Description**: The entity discloses personal information to third parties only for identified purposes and with implicit or explicit consent.

**AWS Services**:
- **AWS CloudTrail** (Disclosure audit)
- **Amazon S3** (Data transfer logs)
- **AWS Lambda** (Disclosure validation)
- **Amazon DynamoDB** (Consent records)

**Implementation**:

1. **Third-Party Inventory**:
   ```
   Maintain list of third parties:
   - Name and contact
   - Purpose of disclosure
   - Data elements shared
   - Legal basis (consent, contract, etc.)
   - Data processing agreement (DPA)
   - Transfer mechanism (if international)
   ```

2. **Consent Verification**:
   ```
   Lambda: verify_disclosure_consent()
   - Check user consent record
   - Verify consent for specific third party
   - Check consent is current
   - Log disclosure attempt
   - Deny if no consent
   ```

3. **Disclosure Logging**:
   ```
   CloudTrail + Custom Logs:
   - Log all data transfers
   - Recipient identity
   - Data elements shared
   - Timestamp
   - Legal basis
   - User consent reference
   ```

4. **Data Processing Agreements**:
   ```
   - DPA with each third party
   - Standard contractual clauses (GDPR)
   - CPRA service provider agreement (CCPA)
   - Store signed agreements
   - Annual DPA reviews
   ```

**Evidence Required**:
- Third-party inventory
- Data processing agreements
- Disclosure logs
- Consent verification code
- Transfer impact assessments (for international)
- Privacy policy disclosures

**Cost Estimation**:
- CloudTrail: $2 per 100,000 events
- Lambda: $0.20 per million requests
- S3 storage: $0.023/GB
- Total: $20-100/month

---

## P7: Security for Privacy

### P7.1 - Security Controls

**Control Description**: The entity implements security controls to protect personal information.

**AWS Services**: See [Confidentiality Criteria](confidentiality.md) for detailed security controls.

**Key Controls**:
- Encryption at rest (KMS)
- Encryption in transit (TLS)
- Access controls (IAM)
- Network security (VPC, security groups)
- Monitoring (CloudTrail, GuardDuty)
- Incident response

**Cross-Reference**: All security controls from Common Criteria (CC6) and Confidentiality (C) apply to privacy.

---

## P8: Data Quality

### P8.1 - Data Accuracy

**Control Description**: The entity maintains accurate, complete, and relevant personal information.

**AWS Services**:
- **AWS Lambda** (Validation functions)
- **Amazon API Gateway** (Input validation)
- **Amazon DynamoDB** (Data storage)
- **AWS Glue** (Data quality rules)

**Implementation**:

1. **Input Validation**:
   ```
   - Email format validation
   - Phone number format validation
   - Address validation (via third-party API)
   - Required field enforcement
   - Data type checking
   ```

2. **Data Quality Checks**:
   ```
   AWS Glue Data Quality:
   - Completeness checks
   - Format validation
   - Duplicate detection
   - Consistency checks
   - Automated correction where possible
   ```

3. **User-Driven Accuracy**:
   ```
   - Allow users to update their data
   - Periodic re-verification (email, phone)
   - Prompt for updates on login
   - Annual accuracy confirmation
   ```

**Evidence Required**:
- Validation rules
- Data quality metrics
- User update procedures
- Verification logs

**Cost Estimation**: $20-100/month

---

## P9: Monitoring and Enforcement

### P9.1 - Ongoing Monitoring

**Control Description**: The entity monitors compliance with privacy policies and procedures.

**AWS Services**:
- **AWS Config** (Compliance monitoring)
- **Amazon CloudWatch** (Operational monitoring)
- **AWS Security Hub** (Security findings)
- **Amazon Macie** (Data protection monitoring)

**Implementation**:

1. **Compliance Monitoring**:
   ```
   AWS Config Rules:
   - s3-bucket-public-read-prohibited
   - s3-bucket-public-write-prohibited
   - encrypted-volumes
   - rds-storage-encrypted
   - cloudtrail-enabled
   ```

2. **Privacy Metrics**:
   ```
   CloudWatch Dashboards:
   - Access requests (count, avg response time)
   - Deletion requests (count, completion rate)
   - Consent rates
   - Opt-out rates
   - Policy violations
   ```

3. **Regular Audits**:
   ```
   - Quarterly privacy reviews
   - Annual third-party audits
   - Continuous monitoring via Config
   - Incident reporting
   ```

**Evidence Required**:
- Config compliance reports
- CloudWatch dashboards
- Audit reports
- Violation logs and remediation

**Cost Estimation**:
- Config: $0.003 per config item
- CloudWatch: $0.30 per metric
- Security Hub: $0.0010 per finding
- Total: $100-500/month

---

## Implementation Checklist

### Phase 1: Foundation (Week 1-3)
- [ ] Create comprehensive privacy policy
- [ ] Implement consent collection
- [ ] Set up preference management
- [ ] Document data inventory
- [ ] Define retention policies

### Phase 2: Data Subject Rights (Week 4-6)
- [ ] Implement data access API
- [ ] Create data export functionality
- [ ] Implement data correction
- [ ] Implement deletion workflow
- [ ] Test all DSAR processes

### Phase 3: Controls (Week 7-9)
- [ ] Implement purpose-based access control
- [ ] Configure data retention automation
- [ ] Set up third-party disclosure logging
- [ ] Implement data quality checks
- [ ] Configure monitoring and alerts

### Phase 4: Documentation & Testing (Week 10-12)
- [ ] Complete all documentation
- [ ] Perform privacy impact assessment
- [ ] Test all user-facing features
- [ ] Train staff on procedures
- [ ] Prepare for audit

---

## Common Audit Findings

### Finding 1: No Privacy Policy
**Issue**: No privacy policy or outdated policy
**Remediation**: Create comprehensive, current privacy policy

### Finding 2: No Consent Records
**Issue**: Unable to demonstrate consent
**Remediation**: Implement consent storage and audit trail

### Finding 3: Cannot Fulfill DSAR
**Issue**: No process for data subject access requests
**Remediation**: Implement data export and deletion workflows

### Finding 4: Excessive Data Retention
**Issue**: Retaining data longer than necessary
**Remediation**: Implement automated deletion with TTL

### Finding 5: No Third-Party Tracking
**Issue**: Unable to list third-party data recipients
**Remediation**: Maintain third-party inventory and DPAs

---

## Next Steps

1. Review applicable privacy laws (GDPR, CCPA, etc.)
2. Create or update privacy policy
3. Implement consent and preference management
4. Build data subject rights APIs
5. Set up monitoring and enforcement
6. Conduct privacy impact assessment
7. Begin evidence collection

[← Back to Processing Integrity](processing-integrity.md) | [Back to Main Guide](../README.md)
