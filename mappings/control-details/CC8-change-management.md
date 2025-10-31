# CC8: Change Management

## Control Family Overview

CC8 focuses on managing changes to information systems in a controlled manner that preserves security and minimizes risk. This includes changes to infrastructure, applications, security controls, and system configurations.

## Control Description

The entity implements change management processes to authorize, design, develop, configure, document, test, approve, and implement changes to infrastructure, data, software, and procedures. Key requirements include:

- **Change Authorization**: Formal approval process for all changes
- **Change Testing**: Validation of changes in non-production environments
- **Change Documentation**: Complete records of what changed, why, and by whom
- **Emergency Changes**: Procedures for urgent changes with appropriate controls
- **Rollback Capability**: Ability to reverse changes if issues occur
- **Change Communication**: Notification of changes to affected parties

## AWS Services That Address CC8

### Primary Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS CodePipeline** | CI/CD orchestration | Automated deployment pipelines, approval gates, rollback |
| **AWS CodeBuild** | Build automation | Automated testing, security scanning, artifact generation |
| **AWS CodeDeploy** | Deployment automation | Blue/green deployments, canary releases, automatic rollback |
| **AWS Config** | Configuration tracking | Resource change history, compliance monitoring, remediation |
| **AWS CloudFormation** | Infrastructure as Code | Change sets, stack updates, drift detection |

### Supporting Services

| AWS Service | Purpose | Key Features |
|------------|---------|--------------|
| **AWS Systems Manager** | Change management | Change Calendar, Automation, Change Manager |
| **AWS CloudTrail** | Audit logging | API change tracking, who/what/when documentation |
| **Amazon S3** | Artifact storage | Versioning, immutable builds, audit trail |
| **AWS Service Catalog** | Pre-approved resources | Standardized deployments, governance, version control |
| **AWS Organizations** | Policy enforcement | SCPs preventing unauthorized changes |
| **Amazon ECR** | Container registry | Image scanning, version control, immutability |
| **AWS Lambda** | Automation | Approval workflows, automated validations |

## Implementation Steps

### Phase 1: Infrastructure as Code Foundation (Week 1-2)

#### 1. Establish Infrastructure as Code (IaC) Standards

**Why IaC for CC8**:
- All infrastructure changes are code-reviewed
- Complete audit trail in version control
- Consistent, repeatable deployments
- Easy rollback to previous states
- Automated testing of infrastructure changes

**Repository Structure**:
```
infrastructure/
├── terraform/                 # Infrastructure definitions
│   ├── modules/              # Reusable components
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── production/
│   └── policies/             # Security policies
├── cloudformation/
│   ├── templates/
│   └── stacks/
├── .github/workflows/        # CI/CD pipelines
└── docs/
    ├── runbooks/
    └── architecture/
```

**Sample CloudFormation Template with Change Controls**:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Production Web Application Stack - Requires Approval for Changes'

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: "Change Management"
        Parameters:
          - ChangeTicket
          - ApprovalBy
          - ChangeReason

Parameters:
  ChangeTicket:
    Type: String
    Description: "JIRA ticket number authorizing this change (e.g., CHG-1234)"
    AllowedPattern: "^CHG-[0-9]{4,}$"
  
  ApprovalBy:
    Type: String
    Description: "Name of person who approved this change"
    
  ChangeReason:
    Type: String
    Description: "Brief description of why this change is needed"
    MinLength: 10
    MaxLength: 500

  InstanceType:
    Type: String
    Default: t3.medium
    AllowedValues:
      - t3.medium
      - t3.large
      - t3.xlarge
    Description: "EC2 instance type (requires CHG ticket to modify)"

Resources:
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !Ref LatestAmiId
      Tags:
        - Key: ChangeTicket
          Value: !Ref ChangeTicket
        - Key: ApprovedBy
          Value: !Ref ApprovalBy
        - Key: ChangeReason
          Value: !Ref ChangeReason
        - Key: ChangeDate
          Value: !Sub '${AWS::StackName}-${AWS::StackId}'

Outputs:
  ChangeMetadata:
    Description: "Change tracking information"
    Value: !Sub |
      Change Ticket: ${ChangeTicket}
      Approved By: ${ApprovalBy}
      Reason: ${ChangeReason}
      Timestamp: ${AWS::StackName}
```

#### 2. Implement Version Control with Branch Protection

```bash
# GitHub branch protection rules (via API or UI)
curl -X PUT \
  https://api.github.com/repos/org/infrastructure/branches/main/protection \
  -H "Authorization: token $GITHUB_TOKEN" \
  -d '{
    "required_status_checks": {
      "strict": true,
      "contexts": ["terraform-validate", "security-scan", "compliance-check"]
    },
    "enforce_admins": true,
    "required_pull_request_reviews": {
      "dismissal_restrictions": {},
      "dismiss_stale_reviews": true,
      "require_code_owner_reviews": true,
      "required_approving_review_count": 2
    },
    "restrictions": {
      "users": [],
      "teams": ["infrastructure-admins"],
      "apps": []
    }
  }'
```

**Branch Protection Requirements**:
- ✅ Require pull request reviews (minimum 2 approvers)
- ✅ Require status checks to pass before merging
- ✅ Require CODEOWNERS approval for protected paths
- ✅ Dismiss stale reviews when new commits pushed
- ✅ Restrict who can push to main branch
- ✅ Require signed commits
- ✅ Include administrators in restrictions

#### 3. Configure AWS Config for Change Tracking

```bash
# Enable Config in all regions
for region in us-east-1 us-west-2 eu-west-1; do
  aws configservice put-configuration-recorder \
    --configuration-recorder name=default,roleARN=arn:aws:iam::ACCOUNT:role/ConfigRole \
    --recording-group allSupported=true,includeGlobalResourceTypes=true \
    --region $region
    
  aws configservice start-configuration-recorder \
    --configuration-recorder-name default \
    --region $region
done

# Deploy Config rules for change management
aws configservice put-config-rule --config-rule '{
  "ConfigRuleName": "approved-amis-only",
  "Description": "Checks that EC2 instances use approved AMIs",
  "Source": {
    "Owner": "AWS",
    "SourceIdentifier": "APPROVED_AMIS_BY_ID"
  },
  "Scope": {
    "ComplianceResourceTypes": ["AWS::EC2::Instance"]
  },
  "InputParameters": "{\"amiIds\":\"ami-12345,ami-67890\"}"
}'

aws configservice put-config-rule --config-rule '{
  "ConfigRuleName": "required-tags-on-resources",
  "Description": "Checks that resources have required change management tags",
  "Source": {
    "Owner": "AWS",
    "SourceIdentifier": "REQUIRED_TAGS"
  },
  "InputParameters": "{\"tag1Key\":\"ChangeTicket\",\"tag2Key\":\"Environment\"}"
}'
```

### Phase 2: CI/CD Pipeline Implementation (Week 3-4)

#### 4. Build Automated Deployment Pipeline

**CodePipeline Definition**:
```yaml
# buildspec.yml for CodeBuild
version: 0.2

