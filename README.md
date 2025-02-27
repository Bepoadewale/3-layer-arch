# 3-Layer Architecture Deployment with AWS CloudFormation

This repository demonstrates the deployment of a 3-layer microservices architecture using AWS CloudFormation and GitHub Actions.

## Project Architecture
1. **Foundation Layer**
This layer establishes the networking infrastructure:

- **VPC**: Isolates resources within a dedicated network.
- **Subnets**: 4 subnets (2 public, 2 private) spread across multiple AZs for fault tolerance.
- **Internet Gateway & NAT Gateway**: Provides internet access for public subnets and private resources, respectively.
- **Route Tables**: Direct traffic between public and private subnets.

2. **Security Layer**
Ensures system security and monitoring:

- **Security Groups**: Restricts access to only specific ports (e.g container port 5000).
- **Amazon GuardDuty**: Provides continuous threat detection and sends alerts to an SNS topic.
- **SNS Topics**: ECSNotifications for ECS-related alerts and GuardDutyNotifications for threat notifications.
- **CloudWatch Alarms**: Monitors ECS task metrics and triggers notifications for anomalies.

3. **Application Layer**
Deploys and manages the microservices using ECS:

- **ECS Cluster**: Hosts containerized services using Fargate.
- **Task Definition**: Defines container configurations (image, CPU, memory).
- **ECS Service**: Ensures fault-tolerant deployment across AZs.
- **Auto-Scaling**: Dynamically scales up and down ECS tasks (2 - 10) based on CPU utilization.

## Repo Structure

- **app.py**: Contains the Python application code for the web service.

- **cloudformation-template.yaml**: Defines the AWS infrastructure resources, including VPC, subnets, ECS cluster, task definitions, services, and associated IAM roles and policies.

- **dockerfile**: Specifies the Docker image build instructions for the application.

- **requirements.txt**: Lists the Python dependency required by the application.

- **.github/workflows/deploy.yml**: Contains the GitHub Actions workflow for continuous integration and deployment (CI/CD) of the application.

## Prerequisites

Before deploying this architecture, ensure you have the following:

- **AWS Account**: Access to an AWS account with permissions to create the necessary resources.

- **AWS CLI**: Installed and configured with appropriate credentials.

- **Docker**: Installed for building and pushing Docker images.

- **GitHub Secrets**: Configured in your repository settings:
  - `AWS_ACCESS_KEY_ID`: Your AWS access key ID.
  - `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key.
  - `AWS_ACCOUNT_ID`: Your AWS account ID.
  - `SNS_EMAIL`: Email address for SNS notifications.

## Deployment Steps

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/Bepoadewale/3-layer-arch.git
   cd 3-layer-arch
   ```

2. **CI/CD Pipeline**:

The GitHub Actions workflow (`.github/workflows/deploy.yml`) automates the deployment process:

- **Steps**:
  - Checks out the code.
  - Configures AWS credentials.
  - Builds and pushes the Docker image to Amazon ECR.
  - Deploys the CloudFormation stack.
  - Prompts for SNS subscription confirmation.
  - Updates the ECS service to force a new deployment.

3. **Confirm SNS Subscriptions**:

   Check your email for 2 subscription confirmation mails from AWS SNS and confirm them to receive notifications.

## Cleanup

To delete the deployed resources and avoid incurring charges:

```bash
aws cloudformation delete-stack --stack-name MyMicroservicesStack
```
