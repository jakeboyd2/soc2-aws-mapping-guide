# CC7: System Operations

## Control Family Overview

CC7 addresses the entity's ability to detect, respond to, mitigate, and recover from security events, system failures, and processing deviations. This includes monitoring, logging, incident response, and capacity management.

## Control Description

The entity monitors, evaluates, and responds to system operations, security events, and other incidents. Key requirements include:

- **Monitoring**: Continuous monitoring of system operations and security events
- **Logging**: Comprehensive logging of system activities and security-relevant events
- **Incident Detection**: Automated detection of security incidents and anomalies
- **Incident Response**: Documented procedures for responding to and recovering from incidents
- **Capacity Management**: Monitoring and management of system capacity and performance

## AWS Services That Address CC7

### Primary Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **Amazon CloudWatch** | Monitoring & observability | Metrics, logs, alarms, dashboards, insights |
| **AWS CloudTrail** | Audit logging | API activity tracking, event history, compliance support |
| **AWS Security Hub** | Security findings aggregation | Centralized security view, compliance checks, automated response |
| **Amazon GuardDuty** | Threat detection | ML-based anomaly detection, threat intelligence |
| **AWS Config** | Configuration monitoring | Resource inventory, compliance tracking, change detection |

### Supporting Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **Amazon EventBridge** | Event-driven automation | Event routing, automated response, workflow integration |
| **AWS Systems Manager** | Operations management | Automation, patch management, run command |
| **Amazon SNS** | Alerting | Multi-channel notifications, topic-based pub/sub |
| **AWS Lambda** | Automated response | Serverless automation, incident remediation |
| **AWS X-Ray** | Distributed tracing | Application performance monitoring, request tracing |
| **VPC Flow Logs** | Network monitoring | Network traffic analysis, security investigation |
| **CloudWatch Logs Insights** | Log analysis | Fast queries, pattern detection, visualization |

## Implementation Steps

### Phase 1: Logging Foundation (Week 1-2)

#### 1. Enable Comprehensive CloudTrail Logging

```bash
# Create organization-wide CloudTrail
aws cloudtrail create-trail \
  --name org-security-trail \
  --s3-bucket-name security-logs-central-bucket \
  --is-multi-region-trail \
  --is-organization-trail \
  --enable-log-file-validation \
  --include-global-service-events

# Enable CloudTrail Insights for anomaly detection
aws cloudtrail put-insight-selectors \
  --trail-name org-security-trail \
  --insight-selectors '[{
    "InsightType": "ApiCallRateInsight"
  }]'

# Log all data events for S3
aws cloudtrail put-event-selectors \
  --trail-name org-security-trail \
  --event-selectors '[{
    "ReadWriteType": "All",
    "IncludeManagementEvents": true,
    "DataResources": [{
      "Type": "AWS::S3::Object",
      "Values": ["arn:aws:s3:::*/"]
    }]
  }]'

# Start logging
aws cloudtrail start-logging --name org-security-trail
```

**Configuration Checklist**:
- ✅ Multi-region trail enabled
- ✅ Organization trail covers all accounts
- ✅ Log file validation enabled (integrity protection)
- ✅ S3 bucket encryption enabled (SSE-KMS)
- ✅ S3 bucket lifecycle policy (retain 90 days hot, 1 year archive, 7 years glacier)
- ✅ CloudTrail Insights enabled for anomaly detection
- ✅ Data events captured for sensitive resources (S3, Lambda)

#### 2. Configure VPC Flow Logs

```bash
# Enable VPC Flow Logs for all VPCs
for vpc in $(aws ec2 describe-vpcs --query 'Vpcs[*].VpcId' --output text); do
  aws ec2 create-flow-logs \
    --resource-type VPC \
    --resource-ids $vpc \
    --traffic-type ALL \
    --log-destination-type cloud-watch-logs \
    --log-group-name "/aws/vpc/flowlogs/${vpc}" \
    --deliver-logs-permission-arn arn:aws:iam::ACCOUNT-ID:role/VPCFlowLogsRole \
    --tag-specifications "ResourceType=vpc-flow-log,Tags=[{Key=Environment,Value=Production}]"
done
```

**Flow Log Configuration**:
- Traffic type: ALL (accept and reject)
- Destination: CloudWatch Logs (for analysis) + S3 (for long-term retention)
- Format: Custom format with additional fields
- Retention: 90 days in CloudWatch, 7 years in S3

**Custom Flow Log Format** (enhanced visibility):
```
${version} ${account-id} ${interface-id} ${srcaddr} ${dstaddr} ${srcport} ${dstport} ${protocol} ${packets} ${bytes} ${start} ${end} ${action} ${log-status} ${vpc-id} ${subnet-id} ${instance-id} ${tcp-flags} ${type} ${pkt-srcaddr} ${pkt-dstaddr} ${region} ${az-id} ${sublocation-type} ${sublocation-id}
```

#### 3. Enable AWS Config

