# AWS Certified Developer Associate (DVA-C02) – Course Overview

This document summarizes the content covered in the AWS Certified Developer Associate course (DVA-C02) by Stéphane Maarek. The course prepares you for the certification exam and covers over 30 AWS services.

---

## Course Topics

### 1. Getting Started with AWS

### 2. **AWS Identity & Access Management (IAM)**
- Global service for identity and access management
- Users, groups, policies (JSON), roles
- Security tools: Credentials Report, Access Advisor
- Best practices

### 3. **Amazon EC2 – Basics**
- Infrastructure as a Service (IaaS)
- Virtual machines, EBS storage, ELB, ASG
- Instance types (e.g., t2.micro)

### 4. **Amazon EC2 – Instance Storage**
- EBS volumes & snapshots, AMI process
- EC2 Instance Store

### 5. **High Availability & Scalability**
- Auto Scaling Groups (ASG): Launch Templates, Min/Max/Initial Size, Scaling Policies

### 6. **RDS, Aurora, & ElastiCache**
- Managed SQL databases: Postgres, MySQL, Oracle, SQL Server, IBM DB2, Aurora
- Aurora: AWS proprietary, auto fail-over, scaling
- Security: encryption, IAM, Security Groups
- RDS Proxy, MemoryDB for Redis

### 7. **Amazon Route 53**
- DNS service (Global)
- Alias Records, Health Checks

### 8. **Amazon VPC – Basics**
- Key VPC features for the exam

### 9. **Amazon S3**
- Infinitely scalable storage
- Use cases: backup, DR, archive, data lakes, static websites
- S3 Versioning, Storage Classes (Standard, Standard-IA, One Zone-IA, Glacier)

### 10. **AWS CLI, SDK, IAM Roles & Policies**
- AWS CLI: command-line tool
- SDK: language-specific APIs
- IAM Roles, Access Keys

### 11. **Amazon S3 – Advanced**
- S3 Analytics, Event Notifications (Lambda, SQS, SNS, EventBridge)
- S3 Object Lambda Access Points

### 12. **Amazon S3 – Security**

### 13. **Amazon CloudFront**
- Content Delivery Network (CDN), edge caching (Global)
- Security: Shield, WAF, Origin Access Control (OAC)
- Real-time logging (Kinesis Data Streams/Firehose)

### 14. **Containers on AWS**
- Docker, Amazon ECR
- ECS, EKS, Fargate
- ECS Task Definitions, Data Volumes

### 15. **AWS Elastic Beanstalk**
- Platform as a Service (PaaS) for code deployment

### 16. **AWS CloudFormation**
- Declarative infrastructure as code (JSON/YAML templates)
- Concepts: Stacks, Templates, Resources, Parameters, Mappings, Outputs
- Intrinsic Functions, Deletion/Stack Policies

### 17. **AWS Integration & Messaging**
- SQS: message queues, DLQ, FIFO
- SNS: Pub/Sub, integrations
- Kinesis: Data Streams, Firehose, Analytics

### 18. **AWS Monitoring, Troubleshooting & Audit**
- CloudWatch: metrics, logs, alarms, Synthetics Canaries
- EventBridge, X-Ray (tracing), CloudTrail (audit, compliance)

### 19. **AWS Lambda**
- Serverless compute
- Language support, execution roles, logging, monitoring
- Invocation types, error handling

### 20. **Amazon DynamoDB**
- NoSQL database: Tables, Items, Attributes
- Primary Key, Query/Scan, DAX (caching), Streams, Transactions
- Fine-grained access control

### 21. **Amazon API Gateway**
- Build, deploy, manage APIs (REST, WebSocket)
- Security, throttling, caching, logging, tracing
- Resource Policies, Cognito integration

### 22. **AWS CICD**
- CodeCommit, CodePipeline, CodeBuild, CodeDeploy, CodeStar
- Pipeline stages, buildspec.yml

### 23. **AWS Serverless Application Model (SAM)**
- Define serverless apps, local testing, leverages CloudFormation

### 24. **AWS Cloud Development Kit (CDK)**
- Define infrastructure in code (TypeScript, Python, Java, .NET)
- Compiles to CloudFormation, uses constructs (L1, L2, L3/Patterns)

### 25. **Amazon Cognito**
- Managed user sign-up/sign-in, access control
- Used by Amplify

### 26. **Other Serverless Services**
- Lambda, DynamoDB, Cognito, API Gateway, S3, SNS, SQS, Kinesis Firehose, Aurora Serverless, Step Functions, Fargate

### 27. **Advanced Identity in AWS**
- AWS STS: temporary credentials, AssumeRole, AssumeRoleWithWebIdentity

### 28. **AWS Security & Encryption**
- KMS: key management (symmetric/asymmetric, key types, rotation, policies)
- CloudHSM, SSM Parameter Store, CloudFormation dynamic references

### 29. **Other Services**
- SES (email), OpenSearch, Athena, MSK (Kafka), Private CA, Macie

### 30. **Exam Preparation**
- Practice, elimination, whitepapers, FAQs, AWS community
- Exam: 65 questions, 130 minutes
