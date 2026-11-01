# 3-Tier-Infrastructure-on-AWS-using-Terraform
Automated 3-Tier AWS Infrastructure (VPC, Multi-AZ Subnets, EC2 Web Server, and RDS MySQL Database) using Terraform (IaC).

# 🚀 AWS 3-Tier Infrastructure Automation with Terraform

This repository contains the Terraform configuration code to automate the provisioning of a secure, multi-AZ **3-Tier Architecture** on AWS.

## 🏗️ Architecture Breakdown

1. **Network Tier:**
   - **VPC:** `Prod-3Tier-VPC` (`10.0.0.0/16`) with DNS hostnames enabled.
   - **Internet Gateway:** `Prod-IGW` attached for public internet access.
   - **Public Subnet:** `Web-Public-Subnet-1a` (`10.0.1.0/24`) in `us-east-1a`.
   - **Private Subnets:** `DB-Private-Subnet-1a` (`10.0.2.0/24`) and `DB-Private-Subnet-1b` (`10.0.3.0/24`) spanning multiple AZs.
   - **Route Tables:** Associated public route table directing `0.0.0.0/0` traffic to IGW.

2. **Compute Tier (Presentation):**
   - **EC2 Instance:** `Web-Server-Instance` (`t3.micro`) running Amazon Linux 2023 deployed in the public subnet.

3. **Database Tier (Data):**
   - **RDS Subnet Group:** `main-db-subnet-group` covering 2 Availability Zones (`us-east-1a` and `us-east-1b`).
   - **RDS Instance:** `App-RDS-Database` running MySQL 8.0 (`db.t3.micro`) safely isolated inside private subnets.

---

## 🛠️ Prerequisites
- [AWS CLI](https://aws.amazon.com/cli/) installed and configured (`aws configure`).
- [Terraform](https://www.terraform.io/) installed (v1.0+).

⚠️ Challenges Faced & Solutions
1: Instance Type Eligibility Error (InvalidParameterCombination)

Challenge: The initial configuration utilized a t2.micro instance type, which triggered an incompatibility error regarding Free Tier eligibility within the target region context.

Solution: Updated the EC2 and RDS instance classes to t3.micro and db.t3.micro respectively, ensuring full compatibility with the target AWS region (us-east-1).

2: RDS Availability Zone Coverage Requirement

Challenge: AWS requires a DB Subnet Group to span a minimum of two distinct Availability Zones to guarantee database high availability, which initially failed due to single-AZ subnet definitions.

Solution: Added a secondary private subnet (10.0.3.0/24) in us-east-1b and incorporated both private subnets into the custom RDS DB Subnet Group.

3: Transient Network Disconnections & DNS Resolution Errors

Challenge: An unexpected network drop during execution caused a no such host DNS lookup failure while Terraform was monitoring the RDS instance creation phase.

Solution: Once the internet connection was re-established, re-running terraform apply allowed Terraform's state management engine to seamlessly detect existing resources and complete the remaining provisioning lifecycle without data corruption.

🚀 Quickstart Guide
Clone the repository:

git clone https://github.com/abuturab9/3-Tier-Infrastructure-on-AWS-using-Terraform.git
cd 3-Tier-Infrastructure-on-AWS-using-Terraform

Initialize Terraform:
terraform init

Review the execution plan:
terraform plan

Deploy the infrastructure:
terraform apply
Clean up resources (To prevent unnecessary AWS costs):

Bash
terraform destroy