```bash
# Create configuration recorder
aws configservice put-configuration-recorder \
  --configuration-recorder name=default,roleARN=arn:aws:iam::ACCOUNT-ID:role/ConfigRole \
  --recording-group allSupported=true,includeGlobalResourceTypes=true

# Set up delivery channel
aws configservice put-delivery-channel \
  --delivery-channel name=default,s3BucketName=config-logs-bucket,configSnapshotDeliveryProperties={deliveryFrequency=TwentyFour_Hours}

# Start configuration recorder
aws configservice start-configuration-recorder \
  --configuration-recorder-name default

# Enable organization-wide compliance rules
aws configservice put-organization-config-rule \
  --organization-config-rule-name encrypted-volumes \
  --organization-managed-rule-metadata RuleIdentifier=ENCRYPTED_VOLUMES,ResourceTypesScope=AWS::EC2::Volume
```

**Essential Config Rules**:
- `ENCRYPTED_VOLUMES` - All EBS volumes must be encrypted
- `S3_BUCKET_PUBLIC_READ_PROHIBITED` - No public read access
- `S3_BUCKET_PUBLIC_WRITE_PROHIBITED` - No public write access
- `IAM_PASSWORD_POLICY` - Password policy compliance
- `MFA_ENABLED_FOR_IAM_CONSOLE_ACCESS` - MFA required
- `ROOT_ACCOUNT_MFA_ENABLED` - Root account MFA
- `CLOUD_TRAIL_ENABLED` - CloudTrail active in all regions
- `GUARDDUTY_ENABLED_CENTRALIZED` - GuardDuty enabled

### Phase 2: Monitoring & Detection (Week 3-4)

#### 4. Deploy GuardDuty

```bash
# Enable GuardDuty in all regions
for region in us-east-1 us-west-2 eu-west-1; do
  aws guardduty create-detector \
    --enable \
    --region $region \
    --finding-publishing-frequency FIFTEEN_MINUTES
done

# For organization: designate delegated administrator
aws guardduty enable-organization-admin-account \
  --admin-account-id SECURITY-ACCOUNT-ID

# Enable S3 protection and Kubernetes audit logs
aws guardduty update-detector \
  --detector-id DETECTOR-ID \
  --data-sources S3Logs={Enable=true},Kubernetes={AuditLogs={Enable=true}}
```

#### 5. Configure Security Hub

```bash
# Enable Security Hub
aws securityhub enable-security-hub \
  --enable-default-standards

# Enable specific standards
aws securityhub batch-enable-standards \
  --standards-subscription-requests '[
    {"StandardsArn": "arn:aws:securityhub:REGION::standards/aws-foundational-security-best-practices/v/1.0.0"},
    {"StandardsArn": "arn:aws:securityhub:REGION::standards/cis-aws-foundations-benchmark/v/1.2.0"},
    {"StandardsArn": "arn:aws:securityhub:REGION::standards/pci-dss/v/3.2.1"}
  ]'

# Enable integrations
aws securityhub update-findings \
  --filters '{"ProductName":[{"Value":"GuardDuty","Comparison":"EQUALS"}]}'
```

**Security Hub Configuration**:
- Enable AWS Foundational Security Best Practices
- Enable CIS AWS Foundations Benchmark
- Enable PCI-DSS (if applicable)
- Integrate GuardDuty, Config, Inspector, Macie
- Configure automated remediation via EventBridge

#### 6. Create CloudWatch Dashboards

```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "title": "Security Events - Last 24 Hours",
        "metrics": [
          ["AWS/GuardDuty", "FindingCount", {"stat": "Sum", "label": "GuardDuty Findings"}],
          ["AWS/Config", "ComplianceViolations", {"stat": "Sum", "label": "Config Violations"}],
          ["AWS/CloudTrail", "UnauthorizedAPICallCount", {"stat": "Sum", "label": "Unauthorized API Calls"}]
        ],
        "period": 300,
        "stat": "Sum",
        "region": "us-east-1",
        "yAxis": {"left": {"min": 0}}
      }
    },
    {
      "type": "log",
      "properties": {
        "title": "Failed Authentication Attempts",
        "query": "SOURCE '/aws/cloudtrail/logs'\n| fields @timestamp, userIdentity.principalId, sourceIPAddress, errorCode\n| filter errorCode = 'UnauthorizedOperation' or errorCode = 'AccessDenied'\n| sort @timestamp desc\n| limit 100",
        "region": "us-east-1"
      }
    },
    {
      "type": "metric",
      "properties": {
        "title": "Application Performance",
        "metrics": [
          ["AWS/ApplicationELB", "TargetResponseTime", {"stat": "Average"}],
          [".", "HTTPCode_Target_5XX_Count", {"stat": "Sum"}],
          [".", "RequestCount", {"stat": "Sum"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1"
      }
    },
    {
      "type": "metric",
      "properties": {
        "title": "Database Performance",
        "metrics": [
          ["AWS/RDS", "CPUUtilization", {"stat": "Average"}],
          [".", "DatabaseConnections", {"stat": "Sum"}],
          [".", "ReadLatency", {"stat": "Average"}],
          [".", "WriteLatency", {"stat": "Average"}]
        ],
        "period": 300,
        "region": "us-east-1"
      }
    }
  ]
}
```

