# Availability Trust Service Criteria

The Availability criteria addresses the accessibility of the system, information, and services as committed or agreed upon.

## Overview

Availability ensures that:
- Systems are available for operation and use as committed or agreed
- The entity meets its availability commitments and system requirements
- Monitoring and incident response procedures are in place
- Recovery procedures ensure continued availability

---

## A1: Availability

### A1.1 - Availability Commitments

**Control Description**: The entity maintains, monitors, and evaluates current processing capacity and use of system components to manage capacity demand and to enable the implementation of additional capacity to help meet its availability objectives.

**AWS Services**:
- Amazon CloudWatch (Capacity monitoring)
- AWS Auto Scaling (Dynamic capacity)
- AWS Compute Optimizer (Right-sizing recommendations)
- AWS Trusted Advisor (Resource utilization)
- Amazon CloudWatch Application Insights (Application monitoring)

**Implementation**:

1. **Capacity Planning**:
   ```
   - Review CloudWatch metrics for CPU, memory, network utilization
   - Set CloudWatch alarms for capacity thresholds (>70% usage)
   - Use Compute Optimizer for right-sizing recommendations
   - Document capacity planning process
   ```

2. **Auto Scaling Configuration**:
   ```
   - Configure Auto Scaling groups for EC2
   - Set up Application Auto Scaling for ECS, DynamoDB, etc.
   - Define scaling policies (target tracking, step scaling)
   - Test scaling behaviors under load
   ```

3. **Monitoring and Alerting**:
   ```
   - Create CloudWatch dashboards for capacity metrics
   - Configure SNS alerts for capacity warnings
   - Set up anomaly detection for usage patterns
   - Regular capacity reviews (monthly)
   ```

**Evidence Required**:
- Capacity planning documentation
- CloudWatch capacity dashboards
- Auto Scaling configurations
- Capacity review meeting notes
- Alert configurations and history
- Load testing results
- Compute Optimizer reports

**Cost Estimation**:
- Small: $50-150/month (basic monitoring, small Auto Scaling)
- Medium: $150-500/month (advanced monitoring, multi-service scaling)
- Large: $500-2,000/month (enterprise monitoring, complex scaling)

---

### A1.2 - Environmental Protections and Monitoring

**Control Description**: The entity implements controls to monitor environmental protections and related events and issues.

**AWS Services**:
- AWS Health Dashboard (Service health monitoring)
- AWS Personal Health Dashboard (Account-specific alerts)
- Amazon CloudWatch (System monitoring)
- AWS Systems Manager (Fleet management)
- AWS Status Page (Public status)

**Implementation**:

1. **Infrastructure Monitoring**:
   ```
   - Enable CloudWatch detailed monitoring
   - Configure custom metrics for application health
   - Set up synthetic monitoring with CloudWatch Synthetics
   - Monitor system-level metrics (disk, memory, network)
   ```

2. **Service Health Monitoring**:
   ```
   - Subscribe to AWS Health notifications
   - Configure EventBridge rules for health events
   - Set up automated responses to health events
   - Monitor Personal Health Dashboard
   ```

3. **Physical Environment** (AWS Data Centers):
   ```
   - Review AWS SOC 2 reports for data center controls
   - Document reliance on AWS physical controls
   - Understand AWS availability zones
   - Leverage multi-AZ deployments
   ```

**Evidence Required**:
- CloudWatch monitoring configurations
- AWS Health notification setup
- Environmental monitoring documentation
- AWS SOC 2 reports (from Artifact)
- Incident response to environmental events
- Multi-AZ deployment evidence

**Cost Estimation**:
- Basic monitoring: $50-100/month
- Advanced monitoring with Synthetics: $100-300/month
- Enterprise monitoring: $300-1,000/month

---

### A1.3 - System Backup and Restoration

**Control Description**: The entity implements controls to protect against loss of data and to enable recovery in case of failure.

**AWS Services**:
- **AWS Backup** (Centralized backup management)
- **Amazon S3** (Backup storage with versioning)
- **AWS RDS** Automated Backups
- **Amazon EBS Snapshots**
- **AWS Data Lifecycle Manager** (DLM)
- **AWS Backup Vault Lock** (Compliance lock)
- **Amazon S3 Glacier** (Long-term archival)

**Implementation**:

1. **Centralized Backup Strategy**:
   ```
   - Create AWS Backup plans for all critical resources
   - Define RPO (Recovery Point Objective): 1-24 hours
   - Define RTO (Recovery Time Objective): 1-4 hours
   - Tag resources for automatic backup inclusion
   ```

2. **Database Backups**:
   ```
   - Enable automated RDS backups (7-35 day retention)
   - Configure RDS snapshots for point-in-time recovery
   - Enable DynamoDB point-in-time recovery
   - Test database restoration procedures
   ```

