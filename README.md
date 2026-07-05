AWS Production-Ready Highly Available Web Application
Project Overview
This project demonstrates the design and implementation of a production-ready, highly available, and secure web application architecture on AWS.

The infrastructure follows AWS Well-Architected Framework best practices, including high availability, scalability, security, monitoring, and fault tolerance.

The application is deployed across multiple Availability Zones using Amazon EC2 instances managed by an Auto Scaling Group behind an Application Load Balancer. Security is enhanced using private subnets, Security Groups, and IAM roles. Monitoring and alerting are implemented using Amazon CloudWatch and SNS. AWS WAF was fully designed and configured but could not be provisioned due to IAM restrictions in the AWS Academy Learner Lab used for this build (see the WAF section below for details).

Architecture Diagram
Architecture Diagram

Solution Architecture
The infrastructure is deployed inside a custom VPC and spans two Availability Zones to achieve fault tolerance and high availability.

Users access the application through an Application Load Balancer.

The Load Balancer distributes traffic across multiple EC2 instances deployed in private subnets.

Auto Scaling automatically launches or terminates EC2 instances based on demand.

Amazon RDS provides managed database services.

Amazon S3 provides centralized object storage for project assets, backups, screenshots, and documentation.

AWS WAF was configured against the Application Load Balancer with managed rule groups to protect against:

SQL Injection
Cross Site Scripting (XSS)
Known Malicious Inputs
Bad Reputation IP Addresses
Note: the WAF web ACL could not actually be created due to Learner Lab IAM restrictions. See the AWS WAF Configuration section below.

CloudWatch monitors infrastructure metrics and SNS sends notifications when alarms are triggered.

AWS Services Used
Networking
Amazon VPC
Public Subnets
Private Application Subnets
Private Database Subnets
Internet Gateway
NAT Gateway
Route Tables
Compute
Amazon EC2
Launch Template
Auto Scaling Group
Load Balancing
Application Load Balancer
Target Groups
Health Checks
Database
Amazon RDS MySQL
Storage
Amazon S3
Security
Security Groups
IAM Roles (Learner Lab provided role)
AWS WAF (designed, not provisioned — see note below)
Monitoring
Amazon CloudWatch
Amazon SNS
VPC Design
VPC
CIDR: 10.0.0.0/16
Public Subnets
Public Subnet A
10.0.1.0/24
AZ-A
Public Subnet B
10.0.2.0/24
AZ-B
Used for:

Application Load Balancer
NAT Gateways
Private Application Subnets
Private-App-A
10.0.11.0/24
AZ-A
Private-App-B
10.0.12.0/24
AZ-B
Used for:

EC2 Instances
Private Database Subnets
Private-DB-A
10.0.21.0/24
AZ-A
Private-DB-B
10.0.22.0/24
AZ-B
Used for:

Amazon RDS
Security Groups
ALB Security Group
Inbound Rules

HTTP 80     0.0.0.0/0
HTTPS 443   0.0.0.0/0
Outbound Rules

All Traffic
EC2 Security Group
Inbound Rules

HTTP 80
Source: ALB Security Group
Outbound Rules

All Traffic
RDS Security Group
Inbound Rules

MySQL 3306
Source: EC2 Security Group
Outbound Rules

All Traffic
Auto Scaling Configuration
Minimum Capacity

2
Desired Capacity

2
Maximum Capacity

4
Scaling Metric

CPUUtilization
Scale-Out Trigger

CPU > 70%
Application Load Balancer
Features:

Multi-AZ Deployment
Health Checks
Traffic Distribution
Fault Tolerance
Health Check Path

/
Protocol

HTTP
Port

80
Amazon RDS
Database Engine

MySQL
Instance Class

db.t3.micro
Deployment

Multi-AZ
Database Subnets

Private-DB-A
Private-DB-B
Accessibility

Private
Public Access

Disabled
Features