#### 7. Set Up Critical Alarms

```bash
# Create SNS topic for security alerts
aws sns create-topic --name security-critical-alerts
aws sns subscribe \
  --topic-arn arn:aws:sns:REGION:ACCOUNT:security-critical-alerts \
  --protocol email \
  --notification-endpoint security-team@company.com

# Alarm: Root account usage
aws cloudwatch put-metric-alarm \
  --alarm-name "RootAccountUsage" \
  --alarm-description "Alert when root account is used" \
  --metric-name RootAccountUsage \
  --namespace CISBenchmark \
  --statistic Sum \
  --period 60 \
  --threshold 1 \
  --comparison-operator GreaterThanOrEqualToThreshold \
  --evaluation-periods 1 \
  --alarm-actions arn:aws:sns:REGION:ACCOUNT:security-critical-alerts

# Alarm: Unauthorized API calls
aws cloudwatch put-metric-alarm \
  --alarm-name "UnauthorizedAPICalls" \
  --alarm-description "Alert on unauthorized API attempts" \
  --metric-name UnauthorizedAPICallCount \
  --namespace CloudTrailMetrics \
  --statistic Sum \
  --period 300 \
  --threshold 5 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --alarm-actions arn:aws:sns:REGION:ACCOUNT:security-critical-alerts

# Alarm: GuardDuty high severity findings
aws cloudwatch put-metric-alarm \
  --alarm-name "GuardDutyHighSeverity" \
  --alarm-description "Alert on high severity GuardDuty findings" \
  --metric-name FindingCount \
  --namespace AWS/GuardDuty \
  --statistic Sum \
  --period 300 \
  --threshold 1 \
  --comparison-operator GreaterThanOrEqualToThreshold \
  --evaluation-periods 1 \
  --dimensions Name=Severity,Value=High \
  --alarm-actions arn:aws:sns:REGION:ACCOUNT:security-critical-alerts
```

### Phase 3: Incident Response Automation (Week 5-6)

#### 8. Automated Incident Response

**EventBridge Rule for GuardDuty Findings**:
```json
{
  "source": ["aws.guardduty"],
  "detail-type": ["GuardDuty Finding"],
  "detail": {
    "severity": [7, 8, 8.8]
  }
}
```

**Lambda Response Function**:
```python
import boto3
import json
import os

ec2 = boto3.client('ec2')
sns = boto3.client('sns')
guardduty = boto3.client('guardduty')

def lambda_handler(event, context):
    """
    Automated response to GuardDuty findings
    """
    finding = event['detail']
    finding_type = finding['type']
    severity = finding['severity']
    
    # Extract resource information
    resource = finding.get('resource', {})
    instance_id = resource.get('instanceDetails', {}).get('instanceId')
    
    response_actions = []
    
    # High severity findings - isolate instance
    if severity >= 7 and instance_id:
        try:
            # Create forensics security group (deny all)
            forensics_sg = create_forensics_sg()
            
            # Move instance to isolation security group
            ec2.modify_instance_attribute(
                InstanceId=instance_id,
                Groups=[forensics_sg]
            )
            
            # Create snapshot for forensics
            volumes = ec2.describe_instance_attribute(
                InstanceId=instance_id,
                Attribute='blockDeviceMapping'
            )
            
            for volume in volumes['BlockDeviceMappings']:
                volume_id = volume['Ebs']['VolumeId']
                snapshot = ec2.create_snapshot(
                    VolumeId=volume_id,
                    Description=f'Forensics snapshot for {instance_id} - {finding_type}',
                    TagSpecifications=[{
                        'ResourceType': 'snapshot',
                        'Tags': [
                            {'Key': 'Purpose', 'Value': 'Forensics'},
                            {'Key': 'IncidentId', 'Value': finding['id']},
                            {'Key': 'FindingType', 'Value': finding_type}
                        ]
                    }]
                )
                response_actions.append(f"Created forensics snapshot: {snapshot['SnapshotId']}")
            
            response_actions.append(f"Isolated instance {instance_id}")
            
        except Exception as e:
            response_actions.append(f"Error isolating instance: {str(e)}")
    
    # Notify security team
    notification = {
        'Finding': finding_type,
        'Severity': severity,
        'Description': finding.get('description', 'No description'),
        'Resource': instance_id or 'N/A',
        'Actions': response_actions,
        'FindingId': finding['id']
    }
    
    sns.publish(
        TopicArn=os.environ['SNS_TOPIC_ARN'],
        Subject=f'GuardDuty Alert: {finding_type}',
        Message=json.dumps(notification, indent=2)
    )
    
    # Create incident ticket in ticketing system
    create_incident_ticket(finding, response_actions)
    
    return {
        'statusCode': 200,
        'body': json.dumps(response_actions)
    }

def create_forensics_sg():
    """Create security group that denies all traffic for forensics"""
    try:
        response = ec2.describe_security_groups(
            Filters=[{'Name': 'group-name', 'Values': ['forensics-isolation']}]
        )
        if response['SecurityGroups']:
            return response['SecurityGroups'][0]['GroupId']
    except:
        pass
    
    # Create new forensics SG
    vpc_id = os.environ['VPC_ID']
    sg = ec2.create_security_group(
        GroupName='forensics-isolation',
        Description='Isolation security group for compromised instances',
        VpcId=vpc_id,
        TagSpecifications=[{
            'ResourceType': 'security-group',
            'Tags': [{'Key': 'Purpose', 'Value': 'Forensics'}]
        }]
    )
    
    # Remove default egress rule
    ec2.revoke_security_group_egress(
        GroupId=sg['GroupId'],
        IpPermissions=[{
            'IpProtocol': '-1',
            'IpRanges': [{'CidrIp': '0.0.0.0/0'}]
        }]
    )
    
    return sg['GroupId']

def create_incident_ticket(finding, actions):
    """Create ticket in incident management system"""
    # Integration with ServiceNow, Jira, PagerDuty, etc.
    pass
```

