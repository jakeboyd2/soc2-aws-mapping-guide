# Processing Integrity Trust Service Criteria

The Processing Integrity criteria addresses whether system processing is complete, valid, accurate, timely, and authorized to meet the entity's objectives.

## Overview

Processing Integrity ensures that:
- System processing is complete, valid, accurate, timely, and authorized
- Data inputs are complete and accurate
- Processing is properly authorized
- Errors are identified and corrected
- Outputs are complete and accurate

---

## PI1: Processing Integrity

### PI1.1 - Processing Inputs

**Control Description**: The entity obtains or generates, uses, and communicates relevant, quality information regarding inputs to meet the entity's objectives.

**AWS Services**:
- **AWS Lambda** (Input validation functions)
- **Amazon API Gateway** (Request validation)
- **AWS WAF** (Input filtering)
- **Amazon EventBridge** (Event validation)
- **AWS Step Functions** (Workflow validation)
- **Amazon SQS** (Message queuing with validation)

**Implementation**:

1. **Input Validation**:
   ```
   API Gateway:
   - Request validation using JSON Schema
   - Rate limiting per client
   - API key authentication
   - Request/response transformation
   
   Example validation:
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "type": "object",
     "properties": {
       "email": {"type": "string", "format": "email"},
       "amount": {"type": "number", "minimum": 0}
     },
     "required": ["email", "amount"]
   }
   ```

2. **AWS WAF Rules**:
   ```
   - SQL injection protection
   - XSS (Cross-Site Scripting) protection
   - Size constraints
   - Geographic restrictions
   - Rate-based rules
   ```

3. **Data Quality Checks**:
   ```
   Lambda functions:
   - Type validation
   - Range checks
   - Format validation
   - Business rule validation
   - Completeness checks
   ```

4. **Error Handling**:
   ```
   - Dead letter queues (DLQ) for failed messages
   - Retry logic with exponential backoff
   - Error logging to CloudWatch
   - Alerting on validation failures
   ```

**Evidence Required**:
- Input validation configurations
- API Gateway request validators
- WAF rule definitions
- Lambda validation functions
- Error handling procedures
- Validation failure logs
- CloudWatch metrics for invalid inputs

**Cost Estimation**:
- API Gateway: $3.50 per million requests
- Lambda: $0.20 per million requests
- WAF: $5/month + $1 per rule + $0.60 per million requests
- SQS: $0.40 per million requests
- Small: $50-150/month
- Medium: $150-500/month
- Large: $500-2,000/month

---

### PI1.2 - Processing Completeness and Accuracy

**Control Description**: The entity implements policies and procedures to provide reasonable assurance that processing is complete and accurate.

**AWS Services**:
- **AWS Step Functions** (Workflow orchestration)
- **Amazon SQS** (Guaranteed message delivery)
- **AWS CloudWatch** (Processing monitoring)
- **AWS X-Ray** (Distributed tracing)
- **Amazon DynamoDB** (Atomic transactions)
- **Amazon RDS** (ACID transactions)
- **AWS Lambda** (Idempotent processing)

**Implementation**:

1. **Transaction Management**:
   ```
   DynamoDB Transactions:
   - Use TransactWriteItems for atomic operations
   - Implement optimistic locking
   - Idempotency tokens for API calls
   
   RDS/Aurora:
   - Database transactions (BEGIN, COMMIT, ROLLBACK)
   - Isolation levels for data consistency
   - Foreign key constraints
   ```

2. **Workflow Orchestration**:
   ```
   Step Functions:
   - Define processing workflows as state machines
   - Error handling and retry logic
   - Parallel processing with aggregation
   - Compensation logic for failures
   
   Example workflow:
   ValidateInput → ProcessPayment → UpdateInventory → SendNotification
   ```

3. **Message Processing**:
   ```
   SQS Configuration:
   - Standard queue: At-least-once delivery
   - FIFO queue: Exactly-once processing
   - Message deduplication
   - Visibility timeout for processing
   - Dead letter queues for failures
   ```

4. **Idempotency**:
   ```
   - Use idempotency tokens (UUIDs)
   - Store processed request IDs in DynamoDB
   - TTL for automatic cleanup
   - Prevent duplicate processing
   ```

5. **Monitoring**:
   ```
   CloudWatch Metrics:
   - Processing success/failure rates
   - Processing duration
   - Queue depths
   - Error rates
   
   X-Ray Tracing:
   - End-to-end request tracing
   - Identify bottlenecks
   - Error analysis
   ```

**Evidence Required**:
- Step Functions state machine definitions
- Transaction handling code
- Idempotency implementation
- CloudWatch dashboards
- X-Ray trace maps
- Processing reconciliation reports
- Error handling documentation