phases:
  pre_build:
    commands:
      - echo "Starting pre-build phase"
      - echo "Checking for change ticket..."
      - |
        if [ -z "$CHANGE_TICKET" ]; then
          echo "ERROR: CHANGE_TICKET environment variable not set"
          exit 1
        fi
      
      # Validate infrastructure code
      - terraform init
      - terraform validate
      - terraform fmt -check
      
      # Security scanning
      - tfsec . --format json > tfsec-results.json
      - checkov -d . --framework terraform --output json > checkov-results.json
      
      # Check for high-severity findings
      - |
        CRITICAL=$(jq '[.results[].rule_severity] | map(select(. == "CRITICAL")) | length' tfsec-results.json)
        if [ "$CRITICAL" -gt 0 ]; then
          echo "ERROR: Critical security findings detected"
          exit 1
        fi

  build:
    commands:
      - echo "Generating Terraform plan..."
      - terraform plan -out=tfplan -input=false
      - terraform show -json tfplan > tfplan.json
      
      # Analyze plan for risky changes
      - |
        DESTROYS=$(jq '[.resource_changes[] | select(.change.actions[] == "delete")] | length' tfplan.json)
        if [ "$DESTROYS" -gt 0 ]; then
          echo "WARNING: Plan includes $DESTROYS resource deletions"
          echo "Manual approval required for destructive changes"
        fi

  post_build:
    commands:
      - echo "Build complete"
      - echo "Change Ticket: $CHANGE_TICKET"
      - echo "Approved By: $APPROVER"
      - echo "Ready for manual approval"

artifacts:
  files:
    - tfplan
    - tfplan.json
    - tfsec-results.json
    - checkov-results.json
  name: terraform-artifacts

reports:
  security:
    files:
      - tfsec-results.json
      - checkov-results.json
    file-format: JSON
```

**Pipeline with Approval Gates**:
```python
import aws_cdk as cdk
from aws_cdk import (
    aws_codepipeline as codepipeline,
    aws_codepipeline_actions as cpactions,
    aws_codebuild as codebuild,
    aws_sns as sns,
    aws_iam as iam,
)

class InfrastructurePipeline(cdk.Stack):
    def __init__(self, scope, id, **kwargs):
        super().__init__(scope, id, **kwargs)
        
        # SNS topic for approval notifications
        approval_topic = sns.Topic(
            self, "ApprovalTopic",
            display_name="Infrastructure Change Approvals"
        )
        
        # Pipeline
        pipeline = codepipeline.Pipeline(
            self, "InfraPipeline",
            pipeline_name="infrastructure-deployment",
            restart_execution_on_update=True
        )
        
        # Source stage - GitHub
        source_output = codepipeline.Artifact("SourceOutput")
        source_action = cpactions.GitHubSourceAction(
            action_name="GitHub_Source",
            owner="your-org",
            repo="infrastructure",
            oauth_token=cdk.SecretValue.secrets_manager("github-token"),
            output=source_output,
            branch="main",
            trigger=cpactions.GitHubTrigger.WEBHOOK
        )
        
        pipeline.add_stage(
            stage_name="Source",
            actions=[source_action]
        )
        
        # Validate stage
        validate_project = codebuild.PipelineProject(
            self, "ValidateProject",
            build_spec=codebuild.BuildSpec.from_source_filename("buildspec.yml"),
            environment=codebuild.BuildEnvironment(
                build_image=codebuild.LinuxBuildImage.STANDARD_5_0,
                privileged=True
            )
        )
        
        validate_output = codepipeline.Artifact("ValidateOutput")
        validate_action = cpactions.CodeBuildAction(
            action_name="Validate_And_Plan",
            project=validate_project,
            input=source_output,
            outputs=[validate_output]
        )
        
        pipeline.add_stage(
            stage_name="Validate",
            actions=[validate_action]
        )
        
        # Manual approval for production
        approval_action = cpactions.ManualApprovalAction(
            action_name="Approve_Production_Deployment",
            notification_topic=approval_topic,
            additional_information="Review Terraform plan in artifacts before approving",
            external_entity_link="https://jira.company.com/browse/CHG-",
            run_order=1
        )
        
        pipeline.add_stage(
            stage_name="Approval",
            actions=[approval_action]
        )
        
        # Deploy stage
        deploy_project = codebuild.PipelineProject(
            self, "DeployProject",
            build_spec=codebuild.BuildSpec.from_object({
                "version": "0.2",
                "phases": {
                    "build": {
                        "commands": [
                            "terraform init",
                            "terraform apply -input=false tfplan",
                            "terraform output -json > outputs.json"
                        ]
                    }
                },
                "artifacts": {
                    "files": ["outputs.json"]
                }
            })
        )
        
        deploy_action = cpactions.CodeBuildAction(
            action_name="Deploy_To_Production",
            project=deploy_project,
            input=validate_output
        )
        
        pipeline.add_stage(
            stage_name="Deploy",
            actions=[deploy_action]
        )
```

#### 5. Implement Blue/Green Deployment Strategy

```python
# Lambda-based blue/green deployment with CodeDeploy
{
  "version": 0.0,
  "Resources": [
    {
      "TargetService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
          "TaskDefinition": "<TASK_DEFINITION>",
          "LoadBalancerInfo": {
            "ContainerName": "web-app",
            "ContainerPort": 8080
          },
          "PlatformVersion": "LATEST",
          "NetworkConfiguration": {
            "AwsvpcConfiguration": {
              "Subnets": ["subnet-1", "subnet-2"],
              "SecurityGroups": ["sg-12345"],
              "AssignPublicIp": "DISABLED"
            }
          }
        }
      }
    }
  ],
  "Hooks": [
    {
      "BeforeInstall": "LambdaFunctionToValidateBeforeTrafficShift"
    },
    {
      "AfterInstall": "LambdaFunctionToValidateAfterTrafficShift"
    },
    {
      "BeforeAllowTraffic": "LambdaFunctionToValidateBeforeAllowingProductionTraffic"
    },
    {
      "AfterAllowTraffic": "LambdaFunctionToValidateService"
    }
  ]
}
```

**Canary Deployment Configuration**:
```yaml
# CodeDeploy AppSpec for Lambda
version: 0.0
Resources:
  - MyFunction:
      Type: AWS::Lambda::Function
      Properties:
        Name: "production-api-function"
        Alias: "live"
        CurrentVersion: "5"
        TargetVersion: "6"

Hooks:
  - BeforeAllowTraffic: "PreTrafficHook"
  - AfterAllowTraffic: "PostTrafficHook"