#### 9. Configure Log Retention and Archival

```python
import boto3
from datetime import datetime, timedelta

logs = boto3.client('logs')
s3 = boto3.client('s3')

def configure_log_retention():
    """
    Set retention policies for all log groups
    """
    retention_policies = {
        '/aws/lambda/': 90,           # Lambda logs: 90 days
        '/aws/rds/': 90,              # RDS logs: 90 days
        '/aws/ecs/': 60,              # ECS logs: 60 days
        '/aws/eks/': 90,              # EKS logs: 90 days
        '/aws/cloudtrail/': 2555,     # CloudTrail: 7 years
        'security': 2555,              # Security logs: 7 years
        'audit': 2555                  # Audit logs: 7 years
    }
    
    log_groups = logs.describe_log_groups()
    
    for log_group in log_groups['logGroups']:
        log_group_name = log_group['logGroupName']
        
        # Determine retention based on prefix
        retention_days = 30  # default
        for prefix, days in retention_policies.items():
            if prefix in log_group_name:
                retention_days = days
                break
        
        # Set retention
        logs.put_retention_policy(
            logGroupName=log_group_name,
            retentionInDays=retention_days
        )
        
        print(f"Set retention for {log_group_name}: {retention_days} days")

def archive_logs_to_s3():
    """
    Export logs to S3 for long-term archival
    """
    log_groups = logs.describe_log_groups()
    
    for log_group in log_groups['logGroups']:
        # Export logs older than 90 days to S3
        from_time = int((datetime.now() - timedelta(days=180)).timestamp() * 1000)
        to_time = int((datetime.now() - timedelta(days=90)).timestamp() * 1000)
        
        try:
            response = logs.create_export_task(
                logGroupName=log_group['logGroupName'],
                fromTime=from_time,
                to=to_time,
                destination='log-archive-bucket',
                destinationPrefix=f"exports/{log_group['logGroupName']}/"
            )
            print(f"Exporting {log_group['logGroupName']} to S3: {response['taskId']}")
        except Exception as e:
            print(f"Error exporting {log_group['logGroupName']}: {str(e)}")
```

## Evidence Collection Tips

### Required Evidence Types

#### 1. Logging Configuration

**What Auditors Want**:
- CloudTrail configuration showing organization-wide coverage
- VPC Flow Logs enablement across all VPCs
- Application and system logs retention settings
- Log integrity verification mechanisms

**How to Collect**:
```bash
# CloudTrail configuration
aws cloudtrail describe-trails > cloudtrail-config.json
aws cloudtrail get-trail-status --name org-security-trail > cloudtrail-status.json

# VPC Flow Logs inventory
aws ec2 describe-flow-logs --output json > vpc-flow-logs.json

# CloudWatch Logs groups and retention
aws logs describe-log-groups --output json > log-groups-retention.json

# Config recording status
aws configservice describe-configuration-recorders > config-recorders.json
aws configservice describe-delivery-channels > config-delivery.json
```

#### 2. Monitoring and Alerting Configuration

**What Auditors Want**:
- List of all alarms and their thresholds
- Evidence of alarm testing
- Alert distribution lists
- Dashboards showing monitoring coverage

**How to Collect**:
```bash
# Export all CloudWatch alarms
aws cloudwatch describe-alarms --output json > cloudwatch-alarms.json

# Export SNS topics and subscriptions
aws sns list-topics > sns-topics.json
for topic in $(aws sns list-topics --query 'Topics[*].TopicArn' --output text); do
  aws sns list-subscriptions-by-topic --topic-arn $topic >> sns-subscriptions.json
done

# Export EventBridge rules
aws events list-rules --output json > eventbridge-rules.json

# Dashboard configurations
aws cloudwatch list-dashboards > dashboards-list.json
```