3. **File System Backups**:
   ```
   - Configure EFS backup via AWS Backup
   - Create EBS snapshot policies via DLM
   - Enable S3 versioning for data protection
   - Implement S3 Cross-Region Replication
   ```

4. **Backup Security**:
   ```
   - Encrypt all backups (AWS KMS)
   - Use Backup Vault Lock for immutability
   - Implement least-privilege access to backups
   - Cross-account backup copies
   ```

5. **Restoration Testing**:
   ```
   - Quarterly restore testing schedule
   - Document restore procedures
   - Measure actual RTOs
   - Test cross-region restoration
   ```

**Evidence Required**:
- AWS Backup plan configurations
- Backup success/failure reports
- Restoration test documentation
- RPO/RTO definitions and measurements
- Backup encryption configurations
- Cross-region replication settings
- Restoration procedure documentation
- Test restore results (screenshots, logs)

**Cost Estimation**:
- Backup storage: $0.05-0.10/GB/month (S3 Standard)
- Backup storage (cold): $0.004/GB/month (Glacier)
- AWS Backup: Storage + $0.50 per backup job
- Cross-region replication: Data transfer costs

**Example Costs**:
- 1TB backup (hot): $50-100/month
- 1TB backup (cold): $4-10/month
- 10TB backup (hot): $500-1,000/month
- Restore data transfer: $0.09/GB out

---

## A2: Performance and Availability Monitoring

### A2.1 - Performance Monitoring

**Control Description**: The entity monitors, measures, and evaluates the performance of the system against defined performance standards.

**AWS Services**:
- **Amazon CloudWatch** (Metrics, dashboards, alarms)
- **AWS X-Ray** (Distributed tracing)
- **Amazon CloudWatch Synthetics** (Synthetic monitoring)
- **Amazon CloudWatch RUM** (Real User Monitoring)
- **Amazon CloudWatch Application Insights** (Application observability)
- **AWS Compute Optimizer** (Performance recommendations)

**Implementation**:

1. **Infrastructure Performance**:
   ```
   - Monitor EC2 CPU, memory, disk, network
   - Track RDS query performance
   - Monitor Lambda duration and concurrency
   - Set performance baselines
   ```

2. **Application Performance**:
   ```
   - Implement X-Ray for request tracing
   - Set up custom CloudWatch metrics
   - Monitor API response times
   - Track error rates and exceptions
   ```

3. **User Experience Monitoring**:
   ```
   - Deploy CloudWatch Synthetics canaries
   - Implement CloudWatch RUM for real users
   - Monitor page load times
   - Track availability from multiple locations
   ```

4. **Performance Dashboards**:
   ```
   - Create CloudWatch dashboards for each service
   - Executive summary dashboard
   - Alert on performance degradation
   - Historical trend analysis
   ```

**Evidence Required**:
- Performance monitoring configurations
- CloudWatch dashboards (screenshots)
- Performance baseline documentation
- Alert configurations
- X-Ray traces for critical paths
- Synthetics canary results
- Performance trend reports
- Incident records for performance issues

**Cost Estimation**:
- Basic CloudWatch: $10-50/month
- X-Ray: $5 per million traces
- Synthetics: $0.0012 per canary run
- RUM: $1 per 100,000 events
- Total typical: $50-300/month

---

## Implementation Checklist

### Phase 1: Foundation (Week 1-2)
- [ ] Enable CloudWatch detailed monitoring
- [ ] Create basic availability dashboards
- [ ] Configure Auto Scaling groups
- [ ] Set up AWS Health notifications
- [ ] Document RPO/RTO requirements

### Phase 2: Backup & Recovery (Week 3-4)
- [ ] Configure AWS Backup plans
- [ ] Enable RDS automated backups
- [ ] Set up EBS snapshot policies
- [ ] Implement S3 versioning
- [ ] Configure cross-region replication
- [ ] Perform initial restore test

### Phase 3: Advanced Monitoring (Week 5-6)
- [ ] Deploy CloudWatch Synthetics canaries
- [ ] Implement X-Ray tracing
- [ ] Set up Application Insights
- [ ] Configure custom metrics
- [ ] Create alerting runbooks

### Phase 4: Testing & Validation (Week 7-8)
- [ ] Conduct load testing
- [ ] Quarterly restore testing
- [ ] Chaos engineering exercises
- [ ] Review and optimize Auto Scaling
- [ ] Document all procedures

---

## Reference Architecture: High Availability Application