# Deployment configuration
DeploymentPreference:
  Type: Canary10Percent5Minutes
  Alarms:
    - ErrorRateAlarm
    - LatencyAlarm
  Hooks:
    PreTraffic: !Ref PreTrafficLambda
    PostTraffic: !Ref PostTrafficLambda
  TriggerConfigurations:
    - TriggerEvents:
        - DeploymentFailure
        - DeploymentStop
      TriggerName: ProductionDeploymentTrigger
      TriggerTargetArn: !Ref DeploymentSNSTopic
```

### Phase 3: Change Documentation and Governance (Week 5-6)

#### 6. Implement AWS Systems Manager Change Manager

```python
import boto3
import json
from datetime import datetime, timedelta

ssm = boto3.client('ssm')

def create_change_template():
    """
    Create a change template for infrastructure modifications
    """
    template = {
        "description": "Standard Infrastructure Change Template",
        "templateInformation": """
        # Infrastructure Change Request
        
        ## Change Details
        - **Change Type**: {{ ChangeType }}
        - **Affected Services**: {{ AffectedServices }}
        - **Change Ticket**: {{ ChangeTicket }}
        
        ## Business Justification
        {{ BusinessJustification }}
        
        ## Technical Details
        {{ TechnicalDetails }}
        
        ## Rollback Plan
        {{ RollbackPlan }}
        
        ## Testing Completed
        {{ TestingEvidence }}
        """,
        "parameters": [
            {
                "name": "ChangeType",
                "description": "Type of change (Standard, Normal, Emergency)",
                "type": "String",
                "allowedValues": ["Standard", "Normal", "Emergency"]
            },
            {
                "name": "AffectedServices",
                "description": "List of services affected by this change",
                "type": "StringList"
            },
            {
                "name": "ChangeTicket",
                "description": "JIRA ticket number",
                "type": "String"
            },
            {
                "name": "BusinessJustification",
                "description": "Why this change is needed",
                "type": "String"
            },
            {
                "name": "TechnicalDetails",
                "description": "Technical implementation details",
                "type": "String"
            },
            {
                "name": "RollbackPlan",
                "description": "How to rollback if issues occur",
                "type": "String"
            },
            {
                "name": "TestingEvidence",
                "description": "Evidence of testing in non-production",
                "type": "String"
            }
        ],
        "reviewers": [
            {
                "type": "IDENTITY_ARN",
                "identityArn": "arn:aws:iam::ACCOUNT:role/ChangeApprover"
            }
        ],
        "approvalConfig": {
            "numberOfApprovalsRequired": 2
        }
    }
    
    response = ssm.create_ops_metadata(
        ResourceId="/ChangeManagement/Templates/InfrastructureChange",
        Metadata={"ChangeTemplate": json.dumps(template)}
    )
    
    return response

def submit_change_request(change_details):
    """
    Submit a change request through Change Manager
    """
    # Calculate change window
    start_time = datetime.utcnow() + timedelta(hours=24)
    end_time = start_time + timedelta(hours=4)
    
    response = ssm.create_ops_item(
        Description=change_details['description'],
        Source="ChangeManagement",
        Title=f"CHG: {change_details['title']}",
        Priority=change_details.get('priority', 3),
        Notifications=[
            {
                'Arn': 'arn:aws:sns:REGION:ACCOUNT:change-notifications'
            }
        ],
        OperationalData={
            'ChangeTicket': {
                'Value': change_details['ticket'],
                'Type': 'String'
            },
            'ScheduledStartTime': {
                'Value': start_time.isoformat(),
                'Type': 'DateTime'
            },
            'ScheduledEndTime': {
                'Value': end_time.isoformat(),
                'Type': 'DateTime'
            },
            'AffectedResources': {
                'Value': json.dumps(change_details['resources']),
                'Type': 'SearchableString'
            },
            'ApprovalStatus': {
                'Value': 'PENDING',
                'Type': 'String'
            }
        }
    )
    
    return response['OpsItemId']

# Example usage
change_request = {
    'title': 'Update Production RDS Instance Type',
    'description': 'Upgrade production database from db.t3.large to db.r5.xlarge for improved performance',
    'ticket': 'CHG-1234',
    'priority': 2,
    'resources': [
        'arn:aws:rds:us-east-1:ACCOUNT:db:production-db',
        'arn:aws:rds:us-east-1:ACCOUNT:db:production-db-replica'
    ]
}

ops_item_id = submit_change_request(change_request)
print(f"Change request created: {ops_item_id}")
```

#### 7. Configure Change Calendar (Maintenance Windows)

```bash
# Create change calendar for production changes
aws ssm create-document \
  --name "ProductionChangeCalendar" \
  --document-type "ChangeCalendar" \
  --content '{
    "schemaVersion": "1.0",
    "description": "Production maintenance window calendar",
    "calendarType": "CLOSED_BY_DEFAULT",
    "entries": [
      {
        "description": "Weekly maintenance window",
        "startTime": "2024-11-01T02:00:00Z",
        "endTime": "2024-11-01T06:00:00Z",
        "recurrence": "RRULE:FREQ=WEEKLY;BYDAY=SA"
      },
      {
        "description": "Emergency change window",
        "startTime": "2024-11-15T00:00:00Z",
        "endTime": "2024-11-15T23:59:59Z"
      }
    ]
  }'

# Update calendar state
aws ssm update-document-default-version \
  --name "ProductionChangeCalendar" \
  --document-version "\$LATEST"
```

**Enforce Change Calendar in Automation**:
```python
def check_change_window():
    """
    Verify current time is within approved change window
    """
    ssm = boto3.client('ssm')
    
    response = ssm.get_calendar_state(
        CalendarNames=['ProductionChangeCalendar']
    )
    
    if response['State'] != 'OPEN':
        raise Exception(
            f"Changes not permitted. Calendar state: {response['State']}. "
            f"Next change window: {response.get('NextTransitionTime', 'Not scheduled')}"
        )
    
    return True

# Use in deployment scripts
if __name__ == "__main__":
    try:
        check_change_window()
        print("Change window verified. Proceeding with deployment...")
        # Deployment logic here
    except Exception as e:
        print(f"Deployment blocked: {e}")
        sys.exit(1)
```

#### 8. Implement Automated Rollback Capabilities

**Lambda Function for Automatic Rollback**:
```python
import boto3
import json

codedeploy = boto3.client('codedeploy')
cloudwatch = boto3.client('cloudwatch')