Multi-AZ High Availability
Automatic Failover
Managed Backups
Private Network Access
RDS Validation
Verified Multi-AZ deployment status
Verified database availability
Verified private access through Security Groups
Result:

Status: Available
Deployment: Multi-AZ
Public Access: Disabled
Amazon S3
Used For:

Project Assets
Architecture Diagram
Screenshots
Documentation
Backups
Future Log Archival
Features Enabled:

Versioning
Server-Side Encryption (SSE-S3)
AWS WAF Configuration
The following managed rule groups were selected and configured against the Application Load Balancer during setup:

Core Rule Set
Protects against:

XSS
Common Web Exploits
OWASP Top 10
Known Bad Inputs
Protects against:

Malicious Requests
Suspicious Payloads
SQL Database Protection
Protects against:

SQL Injection Attacks
Amazon IP Reputation List
Blocks:

Known Malicious IP Addresses
Important note: This project was built inside an AWS Academy Learner Lab, and the Lab's IAM policy explicitly denies the wafv2:CreateWebACL action. As a result, while the web ACL and all four rule groups above were fully configured through the console, the WAF resource itself was never actually created and is not active in the deployed environment. This is documented here for transparency, and the configuration is ready to be deployed as-is in a standard AWS account without this restriction.

Monitoring and Alerting
Amazon CloudWatch was configured to monitor:

EC2 CPU Utilization (via Auto Scaling Group metrics)
Auto Scaling Activities
Alarm:

High-CPU-Utilization (triggers when average CPU > 70%)
Notification Service:

Amazon SNS (production-alerts topic)
Email notifications are automatically sent when the alarm is triggered. This was verified by manually setting the alarm state and confirming the email arrived.

High Availability Features
Multi-AZ Application Deployment
Multi-AZ Amazon RDS
Application Load Balancer
Auto Scaling Group
Health Checks
Self-Healing Infrastructure
Redundant NAT Gateways (one per Availability Zone)
Automatic Database Failover
Security Features
Private Application Subnets
Private Database Subnets
Security Groups (chained ALB → EC2 → RDS)
RDS Private Access (public access disabled)
S3 Encryption
AWS WAF (designed, blocked from creation by Lab IAM policy — see note above)
Testing Performed
Load Balancer Validation
Verified successful access through the ALB DNS endpoint, and confirmed responses came from different EC2 instances and Availability Zones on repeated requests.

Auto Scaling Validation
Verified the Auto Scaling Group maintains its desired capacity of 2 instances across both Availability Zones.

Health Check Validation
Verified healthy targets in the Target Group.

RDS Connectivity Validation
Verified the database deployment status, Multi-AZ configuration, and that public access is disabled.

Monitoring Validation
Manually forced the CloudWatch alarm into the "In Alarm" state and confirmed the SNS email notification was received.

Learning Outcomes
Through this project I learned how to:

Design a highly available AWS architecture across multiple Availability Zones
Configure Auto Scaling Groups with target-tracking scaling policies
Deploy and manage an Application Load Balancer and Target Groups
Implement VPC networking and subnetting across public, application, and database tiers
Configure Amazon RDS securely in Multi-AZ mode
Monitor AWS resources using CloudWatch and set up SNS notifications
Design an AWS WAF configuration, and recognize how IAM restrictions in a shared lab environment can prevent a fully designed resource from being deployed
Document a project honestly, including the parts that could not be completed and why
Future Enhancements
The following can be added in future iterations of this project:

Deploy the AWS WAF configuration documented above in a standard AWS account without Lab restrictions
Amazon Route 53 for custom domain management and DNS routing
Amazon CloudFront for global content delivery and caching
AWS Certificate Manager (ACM) for HTTPS/TLS certificates
CloudWatch Logs export to Amazon S3 for long-term log retention
Infrastructure as Code (IaC) using AWS CloudFormation or Terraform
Project Author
Kareem Rashad