**Cost Estimation**:
- Step Functions: $25 per million state transitions
- SQS: $0.40 per million requests
- DynamoDB: $0.25/GB storage + read/write capacity
- X-Ray: $5 per million traces
- CloudWatch: $0.30 per custom metric
- Small: $100-300/month
- Medium: $300-1,000/month
- Large: $1,000-5,000/month

---

### PI1.3 - Processing Authorization

**Control Description**: The entity implements policies and procedures to provide reasonable assurance that processing is authorized.

**AWS Services**:
- **AWS IAM** (Service authorization)
- **Amazon Cognito** (User authorization)
- **AWS Lambda Authorizers** (API authorization)
- **AWS Step Functions** (Workflow authorization)
- **AWS CloudTrail** (Authorization audit)
- **Amazon EventBridge** (Event authorization)

**Implementation**:

1. **API Authorization**:
   ```
   API Gateway Authorizers:
   - Lambda authorizers (custom logic)
   - Cognito User Pool authorizers
   - IAM authorization
   - Resource policies
   
   Authorization flow:
   1. Client requests with token
   2. Authorizer validates token
   3. Returns IAM policy
   4. API Gateway enforces policy
   ```

2. **User Authorization**:
   ```
   Amazon Cognito:
   - User pools for authentication
   - Identity pools for AWS access
   - Groups and role-based access
   - Custom attributes for authorization
   - MFA for sensitive operations
   ```

3. **Service-to-Service Authorization**:
   ```
   IAM Roles:
   - Least privilege policies
   - Service roles for Lambda, ECS
   - Resource-based policies
   - Cross-account access with conditions
   ```

4. **Workflow Authorization**:
   ```
   Step Functions:
   - IAM roles per workflow
   - Task-level authorization
   - Conditional authorization checks
   - Approval steps for critical operations
   ```

5. **Audit Trail**:
   ```
   CloudTrail:
   - Log all authorization decisions
   - Record denied requests
   - Monitor privilege escalation
   - Alert on unauthorized attempts
   ```

**Evidence Required**:
- Authorization policies
- Lambda authorizer code
- Cognito user pool configurations
- IAM role definitions
- CloudTrail authorization logs
- Denied access reports
- Authorization test results

**Cost Estimation**:
- Cognito: $0.0055 per MAU (after free tier)
- Lambda authorizers: Included in API Gateway
- CloudTrail: $2 per 100,000 events
- Small: $20-100/month
- Medium: $100-500/month
- Large: $500-2,000/month

---

### PI1.4 - Processing Timeliness

**Control Description**: The entity processes information on a timely basis.

**AWS Services**:
- **Amazon SQS** (Async processing)
- **Amazon SNS** (Event notifications)
- **Amazon EventBridge** (Scheduled events)
- **AWS Lambda** (Real-time processing)
- **Amazon Kinesis** (Stream processing)
- **AWS Batch** (Batch processing)
- **Amazon CloudWatch** (SLA monitoring)

**Implementation**:

1. **Real-Time Processing**:
   ```
   Lambda + API Gateway:
   - Synchronous request/response
   - Sub-second latency
   - Auto-scaling
   - Timeout configuration (max 15 minutes)
   ```

2. **Near Real-Time Processing**:
   ```
   SQS + Lambda:
   - Asynchronous processing
   - Batch processing with Lambda
   - Configurable polling
   - DLQ for failed processing
   ```

3. **Stream Processing**:
   ```
   Kinesis Data Streams:
   - Real-time data ingestion
   - Parallel processing (shards)
   - Kinesis Data Analytics for SQL processing
   - Lambda for stream processing
   ```

4. **Batch Processing**:
   ```
   AWS Batch:
   - Scheduled batch jobs
   - Large-scale processing
   - Job dependencies
   - Automatic resource provisioning
   
   EventBridge:
   - Scheduled triggers (cron)
   - Time-based processing
   - Event-driven workflows
   ```

5. **SLA Monitoring**:
   ```
   CloudWatch:
   - Define SLAs (e.g., 99.9% < 100ms)
   - Monitor processing duration
   - Alert on SLA violations
   - Dashboard for stakeholders
   ```

**Evidence Required**:
- Processing time requirements documentation
- CloudWatch metrics for processing duration
- SLA definitions and measurements
- Queue depth monitoring
- Alerts for delayed processing
- Capacity planning documentation

**Cost Estimation**:
- Lambda: $0.20 per million requests
- SQS: $0.40 per million requests
- Kinesis: $0.015 per shard-hour + $0.014/GB
- EventBridge: $1 per million events
- AWS Batch: EC2/Fargate costs only
- Small: $50-200/month
- Medium: $200-1,000/month
- Large: $1,000-10,000/month

