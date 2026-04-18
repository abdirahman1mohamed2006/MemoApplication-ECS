# ECS-MEMO APPLICATION

## Overview

I created a  end-to-end AWS ECS deployment of a  memo application, built using a multi-build container , Terraform, and GitHub Actions to automate infrastructure and application delivery.

#### Live demo:
 Video not working sorry :(
<img width="1261" height="727" alt="image" src="https://github.com/user-attachments/assets/07e9486e-887a-42d9-8b1c-43db43e07651" />

## Key Features : 

- **Infrastructure as Code** using Terraform  
- **Multi-stage Docker builds** for efficient images  
- **Automated CI/CD** with GitHub Actions and OIDC  
- **ECS Fargate deployment** in private subnets  
- **High availability** across multiple availability zones  

## Architecture:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/7e25cb43-3803-4f98-9987-c292a7bcb06f" />

### Infrastructure

- **Compute:** AWS ECS Fargate for running containerised applications without managing servers  
- **Networking:** Custom VPC with public and private subnets deployed across multiple availability zones  
- **Outbound Access:** NAT Gateway in public subnets enabling secure internet access from private workloads  
- **Load Balancing:** Application Load Balancer (ALB) handling HTTPS traffic and routing to ECS services  
- **Security:** Workloads isolated in private subnets with controlled access via security groups  
- **DNS & TLS:** Route 53 for domain routing with SSL certificates managed by AWS Certificate Manager (ACM)  
- **Container Registry:** Amazon ECR for storing and managing Docker images  
- **State Management:** Terraform state stored in S3 with versioning, using DynamoDB for state locking

## Repository Structure

<img width="567" height="543" alt="image" src="https://github.com/user-attachments/assets/999a91ce-7e03-4808-a47d-47e3319275b1" />

##  Build & Deployment Workflow

### Prerequisites

- AWS account with CLI configured  
- Terraform (v1.6.0 or newer)  
- Docker Desktop installed  
- GitHub account  
- Registered domain name  

---

### 1. Containerisation

- Packaged the **Memos** application into a Docker container using a multi-stage build  
- Optimised the image size from ~1.44GB down to ~200MB by reducing unnecessary layers  
- Verified the container locally by exposing it on port `5320`  

---

### 2. Initial AWS Setup (Manual)

- Created an Amazon ECR repository and uploaded the initial container image  
- Provisioned core infrastructure manually via the AWS Console to understand service interactions  
- Set up VPC, subnets, security groups, Application Load Balancer, and ECS cluster  
- Configured HTTPS using ACM and mapped the domain with Route 53  
- Validated the full application flow end-to-end  

---

### 3. Infrastructure as Code (Terraform)

- Removed manually created resources after validation  
- Recreated the full infrastructure using modular Terraform components  
  *(VPC, ALB, ECS, Route 53, ACM, ECR)*  
- Introduced a **NAT Gateway** to allow secure outbound access from private subnets  
- Configured remote state storage in S3 via a bootstrap process  
- Enabled DynamoDB state locking to prevent concurrent Terraform runs  

---

### 4. CI/CD Automation

- Infrastructure deployment is triggered manually using a Terraform apply workflow  
- Successful infrastructure updates automatically trigger downstream pipelines  

**Deployment pipeline includes:**
- Building the Docker image  
- Pushing images to Amazon ECR (`latest` + commit SHA tags)  
- Updating ECS service to trigger rolling deployments (no service recreation)  
- Running automated post-deployment health checks with retry logic  

---

### Workflows

- `apply.yaml` *(manual)* — Terraform init → validate → plan → apply  
- `deploy.yaml` *(automated)* — Builds, pushes, and deploys the application  
- `plan.yaml` — Generates Terraform execution plan  
- `destroy.yaml` *(manual)* — Tears down all infrastructure  

<img width="533" height="97" alt="plan" src="https://github.com/user-attachments/assets/58bec4e5-0ae2-45c5-911e-c27f9e72106b" />
<img width="602" height="100" alt="build2" src="https://github.com/user-attachments/assets/8cfa9bbb-0a17-4ce6-a89f-8725bc4d2e1f" />
<img width="532" height="183" alt="actions1" src="https://github.com/user-attachments/assets/dca5be94-fb97-41ca-9433-92e6847bf871" />