def lambda_handler(event, context):
    """
    Monitor deployment health and trigger rollback if needed
    """
    deployment_id = event['detail']['deploymentId']
    application_name = event['detail']['applicationName']
    
    # Get deployment info
    deployment = codedeploy.get_deployment(
        deploymentId=deployment_id
    )['deploymentInfo']
    
    # Check CloudWatch metrics for health
    metrics_healthy = check_deployment_metrics(deployment)
    
    if not metrics_healthy:
        print(f"Unhealthy metrics detected. Initiating rollback for {deployment_id}")
        
        # Stop deployment
        codedeploy.stop_deployment(
            deploymentId=deployment_id,
            autoRollbackEnabled=True
        )
        
        # Notify team
        sns = boto3.client('sns')
        sns.publish(
            TopicArn='arn:aws:sns:REGION:ACCOUNT:deployment-alerts',
            Subject=f'Automatic Rollback Initiated: {application_name}',
            Message=json.dumps({
                'deploymentId': deployment_id,
                'application': application_name,
                'reason': 'Health check failure',
                'timestamp': event['time']
            }, indent=2)
        )
        
        return {'status': 'ROLLED_BACK'}
    
    return {'status': 'HEALTHY'}

def check_deployment_metrics(deployment):
    """
    Check CloudWatch metrics to determine deployment health
    """
    # Example: Check error rate
    response = cloudwatch.get_metric_statistics(
        Namespace='AWS/ApplicationELB',
        MetricName='HTTPCode_Target_5XX_Count',
        StartTime=deployment['createTime'],
        EndTime=datetime.utcnow(),
        Period=300,
        Statistics=['Sum']
    )
    
    error_count = sum([dp['Sum'] for dp in response['Datapoints']])
    
    # Rollback if more than 10 errors
    if error_count > 10:
        return False
    
    # Check other metrics (latency, CPU, etc.)
    # ...
    
    return True
```

**CloudFormation Rollback Configuration**:
```yaml
Resources:
  MyStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub 'https://s3.amazonaws.com/templates/app-stack.yaml'
      Parameters:
        Version: !Ref NewVersion
      Tags:
        - Key: ChangeTicket
          Value: !Ref ChangeTicket
      # Automatic rollback on any failure
      OnFailure: ROLLBACK
      # Disable rollback for troubleshooting (requires approval)
      # OnFailure: DO_NOTHING
      
  RollbackAlarm:
    Type: AWS::CloudWatch::Alarm
    Properties:
      AlarmName: !Sub '${AWS::StackName}-rollback-trigger'
      ComparisonOperator: GreaterThanThreshold
      EvaluationPeriods: 2
      MetricName: Errors
      Namespace: AWS/Lambda
      Period: 300
      Statistic: Sum
      Threshold: 10
      AlarmActions:
        - !GetAtt RollbackLambda.Arn
```

## Evidence Collection Tips

### Required Evidence Types

#### 1. Change Request Documentation

**What Auditors Want**:
- Formal change request with business justification
- Technical implementation plan
- Risk assessment
- Rollback plan
- Testing evidence
- Approval records

**Documentation Template**:
```markdown
# Change Request: CHG-2024-0156

## Change Details
- **Requested By**: John Smith, Senior DevOps Engineer
- **Date Submitted**: 2024-03-01
- **Scheduled Date**: 2024-03-15 02:00-06:00 UTC
- **Change Type**: Normal
- **Risk Level**: Medium

## Business Justification
Upgrade production RDS instances to improve query performance and reduce latency for customer-facing APIs. Current database CPU utilization averaging 75% during peak hours, causing occasional timeouts.

## Technical Details
### Current State
- Instance Type: db.t3.large (2 vCPU, 8 GB RAM)
- Engine: PostgreSQL 14.7
- Multi-AZ: Yes
- Read Replicas: 2

### Proposed Changes
- Upgrade to: db.r5.xlarge (4 vCPU, 32 GB RAM)
- Keep all other configurations unchanged
- Perform upgrade during maintenance window

### Implementation Steps
1. Create final backup (automated)
2. Enable enhanced monitoring
3. Modify primary instance (15-minute downtime expected)
4. Verify replication to read replicas
5. Monitor performance metrics for 1 hour
6. Declare success or rollback

## Testing Evidence
- Successfully tested upgrade in staging environment (2024-02-25)
- Performance testing showed 40% improvement in query latency
- No application code changes required
- Staging upgrade completed in 12 minutes

## Rollback Plan
If issues occur:
1. Revert to previous instance type (db.t3.large)
2. Restore from automated backup if data issues detected
3. Estimated rollback time: 15 minutes
4. Rollback tested in staging: Success (2024-02-27)

## Affected Services
- Production API (api.company.com)
- Customer dashboard (dashboard.company.com)
- Expected impact: 15 minutes read-only mode

## Approval Chain
- [x] Technical Approval: Jane Doe, Lead Architect (2024-03-02)
- [x] Security Approval: Mike Johnson, Security Engineer (2024-03-03)
- [x] Business Approval: Sarah Williams, Product Manager (2024-03-04)
- [x] CAB Approval: Change Advisory Board (2024-03-08)

## Communication Plan
- T-48 hours: Email to engineering team
- T-24 hours: Status page notification
- T-4 hours: Final go/no-go decision
- T-0: Start change, update status page
- T+completion: Success notification

## Post-Implementation Review
- Scheduled: 2024-03-16 10:00 UTC
- Attendees: Change implementer, approvers, stakeholders
```

**How to Collect**:
- Export from ticketing system (Jira, ServiceNow)
- Include email approvals
- Screenshot approval workflow
- Save associated pull requests/merge requests

#### 2. Infrastructure Change History

**What Auditors Want**:
- Complete history of infrastructure changes
- Who made each change
- When changes occurred
- What specifically changed (before/after states)

**How to Collect**:
```bash
# AWS Config change history
aws configservice get-resource-config-history \
  --resource-type AWS::EC2::Instance \
  --resource-id i-1234567890abcdef0 \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-03-31T23:59:59Z \
  > instance-change-history.json

# CloudFormation stack changes
aws cloudformation describe-stack-events \
  --stack-name production-app \
  --max-items 100 \
  > stack-change-events.json

# CloudTrail API changes
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=ResourceType,AttributeValue=AWS::RDS::DBInstance \
  --start-time 2024-01-01 \
  --end-time 2024-03-31 \
  > rds-api-changes.json

# Git commit history for IaC
git log --since="2024-01-01" --until="2024-03-31" \
  --pretty=format:"%h|%an|%ae|%ad|%s" \
  --date=iso \
  -- terraform/ cloudformation/ \
  > iac-changes.csv
```

**Config Timeline Visualization**:
```python
import boto3
import json
from datetime import datetime

config = boto3.client('config')