**Screenshot Requirements**:
- Security dashboard showing key metrics
- GuardDuty console showing active monitoring
- Security Hub compliance scores
- Sample alerts from last 30 days

#### 3. Incident Response Evidence

**What Auditors Want**:
- Incident response procedures documentation
- Evidence of incident detection
- Incident handling records
- Post-incident reviews

**Documentation Template**:
```markdown
# Incident Response Log - 2024

## Incident IR-2024-003

### Detection
- **Date/Time**: 2024-02-15 14:23 UTC
- **Source**: GuardDuty finding "UnauthorizedAccess:EC2/SSHBruteForce"
- **Severity**: Medium (5.0)
- **Alert Method**: CloudWatch alarm triggered SNS notification

### Analysis
- **Affected Resource**: EC2 instance i-0abc123def456
- **Attack Source**: IP 198.51.100.42 (known malicious)
- **Activity**: 147 failed SSH login attempts over 15 minutes
- **Data Accessed**: None - all attempts failed

### Containment
- **Actions Taken**:
  1. Updated security group to deny source IP (14:30 UTC)
  2. Verified no successful authentications occurred
  3. Isolated instance in forensics SG for analysis
  4. Created EBS snapshots for forensics

### Eradication
- **Root Cause**: Security group rule allowing SSH from 0.0.0.0/0
- **Remediation**: 
  1. Removed overly permissive SSH rule
  2. Implemented bastion host requirement
  3. Enabled Systems Manager Session Manager

### Recovery
- **Timeline**: Instance returned to service 2024-02-15 16:45 UTC
- **Validation**: No malicious activity detected post-recovery

### Lessons Learned
- **Process Improvements**: 
  1. Implemented Config rule to detect public SSH access
  2. Added automated remediation for public SSH rules
  3. Updated runbooks with bastion host procedures
- **Training**: Conducted team training on secure remote access
```

#### 4. Security Findings and Remediation

**What Auditors Want**:
- Security Hub findings over audit period
- Evidence of remediation for critical/high findings
- Remediation timelines
- Exception approvals for accepted risks

**How to Collect**:
```bash
# Export Security Hub findings
aws securityhub get-findings \
  --filters '{"CreatedAt": [{"DateRange": {"Value": 90, "Unit": "DAYS"}}]}' \
  --max-items 1000 > security-hub-findings.json

# Export Config compliance data
aws configservice describe-compliance-by-config-rule > config-compliance.json

# GuardDuty findings
aws guardduty list-findings \
  --detector-id DETECTOR-ID \
  --finding-criteria '{"Criterion": {"severity": {"Gte": 7}}}' \
  > guardduty-findings.json
```

**Remediation Tracking Spreadsheet**:
| Finding ID | Service | Severity | Date Detected | Status | Remediation Date | Owner | Notes |
|------------|---------|----------|---------------|--------|------------------|-------|-------|
| SEC-001 | Security Hub | Critical | 2024-01-15 | Closed | 2024-01-15 | DevOps | Auto-remediated |
| SEC-002 | GuardDuty | High | 2024-01-20 | Closed | 2024-01-22 | Security | Investigated false positive |

#### 5. Performance and Capacity Monitoring

**What Auditors Want**:
- Evidence of capacity monitoring
- Performance baseline documentation
- Response to performance degradation
- Capacity planning documentation

**How to Collect**:
```bash
# Export key performance metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-03-31T23:59:59Z \
  --period 86400 \
  --statistics Average,Maximum > cpu-utilization-q1.json

# Database performance metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/RDS \
  --metric-name DatabaseConnections \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-03-31T23:59:59Z \
  --period 86400 \
  --statistics Average,Maximum > rds-connections-q1.json
```

**Capacity Planning Document**:
```markdown
# Quarterly Capacity Review - Q1 2024

## Current Utilization
- EC2: Average 45% CPU, Peak 78%
- RDS: Average 320 connections, Peak 487 (Max: 500)
- S3: 2.4 TB stored, growing 15% month-over-month
- Data Transfer: 8 TB/month average

## Growth Projections
- Q2 2024: Expect 25% increase in traffic
- Peak capacity sufficient with current infrastructure
- RDS connection limit may require attention by Q3

## Planned Actions
- Monitor RDS connection pool utilization weekly
- Evaluate RDS instance upgrade for Q3
- Implement connection pooling in application layer
```

## Common Audit Questions

### Question 1: "How do you monitor your AWS environment for security events?"

**Strong Answer**:
"We implement comprehensive, multi-layered monitoring:

1. **Real-Time Threat Detection**: GuardDuty continuously analyzes CloudTrail logs, VPC Flow Logs, and DNS logs using machine learning to detect threats like credential compromise, cryptocurrency mining, and data exfiltration.

2. **Configuration Monitoring**: AWS Config tracks all resource configurations and evaluates them against our compliance rules. We receive immediate alerts when resources drift from compliant states.

3. **Centralized Security View**: Security Hub aggregates findings from GuardDuty, Config, Inspector, and Macie, giving us a single dashboard of our security posture across all accounts and regions.

