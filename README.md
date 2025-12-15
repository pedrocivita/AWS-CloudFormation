# AWS CloudFormation Full-Stack Infrastructure

[![AWS](https://img.shields.io/badge/AWS-CloudFormation-FF9900?style=flat&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/cloudformation/)
[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-2.x-000000?style=flat&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![DynamoDB](https://img.shields.io/badge/DynamoDB-NoSQL-4053D6?style=flat&logo=amazon-dynamodb&logoColor=white)](https://aws.amazon.com/dynamodb/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Cloud Computing Final Project - Computer Engineering, Insper**  
**Author:** Pedro Toledo Piza Civita  
**Date:** May 2024

**Contact:**  
Email: pedrotpc@al.insper.edu.br | pedrotpcivita@gmail.com  
LinkedIn: [linkedin.com/in/pedrocivita](https://linkedin.com/in/pedrocivita)

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Technology Stack](#technology-stack)
- [Architecture](#architecture)
  - [Architecture Diagram](#architecture-diagram)
  - [Simplified Architecture](#simplified-architecture)
  - [Architecture Components](#architecture-components)
- [Application: Contact List Manager](#application-contact-list-manager)
  - [Features](#features)
  - [DynamoDB Integration](#dynamodb-integration)
  - [Application Interface](#application-interface)
- [Technical Decisions](#technical-decisions)
  - [Region Selection](#region-selection)
  - [Instance Types](#instance-types)
  - [Auto Scaling Configuration](#auto-scaling-configuration)
  - [Security Configuration](#security-configuration)
  - [Load Balancing](#load-balancing)
  - [Scaling Policies](#scaling-policies)
  - [NoSQL Database (DynamoDB)](#nosql-database-dynamodb)
- [Prerequisites](#prerequisites)
  - [AWS Account](#aws-account)
  - [Required Tools](#required-tools)
  - [Software Dependencies](#software-dependencies)
  - [IAM Permissions Setup](#iam-permissions-setup)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Deployment](#deployment)
  - [Accessing the Application](#accessing-the-application)
- [Load Testing](#load-testing)
  - [Running Locust Tests](#running-locust-tests)
  - [Test Results Analysis](#test-results-analysis)
  - [Auto Scaling Validation](#auto-scaling-validation)
- [Infrastructure Teardown](#infrastructure-teardown)
- [Cost Analysis](#cost-analysis)
  - [Estimated Monthly Costs](#estimated-monthly-costs)
  - [Actual Cost Analysis](#actual-cost-analysis)
- [Performance Metrics](#performance-metrics)
- [Command Reference](#command-reference)
- [Lessons Learned](#lessons-learned)
- [Future Improvements](#future-improvements)
- [License](#license)
- [Acknowledgments](#acknowledgments)

---

## Overview

This project demonstrates the design and implementation of a highly available, scalable, and secure cloud infrastructure on Amazon Web Services (AWS) using Infrastructure as Code (IaC) principles with CloudFormation. The architecture provisions a full-stack web application with automatic scaling capabilities, load balancing, and managed NoSQL database services.

The project showcases best practices in cloud architecture design, including multi-availability zone deployment, automated scaling based on performance metrics, and secure IAM-based access control. It serves as a practical example of modern cloud infrastructure management and DevOps principles.

## Key Features

- **Infrastructure as Code**: Complete infrastructure defined in CloudFormation YAML templates
- **High Availability**: Multi-AZ deployment across three availability zones
- **Auto Scaling**: Dynamic scaling based on CPU utilization (5% scale-up, 1% scale-down)
- **Load Balancing**: Application Load Balancer distributing traffic across instances
- **Managed Database**: Amazon DynamoDB with pay-per-request billing
- **Security**: Comprehensive IAM roles and Security Groups
- **Performance Testing**: Integrated load testing with Locust
- **Cost Optimization**: Automated resource management and detailed cost analysis

## Technology Stack

**Cloud Infrastructure:**
- AWS CloudFormation
- Amazon EC2 (t2.micro instances)
- Application Load Balancer (ALB)
- Auto Scaling Groups
- Amazon DynamoDB
- Amazon VPC
- AWS IAM
- Amazon CloudWatch

**Application Stack:**
- Python 3.x
- Flask Web Framework
- Boto3 (AWS SDK for Python)

**DevOps Tools:**
- AWS CLI
- Locust (Load Testing)
- Git

## Architecture

### Architecture Diagram

The complete architecture diagram illustrates the full AWS infrastructure deployment:

![AWS Infrastructure](./img/awsInfrastructure.png)

### Simplified Architecture

A simplified view highlighting the core components and their relationships:

![AWS Infrastructure Simplified](./img/awsSimples.png)

### Architecture Components

1. **VPC (Virtual Private Cloud)**
   - Custom isolated network environment in AWS
   - CIDR Block: 10.0.0.0/16 (65,536 IP addresses)
   - DNS Support and DNS Hostnames enabled
   - Provides network isolation and security

2. **Public Subnets**
   - Three subnets across different availability zones for high availability
   - CIDR Blocks: 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24
   - Auto-assign public IP addresses enabled
   - Fault-tolerant architecture across multiple AZs

3. **Internet Gateway**
   - Enables communication between VPC and the internet
   - Attached to VPC for external connectivity
   - Routes traffic to and from public subnets

4. **Route Tables**
   - Public route table with internet gateway route (0.0.0.0/0)
   - Associated with all public subnets
   - Manages network traffic routing

5. **Security Groups**
   - Virtual firewall for EC2 instances
   - Inbound rules: HTTP (80), HTTPS (443) from anywhere
   - Outbound rules: All traffic allowed
   - Stateful filtering for enhanced security

6. **Application Load Balancer (ALB)**
   - Distributes incoming traffic across multiple EC2 instances
   - Configured across three availability zones
   - Health checks every 20 seconds
   - HTTP listener on port 80

7. **Auto Scaling Group**
   - Min: 1, Max: 5, Desired: 3 instances
   - Launch Configuration with t2.micro instances
   - Automated scaling based on CloudWatch alarms
   - UserData script for application bootstrap

8. **DynamoDB Table**
   - Table name: ListaDeContatos
   - Primary key: id (String)
   - Pay-per-request billing mode
   - Highly available and scalable NoSQL database

## Application: Contact List Manager

The Contact List Manager is a Flask-based web application that demonstrates cloud-native application development with AWS services integration.

### Features

- **Add Contacts**: Create new contact entries with name, phone, and email
- **View Contacts**: Display all contacts from the DynamoDB database
- **Delete Contacts**: Remove contacts from the list
- **Real-time Updates**: Immediate synchronization with DynamoDB
- **Scalable Architecture**: Handles high traffic through auto-scaling

### DynamoDB Integration

The application uses boto3 (AWS SDK for Python) to communicate with DynamoDB. Each contact is stored as an item in the `ListaDeContatos` table with a unique identifier. The integration leverages:
- DynamoDB's serverless architecture
- Automatic scaling based on demand
- Low-latency data access
- High availability across multiple AZs

### Application Interface

![Application Interface](img/interfaceApp.png)

## Technical Decisions

### Region Selection

**Region:** us-east-1 (N. Virginia)

**Rationale:**
- Lower costs compared to other regions
- Comprehensive service availability
- Reduced latency for target user base
- Extensive AWS support and documentation

### Instance Types

**Instance Type:** t2.micro

**Justification:**
- AWS Free Tier eligible for cost optimization
- Sufficient performance for application workload
- Burstable CPU performance for variable loads
- Ideal for development and testing environments

### Auto Scaling Configuration

**Scale-Up Policy:**
- Trigger: CPU utilization > 5%
- Evaluation period: 2 consecutive 30-second periods
- Action: Add 1 instance
- Cooldown: 30 seconds

**Scale-Down Policy:**
- Trigger: CPU utilization < 1%
- Evaluation period: 2 consecutive 30-second periods
- Action: Remove 1 instance
- Cooldown: 30 seconds

These aggressive thresholds ensure rapid response to load changes while minimizing costs during low-traffic periods.

### Security Configuration

**EC2 Security Group:**
- HTTP (80): Open to 0.0.0.0/0 for public web access
- HTTPS (443): Open to 0.0.0.0/0 for secure communication

**IAM Roles and Policies:**
- **EC2 Instance Role**: Grants necessary permissions for:
  - DynamoDB operations (Scan, GetItem, PutItem, UpdateItem, DeleteItem)
  - S3 read access for application files
  - CloudWatch metrics publishing
- **Principle of Least Privilege**: Only required permissions granted
- **No Hardcoded Credentials**: All access through IAM roles

### Load Balancing

**Application Load Balancer Configuration:**
- Protocol: HTTP on port 80
- Health check path: /
- Health check interval: 20 seconds
- Unhealthy threshold: 2 consecutive failures
- Healthy threshold: 5 consecutive successes
- Multi-AZ deployment for high availability

### Scaling Policies

CloudWatch Alarms monitor CPU utilization and trigger scaling actions:
- **CPUUtilizationAlarmHigh**: Scales up when CPU > 5%
- **CPUUtilizationAlarmLow**: Scales down when CPU < 1%
- Simple scaling policy with immediate response
- 30-second cooldown to prevent rapid oscillation

### NoSQL Database (DynamoDB)

**Configuration:**
- Table: ListaDeContatos
- Primary Key: id (String)
- Billing Mode: PAY_PER_REQUEST
- Benefits:
  - Automatic scaling without capacity planning
  - Serverless operation
  - Low-latency performance
  - Built-in redundancy and backups

## Prerequisites

### AWS Account

- Active AWS account with appropriate permissions
- AWS Access Key ID and Secret Access Key configured
- Sufficient service limits for EC2, VPC, and DynamoDB resources

### Required Tools

- **AWS CLI**: [Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- **Git**: [Installation Guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- **Python 3.x**: [Download](https://www.python.org/downloads/)

### Software Dependencies

**Windows:**
```powershell
pip install locust
```

**Linux:**
```bash
sudo apt-get update
sudo apt-get install python3 python3-pip
pip3 install locust
```

**macOS:**
```bash
brew install python
pip3 install locust
```

### IAM Permissions Setup

Required IAM policies for deployment:
- AdministratorAccess (or equivalent permissions for)
- AmazonDynamoDBFullAccess
- AmazonEC2FullAccess
- AmazonSSMFullAccess
- AWSCloudFormationFullAccess
- ElasticLoadBalancingFullAccess
- IAMFullAccess

**Configuration Steps:**
1. Navigate to IAM Console in AWS
2. Create or select a user
3. Attach the required policies
4. Generate access keys for programmatic access
5. Configure AWS CLI:
   ```bash
   aws configure
   ```
   - AWS Access Key ID: [Your Access Key]
   - AWS Secret Access Key: [Your Secret Key]
   - Default region: us-east-1
   - Default output format: json

## Getting Started

### Installation

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/pedrocivita/AWS-CloudFormation
   cd AWS-CloudFormation
   ```

2. **Set Script Permissions (Linux/macOS):**
   ```bash
   chmod +x scriptsSetupCleanup/setup.sh scriptsSetupCleanup/cleanup.sh
   ```

### Deployment

**Windows:**
```powershell
.\scriptsSetupCleanup\setup.ps1
```

**Linux/macOS:**
```bash
./scriptsSetupCleanup/setup.sh
```

The setup script performs the following operations:
1. Creates S3 bucket for application files
2. Uploads Flask application files to S3
3. Validates CloudFormation template
4. Creates CloudFormation stack
5. Provisions all infrastructure resources

**Estimated deployment time:** 3-5 minutes

### Accessing the Application

1. **Retrieve ALB DNS Name:**
   - Navigate to CloudFormation console
   - Select the stack `StackDoCivitaApp`
   - Go to Outputs tab
   - Copy the `ALBDNSName` value

2. **Access the Web Application:**
   ```
   http://<ALB_DNS_NAME>
   ```

3. **Verify DynamoDB Integration:**
   - Add contacts through the web interface
   - Navigate to DynamoDB console
   - Select `ListaDeContatos` table
   - View items to confirm data persistence

## Load Testing

### Running Locust Tests

**Interactive Mode:**
```bash
pip install locust
locust -f locustfile.py
```
Access the Locust web interface at `http://localhost:8089`

**Headless Mode:**
```bash
locust -f locustfile.py --headless -u 500 -r 50 --run-time 10m
```
- 500 concurrent users
- 50 users/second spawn rate
- 10-minute test duration

### Test Results Analysis

Performance testing with 500 concurrent users demonstrated the effectiveness of the Auto Scaling configuration:

**EC2 Instances During Load Test:**

![Instances with Details](img/instanciasComDetalhes.png)

![Instances without Details](img/instanciasSemDetalhes.png)

**Locust Performance Metrics:**

![Locust Graphs](img/graficosLocust.png)

Key observations:
- **Total Requests per Second**: Consistent throughput during high load
- **Response Times**: Low latency maintained across all instances
- **Number of Users**: Gradual ramp-up to 500 concurrent users
- **Auto Scaling Trigger**: New instances launched when CPU > 5%

### Auto Scaling Validation

**Post-Test Scale Down:**

![Instances After Scale Down](img/insanciasAposScaleDown.png)

After load test completion, the Auto Scaling Group automatically reduced capacity from 5 to 1 instance, demonstrating:
- Effective cost optimization
- Proper CloudWatch alarm configuration
- Reliable scaling policy execution
- Infrastructure elasticity

## Infrastructure Teardown

**Windows:**
```powershell
.\scriptsSetupCleanup\cleanup.ps1
```

**Linux/macOS:**
```bash
./scriptsSetupCleanup/cleanup.sh
```

The cleanup script:
1. Deletes the CloudFormation stack
2. Waits for stack deletion completion
3. Removes the S3 bucket and all contents

**Estimated cleanup time:** 2-3 minutes

## Cost Analysis

### Estimated Monthly Costs

Cost estimation using AWS Pricing Calculator:

| Resource | Monthly Cost |
|----------|-------------|
| EC2 Instances (3x t2.micro) | $25.40 |
| Application Load Balancer | $31.11 |
| Amazon VPC | $12.78 |
| Amazon CloudWatch | $3.06 |
| DynamoDB | $8.13 |
| **Total** | **$80.48** |

**Cost Breakdown:**
- **EC2**: 3 instances × $0.0116/hour × 730 hours
- **ALB**: Fixed costs + data processing charges
- **VPC**: Data transfer and endpoint usage
- **CloudWatch**: Metrics, alarms, and logs
- **DynamoDB**: Storage ($0.25/GB) + Read/Write requests

### Actual Cost Analysis

Real-world costs (normalized to 31 days):

| Resource | Actual Monthly Cost |
|----------|-------------------|
| Elastic Load Balancing | $25.07 |
| EC2 Instances | $23.58 |
| Amazon VPC | $21.88 |
| Amazon CloudWatch | $4.93 |
| EC2 Other | $2.19 |
| DynamoDB | $0.72 |
| **Total** | **$78.38** |

**Actual vs. Estimated Costs:**

![Real Costs - AWS Console](./img/custosReais.png)

![Adjusted Real Costs (31 Days)](./img/custosReaisAjustados.png)

The actual costs closely matched estimates, validating the cost prediction methodology. DynamoDB's pay-per-request model resulted in lower costs due to test workload patterns.

## Performance Metrics

**Deployment Timeline:**

| Operation | Duration |
|-----------|----------|
| S3 Bucket Creation | 5 seconds |
| File Upload to S3 | 10 seconds |
| Template Validation | 3 seconds |
| CloudFormation Stack Creation | 3-5 minutes |
| Cleanup Execution | 2-3 minutes |
| Locust Installation | 1 minute |

**Load Test Results:**
- **Peak Load**: 500 concurrent users
- **Average Response Time**: < 100ms
- **Success Rate**: > 99.9%
- **Auto Scaling Response**: < 60 seconds to provision new instances
- **Scale Down Time**: ~5 minutes after load reduction

## Command Reference

### S3 Bucket Management

**Create bucket:**
```bash
aws s3 mb s3://bucket-do-civita --region us-east-1
```

**Upload application files:**
```bash
aws s3 cp appFiles/app.py s3://bucket-do-civita/app.py
aws s3 cp appFiles/dynamo_service.py s3://bucket-do-civita/dynamo_service.py
aws s3 cp appFiles/home.html s3://bucket-do-civita/home.html
```

**Delete bucket:**
```bash
aws s3 rb s3://bucket-do-civita --force
```

### CloudFormation Stack Management

**Validate template:**
```bash
aws cloudformation validate-template --template-body file://full-stack.yaml
```

**Create stack:**
```bash
aws cloudformation create-stack \
  --stack-name StackDoCivitaApp \
  --template-body file://full-stack.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
```

**Update stack:**
```bash
aws cloudformation update-stack \
  --stack-name StackDoCivitaApp \
  --template-body file://full-stack.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
```

**Delete stack:**
```bash
aws cloudformation delete-stack --stack-name StackDoCivitaApp
```

### Load Testing with Locust

**Install Locust:**
```bash
pip install locust
```

**Run interactive mode:**
```bash
locust -f locustfile.py
```

**Run headless mode:**
```bash
locust -f locustfile.py --headless -u 500 -r 50 --run-time 10m
```

## Lessons Learned

1. **Infrastructure as Code Benefits**: CloudFormation templates enable reproducible, version-controlled infrastructure deployments
2. **Auto Scaling Configuration**: Aggressive CPU thresholds (5%/1%) ensure rapid response to load changes
3. **Cost Optimization**: Pay-per-request DynamoDB billing significantly reduces costs for variable workloads
4. **Multi-AZ Deployment**: Crucial for high availability and fault tolerance
5. **Load Testing Importance**: Performance testing validates architecture decisions and scaling policies
6. **Security Best Practices**: IAM roles eliminate credential management overhead and improve security

## Future Improvements

- **HTTPS Implementation**: Add SSL/TLS certificates via AWS Certificate Manager
- **Custom Domain**: Route 53 for DNS management and custom domain configuration
- **Database Backups**: Implement automated DynamoDB point-in-time recovery
- **Monitoring Dashboard**: CloudWatch dashboards for comprehensive metrics visualization
- **CI/CD Pipeline**: Automate deployments with AWS CodePipeline and CodeDeploy
- **Container Migration**: Consider ECS/Fargate for containerized deployments
- **Caching Layer**: ElastiCache for improved application performance
- **Global Distribution**: CloudFront CDN for reduced latency worldwide

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

This project was developed as part of the Cloud Computing course (6th semester) in the Computer Engineering program at Insper. Special thanks to the professors and colleagues who provided guidance and support throughout the development process.

The experience gained in designing and implementing this cloud infrastructure has been invaluable for understanding modern cloud architecture patterns, DevOps practices, and AWS services.

---

**Repository:** [github.com/pedrocivita/AWS-CloudFormation](https://github.com/pedrocivita/AWS-CloudFormation)

**Project Documentation:** [documentacaoProjetoFinalCloudPedroCivita.pdf](documentacaoProjetoFinalCloudPedroCivita.pdf)

**Contact:**  
Email: pedrotpc@al.insper.edu.br | pedrotpcivita@gmail.com  
LinkedIn: [linkedin.com/in/pedrocivita](https://linkedin.com/in/pedrocivita)