def generate_change_timeline(resource_type, start_date, end_date):
    """
    Generate timeline of configuration changes
    """
    resources = config.list_discovered_resources(
        resourceType=resource_type
    )['resourceIdentifiers']
    
    timeline = []
    
    for resource in resources:
        history = config.get_resource_config_history(
            resourceType=resource_type,
            resourceId=resource['resourceId'],
            laterTime=end_date,
            earlierTime=start_date,
            limit=100
        )
        
        for item in history['configurationItems']:
            change_event = {
                'resourceId': resource['resourceId'],
                'resourceType': resource_type,
                'configurationItemCaptureTime': item['configurationItemCaptureTime'],
                'configurationStateId': item['configurationStateId'],
                'changedBy': item.get('configurationItemMD5Hash', 'Unknown'),
                'changeType': item.get('configurationItemStatus', 'Unknown'),
                'configuration': json.loads(item['configuration'])
            }
            timeline.append(change_event)
    
    # Sort by time
    timeline.sort(key=lambda x: x['configurationItemCaptureTime'])
    
    return timeline

# Generate report
changes = generate_change_timeline('AWS::EC2::Instance', 
                                   datetime(2024, 1, 1),
                                   datetime(2024, 3, 31))

with open('change-timeline-q1-2024.json', 'w') as f:
    json.dump(changes, f, indent=2, default=str)
```

#### 3. Deployment Pipeline Audit Trail

**What Auditors Want**:
- Pipeline execution history
- Who approved deployments
- Automated test results
- Security scan results
- Deployment success/failure rates

**How to Collect**:
```bash
# CodePipeline execution history
aws codepipeline list-pipeline-executions \
  --pipeline-name infrastructure-pipeline \
  --max-results 100 \
  > pipeline-executions.json

# CodeBuild build history with test results
aws codebuild list-builds-for-project \
  --project-name infrastructure-validation \
  --sort-order DESCENDING \
  > build-history.json

# Get detailed build logs
for build_id in $(jq -r '.ids[]' build-history.json); do
  aws codebuild batch-get-builds --ids $build_id \
    > "build-details-${build_id}.json"
done

# Extract approval actions
aws codepipeline get-pipeline-state \
  --name infrastructure-pipeline \
  | jq '.stageStates[] | select(.stageName == "Approval")' \
  > approval-history.json
```

**Deployment Metrics Dashboard**:
```python
import boto3
from datetime import datetime, timedelta

codepipeline = boto3.client('codepipeline')

def generate_deployment_metrics(pipeline_name, days=90):
    """
    Generate deployment success metrics
    """
    end_date = datetime.utcnow()
    start_date = end_date - timedelta(days=days)
    
    executions = []
    next_token = None
    
    while True:
        if next_token:
            response = codepipeline.list_pipeline_executions(
                pipelineName=pipeline_name,
                maxResults=100,
                nextToken=next_token
            )
        else:
            response = codepipeline.list_pipeline_executions(
                pipelineName=pipeline_name,
                maxResults=100
            )
        
        executions.extend(response['pipelineExecutionSummaries'])
        
        if 'nextToken' not in response:
            break
        next_token = response['nextToken']
    
    # Filter by date range
    filtered = [e for e in executions 
                if start_date <= e['startTime'] <= end_date]
    
    # Calculate metrics
    total = len(filtered)
    succeeded = len([e for e in filtered if e['status'] == 'Succeeded'])
    failed = len([e for e in filtered if e['status'] == 'Failed'])
    
    metrics = {
        'period': f'{start_date.date()} to {end_date.date()}',
        'total_deployments': total,
        'successful': succeeded,
        'failed': failed,
        'success_rate': f'{(succeeded/total*100):.1f}%' if total > 0 else 'N/A',
        'average_duration': calculate_avg_duration(filtered)
    }
    
    return metrics

def calculate_avg_duration(executions):
    """Calculate average execution duration"""
    durations = []
    for exec in executions:
        if exec['status'] == 'Succeeded':
            duration = (exec.get('lastUpdateTime', exec['startTime']) - 
                       exec['startTime']).total_seconds()
            durations.append(duration)
    
    if durations:
        avg_seconds = sum(durations) / len(durations)
        return f'{int(avg_seconds/60)} minutes'
    return 'N/A'

# Generate report
metrics = generate_deployment_metrics('infrastructure-pipeline', days=90)
print(json.dumps(metrics, indent=2))
```

#### 4. Emergency Change Documentation

**What Auditors Want**:
- Justification for bypassing normal approval process
- Documentation of emergency situation
- Post-implementation review
- Retroactive approvals

**Emergency Change Template**:
```markdown
# Emergency Change: CHG-2024-EMERG-003

## Emergency Declaration
- **Declared By**: Jane Smith, VP Engineering
- **Date/Time**: 2024-03-10 14:35 UTC
- **Severity**: Critical (SEV-1)

## Incident Summary
- **Incident ID**: INC-2024-0089
- **Issue**: Production database running out of storage space
- **Impact**: API errors, customer transactions failing
- **Customers Affected**: ~5,000 users experiencing errors

## Emergency Justification
Database storage at 98% capacity. Automated alerts triggered at 14:30 UTC. 
Without immediate intervention, database will reach 100% capacity in 
approximately 45 minutes, causing complete service outage.

Normal change approval process (24-48 hours) would result in extended outage. 
Emergency change authorized to prevent total service disruption.

## Immediate Actions Taken
1. **14:40 UTC**: Emergency CAB convened via Slack
2. **14:42 UTC**: Verbal approval from VP Engineering and CTO
3. **14:45 UTC**: Initiated RDS storage increase via Console
   - Old capacity: 500 GB
   - New capacity: 1000 GB
4. **14:52 UTC**: Storage modification completed
5. **14:55 UTC**: Service health verified, errors ceased
6. **15:10 UTC**: Monitoring confirmed stable operation

## Change Details
- **Resource**: production-db.xxxxx.us-east-1.rds.amazonaws.com
- **Change Type**: RDS storage increase
- **Implementation Method**: AWS Console (manual, due to urgency)
- **Implemented By**: John Doe, Senior DevOps Engineer
- **Duration**: 12 minutes (within expected window)

## Root Cause
- Application logs not being rotated properly
- Log tables in database growing unbounded
- Monitoring alert for storage growth rate was too high (90%)

## Prevention Measures (Implemented within 48 hours)
1. Implemented log rotation policy in application
2. Created CloudWatch alarm at 70% storage capacity
3. Automated storage scaling via Lambda trigger
4. Added Config rule to monitor RDS storage thresholds
5. Updated runbook with automated response procedures

## Retroactive Approvals
- [x] Technical Review: Jane Smith, VP Engineering (2024-03-10 16:00)
- [x] Security Review: Mike Johnson, CISO (2024-03-11 09:00)
- [x] CAB Retroactive Approval: Change Advisory Board (2024-03-12)

## Post-Implementation Review
- **Date**: 2024-03-11 14:00 UTC
- **Attendees**: DevOps team, Engineering leadership, CAB members
- **Outcome**: Change deemed appropriate given severity
- **Lessons Learned**:
  - Monitoring thresholds need adjustment
  - Application log management needs improvement
  - Emergency procedures followed correctly
  - Team response time was excellent (10 minutes from alert to fix)