4. **Application Monitoring**: CloudWatch collects metrics, logs, and traces from our applications. We use CloudWatch Insights to query logs for suspicious patterns.

5. **Network Monitoring**: VPC Flow Logs capture all network traffic, which we analyze for anomalous patterns and security group violations.

All findings severity High or above trigger immediate alerts to our security team via PagerDuty, with automated initial response for known scenarios."

**Evidence to Provide**:
- GuardDuty detector configuration showing multi-region coverage
- Security Hub dashboard screenshots
- List of Config rules with compliance scores
- CloudWatch dashboard showing security metrics
- Sample alert notification showing end-to-end flow

### Question 2: "What logs do you collect and how long do you retain them?"

**Strong Answer**:
"We maintain comprehensive logging with retention aligned to security and compliance requirements:

**Audit and Security Logs (7-year retention)**:
- CloudTrail: All API calls across all accounts and regions
- VPC Flow Logs: All network traffic (accept and reject)
- S3 access logs: All object-level operations
- CloudFront access logs: All edge requests
- Application audit logs: Business transactions, access events

**Operational Logs (90-day retention)**:
- Application logs from Lambda, ECS, EKS
- Database logs from RDS (error, slow query, audit)
- Load balancer access logs
- Route 53 query logs

**Short-Term Logs (30-day retention)**:
- Container stdout/stderr
- Debug-level application logs

All logs are stored encrypted in S3 with lifecycle policies. CloudWatch Logs feeds our real-time analysis, then we export to S3 for long-term retention. We verify log integrity through CloudTrail's digest files and S3 object lock."

**Evidence to Provide**:
- Log retention policy document
- CloudWatch Logs groups with retention settings
- S3 bucket lifecycle policies
- CloudTrail log file validation configuration
- Sample log export showing encryption and integrity

### Question 3: "How do you respond to security incidents?"

**Strong Answer**:
"We have documented incident response procedures with automated initial response:

**Detection (< 5 minutes)**:
- GuardDuty, Security Hub, and CloudWatch alarms detect anomalies
- High-severity findings trigger immediate PagerDuty alerts
- EventBridge rules route findings to Lambda for triage

**Automated Response (< 15 minutes)**:
- Lambda functions execute initial containment:
  - Isolate compromised instances in forensics security groups
  - Create EBS snapshots for forensics
  - Revoke exposed credentials
  - Block malicious IPs in WAF/security groups
- Systems Manager Run Command executes remediation scripts
- SNS notifies security team with initial analysis

**Human Investigation (< 1 hour SLA for critical)**:
- Security analyst reviews automated actions
- Investigates using CloudWatch Logs Insights and Athena
- Escalates to incident commander if needed
- Documents findings in incident ticket (Jira)

**Remediation and Recovery**:
- Implement fixes based on root cause analysis
- Update detective controls to prevent recurrence
- Conduct post-incident review within 48 hours
- Update runbooks and automation

We conduct quarterly incident response tabletops and annual red team exercises."

**Evidence to Provide**:
- Incident response playbook document
- Lambda auto-response code
- EventBridge rule configurations
- Sample incident ticket showing full lifecycle
- Post-incident review documents
- Tabletop exercise reports

### Question 4: "How do you ensure your monitoring and logging systems are working correctly?"

**Strong Answer**:
"We validate our monitoring and logging infrastructure through multiple mechanisms:

**Automated Health Checks**:
- CloudWatch alarms monitor CloudTrail delivery (alerts if no logs received for 1 hour)
- Config rules verify logging is enabled on all resources
- Lambda functions perform daily synthetic tests:
  - Generate test API calls and verify they appear in CloudTrail
  - Create test security group rules and verify Config detects them
  - Trigger test GuardDuty findings to validate alerting pipeline

**Monthly Validation**:
- Security team reviews a sample of alerts to verify proper routing
- Test emergency contact procedures (email, SMS, PagerDuty)
- Verify log query tools (CloudWatch Insights, Athena) return expected results
- Check S3 log buckets for consistent delivery and no gaps

**Quarterly Audits**:
- Review all CloudWatch alarms for accuracy and relevance
- Validate EventBridge rules are triggering correctly
- Test incident response automation end-to-end
- Review and update alert thresholds based on operational patterns

**Continuous Monitoring**:
- CloudWatch metrics track log ingestion volume
- Alarms trigger if log volume drops unexpectedly
- AWS Config continuously validates logging configurations
- Security Hub compliance checks verify monitoring coverage

We maintain runbooks for common monitoring issues and have escalation procedures if logging systems fail."

**Evidence to Provide**:
- Monitoring health check Lambda function code
- CloudWatch alarm for CloudTrail delivery failures
- Config rules monitoring logging enablement
- Monthly validation checklist with sign-offs
- Sample synthetic test results

### Question 5: "How do you handle capacity management and performance monitoring?"

**Strong Answer**:
"We proactively manage capacity through continuous monitoring and forecasting:

**Real-Time Performance Monitoring**:
- CloudWatch dashboards track key performance indicators:
  - Application: Response time, error rates, throughput
  - Database: CPU, connections, IOPS, storage
  - Infrastructure: CPU, memory, network, disk utilization
- Alarms set at 70% (warning) and 85% (critical) of capacity thresholds
- Auto-scaling policies automatically adjust capacity for elastic workloads

**Capacity Analysis**:
- Weekly review of performance trends
- Monthly capacity reports comparing utilization to thresholds
- Quarterly capacity planning meetings with engineering teams
- Annual architecture reviews for scaling optimization

**Proactive Planning**:
- CloudWatch anomaly detection identifies unusual patterns
- We maintain 30% overhead capacity for production systems
- Load testing before major releases validates capacity
- Growth forecasts based on business metrics (users, transactions, data volume)

**Performance Baselines**:
- Documented baseline performance for each service
- Automated comparison of current vs. baseline performance
- Alerts when performance degrades beyond acceptable thresholds (e.g., API response time > 500ms)

If capacity limits are approached, we have documented escalation procedures to provision additional resources before customer impact."

**Evidence to Provide**:
- CloudWatch dashboard showing capacity metrics
- Quarterly capacity planning documents
- Auto-scaling policy configurations
- Performance baseline documentation
- Sample capacity alert with response actions
- Load testing results and capacity validation

### Question 6: "How do you detect and respond to unauthorized access attempts?"

**Strong Answer**:
"We have multiple detection layers for unauthorized access:

**Detection Mechanisms**:
1. **Failed Authentication Monitoring**: CloudWatch Insights queries CloudTrail for failed login attempts. Five failures within 15 minutes triggers an alert.

2. **GuardDuty Detections**: Identifies suspicious activity like:
   - Compromised credentials used from unusual locations
   - API calls from known malicious IPs
   - Unusual API call patterns suggesting reconnaissance

3. **Anomaly Detection**: CloudTrail Insights uses ML to detect unusual API activity volumes, flagging potential brute force or credential stuffing attacks.

4. **Network Monitoring**: VPC Flow Logs analyzed for connection attempts to closed ports, unusual protocols, or denied connections suggesting scanning activity.

**Automated Response**:
- High-confidence threats (known malicious IPs) blocked immediately via WAF/security groups
- Potentially compromised credentials automatically disabled
- Account owners notified to verify legitimate activity
- All unauthorized access attempts logged to security ticket system

**Investigation Process**:
- Security analyst reviews source IP, user agent, geolocation
- Correlates with legitimate user activity patterns
- Checks for successful access following failed attempts
- Determines if data was accessed or modified
- Documents findings and recommendations

**Preventive Controls**:
- MFA required for all access (prevents credential compromise)
- IP allowlisting for production environments
- AWS WAF rate limiting to prevent brute force
- Account lockout after repeated failures (via Cognito/IdP)

All unauthorized access attempts are retained for 7 years in CloudTrail and reviewed during quarterly security reviews."

**Evidence to Provide**:
- CloudWatch Logs Insights query for failed auth attempts
- GuardDuty findings related to unauthorized access
- Sample security ticket from unauthorized access detection
- WAF rules for rate limiting and IP blocking
- Quarterly unauthorized access review report
- Response runbook for compromised credentials

### Question 7: "What evidence do you have that your monitoring detected a real security issue and you responded appropriately?"

**Strong Answer**:
"Here's a recent example demonstrating our detection and response capability:

**Incident**: On February 15, 2024, GuardDuty detected 'UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration' - a High severity finding indicating potential credential theft from an EC2 instance.

**Detection Timeline**:
- 14:23 UTC: GuardDuty finding generated
- 14:24 UTC: CloudWatch alarm triggered
- 14:24 UTC: PagerDuty alert sent to security on-call
- 14:25 UTC: Automated Lambda function disabled the compromised IAM credentials

**Investigation** (14:25-14:45 UTC):
- Reviewed CloudTrail logs: Credentials used from IP in different region than instance
- Analyzed VPC Flow Logs: No unusual network traffic from instance itself
- Checked application logs: Application was making legitimate AWS API calls
- Root cause: Application accidentally logged AWS credentials to application logs, which were exported to external log aggregation service

**Remediation** (14:45-16:30 UTC):
- Rotated all credentials for the application
- Removed credentials from application logs
- Implemented AWS Secrets Manager for credential management
- Updated application code to use IAM roles instead of long-term credentials
- Reviewed external log aggregation service for exposed credentials

**Prevention** (Next 48 hours):
- Deployed Macie to scan S3 buckets for exposed credentials
- Implemented Config rule to detect IAM access keys in application code
- Added automated scanning in CI/CD pipeline for credential exposure
- Conducted team training on secure credential management
- Updated security runbooks

**Post-Incident Review**: Completed February 17, documented lessons learned, no customer data was accessed, total response time 2 hours 7 minutes."