---

### PI1.5 - Error Identification and Correction

**Control Description**: The entity identifies and corrects processing errors on a timely basis.

**AWS Services**:
- **AWS CloudWatch** (Error monitoring)
- **AWS X-Ray** (Error tracing)
- **Amazon SQS DLQ** (Failed message handling)
- **AWS Lambda** (Error handling)
- **Amazon SNS** (Error notifications)
- **AWS Systems Manager OpsCenter** (Incident management)

**Implementation**:

1. **Error Detection**:
   ```
   CloudWatch Alarms:
   - Lambda function errors
   - API Gateway 4xx/5xx errors
   - SQS DLQ message count
   - Step Functions failed executions
   
   X-Ray:
   - Trace errors through system
   - Identify error sources
   - Analyze error patterns
   ```

2. **Error Handling**:
   ```
   Lambda Error Handling:
   - Try-catch blocks
   - Retry logic
   - Dead letter queues
   - Error logging
   
   Step Functions:
   - Catch states for errors
   - Retry configurations
   - Fallback procedures
   - Compensation logic
   ```

3. **Dead Letter Queues**:
   ```
   SQS DLQ Configuration:
   - Max receive count before DLQ
   - Separate DLQ per queue
   - Monitor DLQ depth
   - Manual reprocessing procedures
   ```

4. **Error Notification**:
   ```
   SNS Topics:
   - Critical errors → PagerDuty/On-call
   - Warning errors → Email/Slack
   - Escalation procedures
   - 24/7 monitoring for critical systems
   ```

5. **Error Resolution**:
   ```
   OpsCenter:
   - Centralized incident tracking
   - Automated runbooks
   - Status updates
   - Resolution documentation
   ```

6. **Error Analysis**:
   ```
   CloudWatch Logs Insights:
   - Query error patterns
   - Root cause analysis
   - Trending analysis
   - Preventive measures
   ```

**Evidence Required**:
- Error handling procedures
- CloudWatch alarm configurations
- DLQ configurations and depths
- Error resolution logs
- X-Ray traces showing error handling
- Incident response documentation
- Error trend analysis reports

**Cost Estimation**:
- CloudWatch Logs: $0.50/GB ingested
- CloudWatch Logs Insights: $0.005 per GB scanned
- X-Ray: $5 per million traces
- SNS: $0.50 per million notifications
- OpsCenter: $0.10 per OpsItem
- Small: $50-150/month
- Medium: $150-500/month
- Large: $500-2,000/month

---

## PI2: Quality Information Communication

### PI2.1 - Output Completeness and Accuracy

**Control Description**: The entity produces output that is complete and accurate.

**AWS Services**:
- **Amazon CloudWatch** (Output monitoring)
- **AWS Lambda** (Output validation)
- **Amazon S3** (Output storage and versioning)
- **AWS Glue** (Data quality checks)
- **Amazon Athena** (Output validation queries)

**Implementation**:

1. **Output Validation**:
   ```
   Lambda Functions:
   - Validate output format
   - Check data completeness
   - Verify calculations
   - Business rule validation
   ```

2. **Data Quality Rules**:
   ```
   AWS Glue Data Quality:
   - Completeness checks
   - Uniqueness validation
   - Format validation
   - Statistical analysis
   ```

3. **Output Versioning**:
   ```
   S3 Versioning:
   - Enable versioning for output buckets
   - Object tagging with metadata
   - Lifecycle policies
   - Prevent accidental deletion
   ```

4. **Reconciliation**:
   ```
   - Compare input counts to output counts
   - Validate totals and aggregations
   - Cross-reference with source systems
   - Automated reconciliation reports
   ```

**Evidence Required**:
- Output validation code
- Data quality rules
- Reconciliation reports
- S3 versioning configurations
- Output sample validation
- Quality metrics

**Cost Estimation**:
- S3: $0.023/GB storage
- Glue: $0.44 per DPU-hour
- Athena: $5 per TB scanned
- Lambda: $0.20 per million requests
- Small: $20-100/month
- Medium: $100-500/month
- Large: $500-3,000/month

---

## Implementation Checklist

### Phase 1: Input Validation (Week 1-2)
- [ ] Implement API Gateway request validation
- [ ] Configure AWS WAF rules
- [ ] Create Lambda input validation functions
- [ ] Set up error handling and DLQs
- [ ] Document validation rules