## Evidence Attached
- CloudWatch alert notification
- Slack conversation transcript (emergency CAB)
- RDS modification event log
- Service health metrics before/after
- Incident timeline
```

#### 5. Testing and Validation Evidence

**What Auditors Want**:
- Evidence that changes were tested before production
- Test results showing success criteria met
- Validation that rollback procedures work
- Performance/load testing results

**Test Evidence Collection**:
```bash
# Export test results from CodeBuild
aws codebuild batch-get-builds \
  --ids $(aws codebuild list-builds-for-project \
    --project-name infrastructure-validation \
    --query 'ids[0]' --output text) \
  | jq '.builds[0].phases[] | select(.phaseType == "BUILD")' \
  > test-results.json

# Extract security scan results
aws codebuild batch-get-reports \
  --report-arns $(aws codebuild list-reports-for-report-group \
    --report-group-arn arn:aws:codebuild:REGION:ACCOUNT:report-group/security-scans \
    --query 'reports[0]' --output text) \
  > security-scan-results.json

# Performance test results (example from custom S3 bucket)
aws s3 cp s3://test-results-bucket/load-test-2024-03-15.json . 
```

**Test Report Template**:
```markdown
# Test Report: Production Database Upgrade

## Test Environment
- **Environment**: Staging (staging.company.com)
- **Date**: 2024-03-05
- **Tested By**: DevOps Team
- **Test Duration**: 4 hours

## Test Scenarios

### Scenario 1: Instance Type Upgrade
- **Objective**: Verify upgrade from db.t3.large to db.r5.xlarge
- **Steps**:
  1. Created snapshot before upgrade
  2. Modified instance type via RDS Console
  3. Monitored upgrade progress
  4. Verified application connectivity
  5. Ran performance benchmarks
- **Results**: ✅ PASS
  - Upgrade completed in 11 minutes
  - Zero errors during upgrade
  - Application reconnected automatically
  - Query performance improved by 42%

### Scenario 2: Rollback Procedure
- **Objective**: Validate rollback to previous instance type works
- **Steps**:
  1. Downgrade from db.r5.xlarge to db.t3.large
  2. Verify application continues functioning
  3. Measure rollback duration
- **Results**: ✅ PASS
  - Rollback completed in 9 minutes
  - No data loss
  - Application remained functional

### Scenario 3: Load Testing
- **Objective**: Verify new instance handles expected load
- **Tool**: Apache JMeter
- **Load Profile**:
  - 1,000 concurrent users
  - 50,000 transactions over 30 minutes
- **Results**: ✅ PASS
  - Average response time: 145ms (previous: 247ms)
  - 99th percentile: 380ms (previous: 1,200ms)
  - Zero errors
  - CPU utilization: 45% (previous: 78%)

## Performance Comparison

| Metric | Before (t3.large) | After (r5.xlarge) | Improvement |
|--------|-------------------|-------------------|-------------|
| Avg Query Time | 247ms | 145ms | 41% faster |
| P99 Latency | 1,200ms | 380ms | 68% faster |
| CPU Utilization | 78% | 45% | 33% reduction |
| Connection Pool | 85% used | 52% used | Better capacity |

## Security Validation
- [x] Encryption at rest remains enabled
- [x] Encryption in transit verified
- [x] Security groups unchanged
- [x] Backup retention policy preserved
- [x] Multi-AZ configuration maintained

## Approval for Production
Based on successful staging tests, this change is approved for production 
deployment.