```
┌─────────────────────────────────────────────────────────────┐
│                      Route 53 (DNS)                         │
│                    Health Checks                            │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                   CloudFront (CDN)                          │
│               DDoS Protection (Shield)                      │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│          Application Load Balancer (Multi-AZ)               │
│                  Health Checks                              │
└──────┬─────────────────────────────────┬────────────────────┘
       │                                 │
┌──────▼──────────┐            ┌─────────▼──────────┐
│  Availability   │            │   Availability     │
│    Zone A       │            │     Zone B         │
│                 │            │                    │
│  Auto Scaling   │            │   Auto Scaling     │
│  Group (EC2)    │            │   Group (EC2)      │
│                 │            │                    │
│  ┌───────────┐  │            │   ┌───────────┐   │
│  │ EC2       │  │            │   │ EC2       │   │
│  │ Instance  │  │            │   │ Instance  │   │
│  └─────┬─────┘  │            │   └─────┬─────┘   │
│        │        │            │         │         │
└────────┼────────┘            └─────────┼─────────┘
         │                               │
    ┌────▼───────────────────────────────▼────┐
    │         RDS Multi-AZ                    │
    │    Primary (AZ-A) → Standby (AZ-B)     │
    │      Automated Backups                  │
    │      Point-in-Time Recovery            │
    └─────────────────────────────────────────┘
    
    ┌─────────────────────────────────────────┐
    │          AWS Backup                     │
    │    • Daily backups (35-day retention)   │
    │    • Cross-region replication           │
    │    • Vault Lock (immutability)         │
    └─────────────────────────────────────────┘
    
    ┌─────────────────────────────────────────┐
    │          CloudWatch Monitoring          │
    │    • Metrics & Alarms                   │
    │    • Synthetics Canaries                │
    │    • X-Ray Tracing                      │
    └─────────────────────────────────────────┘
```

---

## AWS Service Costs Summary

| Service | Purpose | Monthly Cost Range |
|---------|---------|-------------------|
| CloudWatch | Monitoring & Alarms | $50-300 |
| Auto Scaling | Dynamic capacity | $0 (pay for resources) |
| Multi-AZ RDS | Database availability | +50% of instance cost |
| AWS Backup | Centralized backups | $0.05/GB + jobs |
| S3 (backups) | Backup storage | $0.023-0.10/GB |
| CloudWatch Synthetics | Availability testing | $0.0012/run |
| X-Ray | Tracing | $5/million traces |
| Application Load Balancer | Multi-AZ distribution | $20-50 |
| Route 53 | DNS with health checks | $1-10 |

**Total Estimated Monthly Cost**: $150-1,000+ depending on scale

---

## Evidence Collection Tips

### For Auditors:
1. **Capacity Management**:
   - Quarterly capacity planning reviews
   - CloudWatch dashboard screenshots
   - Auto Scaling configuration exports
   - Load test results

2. **Backup & Recovery**:
   - AWS Backup console screenshots
   - Successful backup job logs (30-90 days)
   - Restore test documentation (quarterly)
   - RPO/RTO attestation

3. **Monitoring**:
   - CloudWatch alarm configurations
   - Alert notification logs
   - Incident response records
   - Performance baseline documentation

### Retention Requirements:
- Backup logs: 12+ months
- Restore test results: 12+ months
- Capacity reviews: 12+ months
- Performance metrics: 6-12 months
- Incident records: 24+ months

---

## Common Audit Findings

### Finding 1: Insufficient Backup Testing
**Issue**: Backups configured but never tested
**Remediation**: Implement quarterly restore testing schedule

### Finding 2: No RPO/RTO Documentation
**Issue**: Recovery objectives not defined
**Remediation**: Document and measure RPO/RTO for all critical systems

### Finding 3: Single-AZ Deployments
**Issue**: Critical resources in single availability zone
**Remediation**: Implement multi-AZ architecture for all critical components

### Finding 4: No Capacity Planning
**Issue**: No process for capacity management
**Remediation**: Establish quarterly capacity planning reviews

### Finding 5: Insufficient Monitoring
**Issue**: Limited visibility into system health
**Remediation**: Implement comprehensive CloudWatch monitoring and alerting

---

## Best Practices

1. **Design for Multi-AZ from Day One**
   - Use Multi-AZ RDS
   - Deploy Auto Scaling across multiple AZs
   - Use Application Load Balancers
   - Test failover scenarios

2. **Automate Everything**
   - Infrastructure as Code (CloudFormation/Terraform)
   - Automated backup policies
   - Auto Scaling based on metrics
   - Automated alerting and remediation

3. **Test Regularly**
   - Monthly failover testing
   - Quarterly restore testing
   - Annual disaster recovery drills
   - Chaos engineering practices

4. **Monitor Proactively**
   - Set up predictive alarms
   - Use anomaly detection
   - Implement synthetic monitoring
   - Track user experience metrics

5. **Document Everything**
   - Runbooks for common scenarios
   - Escalation procedures
   - Recovery procedures
   - Architecture diagrams

---

## Next Steps

1. Review your current availability posture
2. Implement multi-AZ architecture where needed
3. Configure comprehensive backup strategy
4. Set up monitoring and alerting
5. Schedule regular testing and reviews
6. Begin evidence collection

[← Back to Common Criteria](common-criteria.md) | [View Confidentiality Controls →](confidentiality.md) | [Back to Main Guide](../README.md)