**Evidence to Provide**:
- Complete incident ticket (Jira/ServiceNow) with timeline
- GuardDuty finding JSON
- CloudTrail logs showing unauthorized API calls
- Lambda function logs showing automated credential disablement
- Application code changes (sanitized)
- Post-incident review document
- Follow-up security improvements implemented

## Continuous Compliance

### Daily Tasks
- [ ] Review Security Hub dashboard for new critical/high findings
- [ ] Check GuardDuty for threat detections
- [ ] Monitor CloudWatch alarms for any triggered alerts
- [ ] Verify CloudTrail delivery (automated check)

### Weekly Tasks
- [ ] Review performance trends and capacity utilization
- [ ] Analyze CloudWatch Logs for security anomalies
- [ ] Check for new compliance violations in AWS Config
- [ ] Review and triage medium-severity security findings

### Monthly Tasks
- [ ] Validate monitoring and logging health checks
- [ ] Review and update CloudWatch alarm thresholds
- [ ] Generate capacity utilization report
- [ ] Test incident response automation
- [ ] Review unauthorized access attempt logs
- [ ] Update monitoring dashboards as needed

### Quarterly Tasks
- [ ] Conduct formal incident response tabletop exercise
- [ ] Review and update incident response playbooks
- [ ] Capacity planning meeting with stakeholders
- [ ] Audit log retention compliance
- [ ] Review and optimize CloudWatch costs
- [ ] Security findings remediation review
- [ ] Update monitoring documentation

### Annual Tasks
- [ ] Red team exercise to test detection capabilities
- [ ] Comprehensive review of all monitoring and alerting
- [ ] Review and update performance baselines
- [ ] Audit trail integrity verification
- [ ] Disaster recovery test including log recovery
- [ ] Training refresh for operations and security teams

## Integration Patterns

### Monitoring Stack Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Data Sources                             │
├─────────────────────────────────────────────────────────────┤
│ CloudTrail │ VPC Flow │ Config │ GuardDuty │ Applications  │
└──────┬──────────┬────────┬─────────┬──────────────┬─────────┘
       │          │        │         │              │
       v          v        v         v              v
┌─────────────────────────────────────────────────────────────┐
│              Aggregation & Analysis Layer                    │
├─────────────────────────────────────────────────────────────┤
│  CloudWatch Logs  │  Security Hub  │  EventBridge          │
└──────────┬─────────────────┬─────────────────┬─────────────┘
           │                 │                 │
           v                 v                 v
┌─────────────────────────────────────────────────────────────┐
│              Response & Notification Layer                   │
├─────────────────────────────────────────────────────────────┤
│  Lambda Auto-Response │  SNS/PagerDuty  │  Ticket System   │
└─────────────────────────────────────────────────────────────┘
```

### Sample EventBridge Integration

```json
{
  "version": "0",
  "id": "event-id",
  "detail-type": "GuardDuty Finding",
  "source": "aws.guardduty",
  "account": "123456789012",
  "time": "2024-10-30T12:00:00Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "schemaVersion": "2.0",
    "accountId": "123456789012",
    "region": "us-east-1",
    "partition": "aws",
    "id": "finding-id",
    "arn": "arn:aws:guardduty:us-east-1:123456789012:detector/detector-id/finding/finding-id",
    "type": "UnauthorizedAccess:EC2/SSHBruteForce",
    "resource": {
      "resourceType": "Instance",
      "instanceDetails": {
        "instanceId": "i-0abc123def456",
        "instanceType": "t3.medium"
      }
    },
    "service": {
      "serviceName": "guardduty",
      "detectorId": "detector-id",
      "action": {
        "actionType": "NETWORK_CONNECTION",
        "networkConnectionAction": {
          "connectionDirection": "INBOUND",
          "remoteIpDetails": {
            "ipAddressV4": "198.51.100.42"
          }
        }
      },
      "eventFirstSeen": "2024-10-30T11:45:00Z",
      "eventLastSeen": "2024-10-30T12:00:00Z",
      "archived": false,
      "count": 147
    },
    "severity": 5,
    "title": "SSH brute force attack detected",
    "description": "EC2 instance has been involved in SSH brute force attacks."
  }
}
```

## Additional Resources

### AWS Documentation
- [AWS Security Best Practices](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/welcome.html)
- [CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)
- [GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/)
- [Security Hub User Guide](https://docs.aws.amazon.com/securityhub/latest/userguide/)
- [CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)

### Industry Standards
- [NIST SP 800-53 SI Controls (System and Information Integrity)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)
- [NIST SP 800-92 Guide to Computer Security Log Management](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-92.pdf)
- [CIS AWS Foundations Benchmark - Section 4 (Monitoring)](https://www.cisecurity.org/benchmark/amazon_web_services)

### Tools and Scripts
- [AWS Security Hub Automated Response and Remediation](https://github.com/aws-solutions/aws-security-hub-automated-response-and-remediation)
- [CloudWatch Logs Insights Query Examples](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-examples.html)
- [AWS Config Rules Repository](https://github.com/awslabs/aws-config-rules)

---