**Approved By**: Jane Doe, Lead Architect (2024-03-06)
```

## Common Audit Questions

### Question 1: "How do you ensure all infrastructure changes go through a formal change management process?"

**Strong Answer**:
"We enforce change management through multiple layers of technical and process controls:

**Technical Enforcement**:
1. **Infrastructure as Code Only**: All infrastructure is defined in Terraform/CloudFormation stored in Git. Direct console changes are detected by AWS Config and automatically flagged for remediation.

2. **Service Control Policies**: We use SCPs at the AWS Organization level to prevent creation of resources outside our IaC pipelines. For example, only specific CI/CD roles can create production resources.

3. **Branch Protection**: Our `main` branch requires:
   - Minimum 2 approvals from CODEOWNERS
   - All CI checks passing (validation, security scans, compliance)
   - Linked change ticket in commit message
   - Cannot be bypassed, even by administrators

4. **Automated Validation**: Every pull request triggers automated checks:
   - Infrastructure validation (terraform validate, cfn-lint)
   - Security scanning (tfsec, checkov, Prowler)
   - Compliance checks against our policies
   - Cost estimation for proposed changes

**Process Controls**:
5. **Change Templates**: We use AWS Systems Manager Change Manager with standardized templates requiring:
   - Change ticket number (auto-validated against Jira)
   - Business justification
   - Testing evidence
   - Rollback plan
   - Minimum 2 approvals

6. **Change Calendar**: Production changes only permitted during approved maintenance windows (Saturdays 2-6 AM UTC). Our CI/CD pipeline checks the calendar before deploying.

7. **Manual Approval Gates**: CodePipeline includes manual approval steps for production deployments. Approvers receive detailed change summaries including Terraform plans.

**Monitoring and Enforcement**:
8. **Config Rules**: Detect any resources created outside IaC pipelines
9. **CloudTrail**: All API calls logged and analyzed for unauthorized changes
10. **Automated Alerts**: Any configuration drift detected triggers immediate security team notification

For emergency changes, we have documented bypass procedures requiring VP-level authorization and retroactive CAB approval within 24 hours."

**Evidence to Provide**:
- Git branch protection settings
- SCP policies preventing unauthorized changes
- Sample pull request showing approval workflow
- Config rule detecting manual changes
- Change calendar configuration
- Emergency change procedure document
- Last quarter's change statistics (100% through formal process)

### Question 2: "How do you test changes before implementing them in production?"

**Strong Answer**:
"We maintain a rigorous multi-stage testing approach:

**Environment Progression**:
1. **Development**: Engineers test locally and in dev AWS accounts
2. **Integration**: Automated integration tests in dedicated account
3. **Staging**: Full production replica for comprehensive testing
4. **Production**: Phased rollout with automated rollback

**Automated Testing in CI/CD**:
- **Unit Tests**: Run on every commit
- **Infrastructure Validation**: Terraform validate, CloudFormation validation
- **Security Scanning**: tfsec, checkov scan IaC for security issues
- **Compliance Checks**: Custom scripts verify changes don't violate policies
- **Integration Tests**: Automated tests verify services interact correctly

**Staging Environment Testing**:
- Environment is 1:1 replica of production (smaller instance sizes)
- All changes deployed to staging first
- Automated smoke tests run post-deployment
- Performance/load testing for significant changes
- Minimum 24-hour soak period before production

**Production Deployment Strategy**:
- **Blue/Green**: For applications with load balancers
- **Canary**: For Lambda functions (10% → 50% → 100%)
- **Progressive**: For ECS/EKS rolling updates
- Automated health checks at each stage
- Automatic rollback on health check failures

**Rollback Validation**:
- We test rollback procedures in staging for every change
- Documented rollback steps in change request
- Automated rollback via CodeDeploy for application changes
- CloudFormation stack rollback for infrastructure changes

**Evidence Requirements**:
- Every production change requires screenshot/logs from successful staging deployment
- Test results automatically attached to change ticket
- No change approved without testing evidence"

**Evidence to Provide**:
- CI/CD pipeline configuration showing test stages
- Sample test results from staging deployment
- Blue/green deployment configuration
- Automated health check Lambda functions
- Staging environment architecture diagram
- Rollback test documentation

### Question 3: "How do you track who made what changes and when?"

**Strong Answer**:
"We maintain comprehensive audit trails through multiple systems:

**Source Control Audit Trail**:
- Git commit history with signed commits (GPG keys required)
- Every commit includes:
  - Author identity (verified)
  - Timestamp
  - Change description
  - Linked Jira ticket
  - Pull request with approver identities
- Cannot be modified after merge (branch protection + Git immutability)

**AWS Native Logging**:
- **CloudTrail**: All API calls logged with:
  - User identity (from IAM Identity Center → Okta)
  - Source IP address
  - Timestamp
  - Resources affected
  - Request parameters
- **AWS Config**: Records configuration state changes:
  - Before/after state for every resource
  - Timeline of changes
  - Related CloudTrail events
- **CloudFormation**: Stack change history:
  - Stack updates with change sets
  - Who initiated the change
  - What resources were modified

**Change Management System**:
- AWS Systems Manager Change Manager tracks:
  - Change request submission
  - Approval chain with timestamps
  - Execution status
  - Post-change validation
- Integration with Jira for additional workflow tracking

**Pipeline Execution Logs**:
- CodePipeline records:
  - Who triggered deployment
  - Git commit SHA deployed
  - Approval actions with approver identity
  - Execution duration and status
- CodeBuild logs include full build/test/deploy output

**Comprehensive View**:
For any change, we can provide:
1. Git commit showing the code change and author
2. Pull request with reviewers and approval timestamps
3. Jira ticket with business justification and approvals
4. Pipeline execution showing deployment
5. CloudTrail events showing AWS API calls
6. Config timeline showing before/after resource states

All logs retained for 7 years with integrity protection (CloudTrail digest files, S3 object lock)."

**Evidence to Provide**:
- Sample Git commit with full history
- Pull request with approval chain
- CloudTrail event for infrastructure change
- Config timeline for specific resource
- Change Manager execution record
- Jira ticket with full audit trail

### Question 4: "What is your process for emergency changes that bypass normal approval?"

**Strong Answer**:
"We recognize that true emergencies require expedited procedures while maintaining controls:

**Emergency Criteria** (must meet at least one):
- SEV-1 production incident with customer impact
- Security vulnerability requiring immediate patching
- Imminent service outage if not addressed
- Regulatory requirement with hard deadline

**Emergency Change Process**:

1. **Declaration** (0-5 minutes):
   - Incident Commander declares emergency
   - Emergency CAB convened (Slack channel)
   - VP-level approval required (verbal OK with written follow-up)

2. **Implementation** (5-30 minutes):
   - Change implemented by authorized engineer
   - All actions documented in real-time (Slack transcript)
   - Changes still go through CI/CD when possible
   - If console access required, screen recording captured

3. **Immediate Documentation** (within 1 hour):
   - Create emergency change ticket with:
     - Incident details and severity justification
     - Actions taken with timestamps
     - Who authorized the emergency
     - Impact assessment
   - CloudTrail logs automatically captured

4. **Retroactive Approval** (within 24 hours):
   - Formal CAB review of emergency change
   - Security team reviews for compliance
   - Documentation added to change ticket
   - If not approved, immediate remediation required

5. **Post-Incident Review** (within 48 hours):
   - Root cause analysis
   - Review of emergency procedures
   - Preventive measures identified
   - Update runbooks and automation

**Controls for Emergency Changes**:
- Still logged in CloudTrail (cannot be bypassed)
- Still subject to SCPs and guardrails
- Config still records state changes
- Limited to pre-authorized emergency responders
- Cannot modify financial or legal controls
- All emergency changes reviewed weekly by security team

**Statistics**:
- Last 12 months: 3 emergency changes out of 487 total (0.6%)
- All 3 received retroactive CAB approval
- Average time from emergency to formal documentation: 45 minutes

We continuously work to reduce emergency changes by:
- Improving monitoring and alerting (detect issues earlier)
- Better capacity planning
- Automated remediation for common issues
- More comprehensive testing"

**Evidence to Provide**:
- Emergency change procedure document
- Sample emergency change ticket with full timeline
- Slack transcript showing emergency CAB approval
- CloudTrail logs of emergency actions
- Post-incident review document
- CAB retroactive approval record
- Quarterly emergency change statistics

### Question 5: "How do you prevent unauthorized or undocumented changes to production?"

**Strong Answer**:
"We implement defense-in-depth to prevent unauthorized changes:

**Preventive Controls**:

1. **Service Control Policies**: Organization-level policies that:
   - Deny infrastructure creation except from CI/CD roles
   - Deny production access during change freeze periods
   - Require specific tags on all resources (including ChangeTicket)
   - Prevent disabling of CloudTrail, Config, GuardDuty

2. **IAM Identity Center + Okta**:
   - No standing production access
   - Time-limited sessions (4-hour maximum)
   - MFA required for all access
   - Production access requires manager approval in Okta

3. **Least Privilege**:
   - Engineers have read-only production access by default
   - Write access requires elevation through ServiceNow request
   - Privileged access tied to specific change tickets
   - Automatic revocation after change window closes

4. **Change Calendar**:
   - Automated checks block deployments outside maintenance windows
   - Emergency override requires VP approval
   - Calendar state checked by CI/CD pipeline and IaC tools

**Detective Controls**:

5. **AWS Config Rules**:
   - `require-tags`: All resources must have ChangeTicket tag
   - `approved-amis-only`: Only pre-approved AMIs can be used
   - `no-console-instances`: EC2 instances must be launched via CloudFormation
   - Custom rules detect manual vs. automated changes

6. **Drift Detection**:
   - CloudFormation drift detection runs daily
   - Terraform state checked for manual modifications
   - Any drift triggers immediate security alert

7. **Config Timeline Analysis**:
   - Automated script compares Config events to approved change tickets
   - Flags any resource changes without corresponding ticket
   - Daily report of all configuration changes

8. **CloudTrail Monitoring**:
   - Real-time analysis for console login events
   - Alert on any production resource creation/modification outside CI/CD
   - Machine learning (CloudTrail Insights) detects unusual API patterns

**Response to Unauthorized Changes**:

9. **Automated Remediation**:
   - Config remediation automatically reverts non-compliant changes
   - Security groups opened to 0.0.0.0/0 automatically closed
   - Untagged resources automatically quarantined

10. **Incident Response**:
    - Unauthorized change triggers automatic ticket creation
    - Resource owner and security team notified
    - Access of user who made change immediately suspended pending investigation
    - Post-incident review required before access restored

**Reporting**:
- Weekly report of all production changes with ticket validation
- Monthly security review of access patterns
- Quarterly audit of change management effectiveness

Last audit period: Zero unauthorized changes detected."

**Evidence to Provide**:
- SCP policies preventing unauthorized actions
- Config rules enforcing change management
- Drift detection configuration and recent results
- Automated remediation Lambda functions
- Sample unauthorized change detection alert
- Weekly change validation report
- Quarterly audit results showing zero violations

### Question 6: "How do you ensure changes can be rolled back if problems occur?"

**Strong Answer**:
"Rollback capability is a mandatory requirement for all changes:

**Design for Rollback**:

1. **Infrastructure as Code**:
   - Every deployment is from a specific Git commit
   - Rolling back = deploying previous Git commit
   - State files versioned in S3 with versioning enabled
   - Can recreate any previous infrastructure state

2. **Immutable Infrastructure**:
   - AMIs never modified in place - new AMI created
   - Container images tagged with version and Git SHA
   - Deployments create new resources rather than modifying existing
   - Old resources kept running until new ones validated

3. **Database Migrations**:
   - All migrations reversible (up and down scripts)
   - Tested in staging in both directions
   - Data migrations separate from schema migrations
   - Point-in-time backup before any database change

**Automated Rollback**:

4. **CodeDeploy Automatic Rollback**:
   - Monitors CloudWatch alarms during deployment
   - Automatically rolls back if:
     - Error rate exceeds threshold
     - Health checks fail
     - Custom metrics show degradation
   - Rollback completes in < 5 minutes

5. **Blue/Green Deployments**:
   - New version deployed alongside old version
   - Traffic shifted gradually (10% → 100%)
   - Old version kept running for quick rollback
   - Automatic revert if health checks fail

6. **CloudFormation Rollback**:
   - Automatic rollback on stack update failure
   - Previous stack state automatically restored
   - No manual intervention required

**Rollback Testing**:

7. **Mandatory Rollback Validation**:
   - Every change request must include tested rollback plan
   - Rollback tested in staging before production deployment
   - Rollback time documented and verified
   - Cannot proceed to production without rollback evidence

8. **Quarterly Rollback Drills**:
   - Practice rollback procedures for critical systems
   - Verify rollback automation works correctly
   - Team training on manual rollback procedures
   - Update runbooks based on drill findings

**Rollback Execution**:

9. **Decision Criteria**:
   - Clear thresholds for triggering rollback
   - Error rate > 1%
   - Latency > 2x baseline
   - Any SEV-1/SEV-2 incident related to change
   - Customer complaints > threshold

10. **Execution Process**:
    - Incident Commander authorizes rollback
    - Automated: Click 'rollback' in CodeDeploy/CodePipeline
    - Manual: Execute tested rollback runbook
    - Average rollback time: 7 minutes (tracked metric)
    - Validation tests run post-rollback

**Evidence and Metrics**:
- Last 12 months: 3 production rollbacks out of 487 changes (0.6%)
- All rollbacks completed in < 10 minutes
- Zero data loss from rollbacks
- All rollback procedures tested in staging before use

Every change ticket includes:
- Rollback procedure documentation
- Evidence of successful rollback test in staging
- Estimated rollback time
- Rollback approval from technical lead"

**Evidence to Provide**:
- CodeDeploy automatic rollback configuration
- Sample change ticket with rollback plan
- Rollback test results from staging
- Blue/green deployment configuration
- Actual rollback incident documentation
- Quarterly rollback drill reports
- Rollback success metrics dashboard

## Continuous Compliance

### Daily Tasks
- [ ] Review failed pipeline executions
- [ ] Check Config for unauthorized changes
- [ ] Monitor change calendar compliance
- [ ] Review CloudTrail for unexpected API calls

### Weekly Tasks
- [ ] Validate all production changes had approved tickets
- [ ] Review drift detection reports
- [ ] Check CI/CD pipeline health
- [ ] Audit emergency changes (if any)
- [ ] Review pending change requests

### Monthly Tasks
- [ ] Generate change management metrics report
- [ ] Review and update change templates
- [ ] Audit IaC compliance with standards
- [ ] Test automated rollback procedures
- [ ] Review change failure rates and root causes
- [ ] Update change management documentation

### Quarterly Tasks
- [ ] Conduct change management audit
- [ ] Review and update change approval processes
- [ ] Rollback drill for critical systems
- [ ] CAB effectiveness review
- [ ] Update IaC module library
- [ ] Review emergency change procedures
- [ ] Training refresh for team

### Annual Tasks
- [ ] Comprehensive change management assessment
- [ ] Review and update change management policy
- [ ] Evaluate CI/CD tool effectiveness
- [ ] Disaster recovery test including IaC recovery
- [ ] Third-party audit of change controls
- [ ] Benchmark against industry standards

## Key Performance Indicators

Track these metrics to demonstrate effective change management:

| Metric | Target | Purpose |
|--------|--------|---------|
| Change Success Rate | > 95% | Measure quality of changes |
| Emergency Changes | < 2% of total | Measure planning effectiveness |
| Rollback Rate | < 2% of total | Measure testing effectiveness |
| Changes with Tickets | 100% | Measure compliance |
| Average Approval Time | < 48 hours | Measure process efficiency |
| Mean Time to Deploy | < 30 minutes | Measure automation effectiveness |
| Config Compliance | 100% | Measure control effectiveness |
| Unauthorized Changes | 0 | Measure security posture |

## Additional Resources

### AWS Documentation
- [AWS Well-Architected Framework - Operational Excellence](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/welcome.html)
- [AWS Systems Manager Change Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/change-manager.html)
- [AWS Config Change Tracking](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html)
- [AWS CodePipeline User Guide](https://docs.aws.amazon.com/codepipeline/latest/userguide/)

### Industry Standards
- [ITIL Change Management](https://www.axelos.com/certifications/itil-service-management/itil-4-foundation)
- [NIST SP 800-53 CM Controls (Configuration Management)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)

### Tools and Templates
- [Terraform Best Practices](https://www.terraform-best-practices.com/)
- [AWS CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)
- [GitOps Principles](https://opengitops.dev/)
- [Change Request Template Repository](https://github.com/search?q=change+request+template)

---