### Phase 2: Processing Controls (Week 3-4)
- [ ] Implement transaction management
- [ ] Create Step Functions workflows
- [ ] Configure SQS for reliable messaging
- [ ] Implement idempotency
- [ ] Set up X-Ray tracing

### Phase 3: Authorization (Week 5-6)
- [ ] Configure API Gateway authorizers
- [ ] Set up Cognito user pools
- [ ] Implement IAM roles and policies
- [ ] Enable CloudTrail logging
- [ ] Test authorization scenarios

### Phase 4: Monitoring & Error Handling (Week 7-8)
- [ ] Configure CloudWatch alarms
- [ ] Set up error notification (SNS)
- [ ] Create error handling runbooks
- [ ] Implement DLQ monitoring
- [ ] Set up OpsCenter

### Phase 5: Validation & Testing (Week 9-10)
- [ ] Implement output validation
- [ ] Create reconciliation procedures
- [ ] Test error scenarios
- [ ] Load testing
- [ ] Document all procedures

---

## Reference Architecture: Data Processing Pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                    Input Layer                                   │
├─────────────────────────────────────────────────────────────────┤
│  API Gateway                      Amazon SQS                    │
│  • Request validation             • Standard/FIFO queues        │
│  • Lambda authorizer              • Dead letter queues          │
│  • Rate limiting                  • Message deduplication       │
│  • AWS WAF protection             • Visibility timeout          │
└────────────────┬─────────────────────────┬──────────────────────┘
                 │                         │
┌────────────────▼─────────────────────────▼──────────────────────┐
│                   Processing Layer                               │
├─────────────────────────────────────────────────────────────────┤
│  AWS Step Functions                                             │
│  ┌────────────┐   ┌────────────┐   ┌────────────┐             │
│  │  Validate  │ → │  Process   │ → │  Notify    │             │
│  │   Input    │   │   Data     │   │  Output    │             │
│  └────────────┘   └────────────┘   └────────────┘             │
│         ↓                ↓                ↓                     │
│  Lambda Functions (with error handling)                         │
│  • Idempotent processing                                        │
│  • DynamoDB transactions                                        │
│  • X-Ray tracing                                                │
└────────────────┬────────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────────┐
│                    Output Layer                                  │
├─────────────────────────────────────────────────────────────────┤
│  Amazon S3                        Amazon SNS                    │
│  • Output storage                 • Success notifications       │
│  • Versioning enabled             • Error alerts                │
│  • Validation checks              • Delivery status logging     │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                   Monitoring & Observability                     │
├─────────────────────────────────────────────────────────────────┤
│  CloudWatch: Metrics, Logs, Alarms, Dashboards                 │
│  X-Ray: Distributed tracing and error analysis                  │
│  CloudTrail: Authorization and API audit logs                   │
│  OpsCenter: Incident management and resolution                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## AWS Service Costs Summary

| Service | Purpose | Monthly Cost Range |
|---------|---------|-------------------|
| API Gateway | Input validation | $3.50/million requests |
| AWS WAF | Input filtering | $5 + rules + requests |
| Lambda | Processing | $0.20/million requests |
| Step Functions | Orchestration | $25/million transitions |
| SQS | Message queuing | $0.40/million requests |
| DynamoDB | Transaction storage | $0.25/GB + capacity |
| CloudWatch | Monitoring | $0.30/metric + logs |
| X-Ray | Tracing | $5/million traces |
| Cognito | Authorization | $0.0055/MAU |

**Total Estimated Monthly Cost**:
- Small: $200-500
- Medium: $500-2,000
- Large: $2,000-10,000+

---

## Common Audit Findings

### Finding 1: No Input Validation
**Issue**: Processing accepts invalid or malicious inputs
**Remediation**: Implement API Gateway validation and AWS WAF

### Finding 2: No Transaction Management
**Issue**: Data inconsistencies due to partial updates
**Remediation**: Use DynamoDB transactions or RDS ACID transactions

### Finding 3: No Authorization Checks
**Issue**: Unauthorized processing requests
**Remediation**: Implement Lambda authorizers and IAM policies

### Finding 4: Poor Error Handling
**Issue**: Errors not logged, monitored, or alerted
**Remediation**: Configure DLQs, CloudWatch alarms, and SNS notifications

### Finding 5: No Processing Validation
**Issue**: Output accuracy not verified
**Remediation**: Implement reconciliation and output validation

---

## Next Steps

1. Implement input validation at all entry points
2. Set up transaction management for critical operations
3. Configure authorization for all processing
4. Implement comprehensive error handling
5. Begin evidence collection

[← Back to Confidentiality](confidentiality.md) | [View Privacy Controls →](privacy.md) | [Back to Main Guide](../README.md)
