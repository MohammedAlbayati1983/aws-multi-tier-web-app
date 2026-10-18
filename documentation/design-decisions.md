# Design Decisions & Architecture Notes

This document outlines the architectural choices made during the implementation of the multi-tier web application. It also highlights deviations from a full production environment to accommodate the constraints of a lab setting, ensuring transparency and honesty.

## 1. Networking & High Availability
- **VPC & Subnets:** Used a `/16` CIDR to allow future growth. Two AZs are utilized to ensure High Availability (HA).
- **Public vs Private:** The Web/App tier is placed in Public Subnets (behind an ALB) to receive internet traffic. RDS is placed in Private Subnets for security.
- **NAT Gateway:** Omitted to avoid high hourly costs in the lab. In production, a NAT Gateway would be placed in the Public Subnet to allow private EC2 instances to download updates.

## 2. Layer 4 Security
- **NACLs (Subnet level):** Used default VPC NACLs (allow all) to prevent connectivity issues in the lab, but documented that strict stateless rules are required in production.
- **Security Groups (Compute level):** Followed the Least Privilege principle. `Web-SG` allows HTTP/HTTPS from the internet and SSH from a specific IP. `RDS-SG` allows MySQL traffic **only** from `Web-SG`.

## 3. IAM & S3 Permissions
- **EC2 Role (`EC2-WebApp-Role`):** Attached to EC2 instances. It includes `AmazonSQSFullAccess` (to act as a Producer) and **`AmazonS3ReadOnlyAccess`**. 
  - *Reasoning for S3:* S3 is not a strict requirement in this phase, but the policy was added to simulate a real-world scenario where the application might serve static assets from S3. It demonstrates forward-thinking and best practices.

## 4. Decoupling (SQS)
- **Architecture:** SQS is placed between the Web/App tier and the Database tier.
- **Producer/Consumer:** The EC2 Web Tier acts as the **Producer**. The **Consumer** (which would be an EC2 Worker or AWS Lambda function) is **not implemented** as part of this infrastructure-only project. The SQS queue and IAM roles are fully configured to support it when application code is deployed.

## 5. Database (RDS)
- **Multi-AZ:** Enabled to ensure automatic failover and high availability (AZ-A to AZ-B).
- **Encryption:** RDS storage is encrypted at rest using AWS managed keys.
- **Automated Backups:** **Disabled**. 
  - *Reasoning:* To minimize costs in this lab environment. In a production environment, automated backups (Point-in-Time Recovery) would be strictly mandatory.

## 6. Hybrid Connectivity (Corporate Datacenter)
- **Design:** The solution requires a primary (Direct Connect) and backup (VPN) connection.
- **Implementation:** A **Virtual Private Gateway (VGW)** and a **Site-to-Site VPN** connection were created.
- **Limitation:** Direct Connect requires physical infrastructure and a telecom provider. The VPN was configured with a dummy IP (`8.8.8.8`) to simulate the Corporate Customer Gateway (CGW). 
  - *Note:* The VPN Tunnels state is `Down`. This is an expected behavior in a lab environment without a real on-premises datacenter. The infrastructure design is fully ready for a real connection.

## 7. Infrastructure as Code (Terraform)
- **Current State:** Infrastructure was provisioned manually via AWS Console to build foundational knowledge.
- **Future Enhancement:** Terraform implementation is planned as a future enhancement (Folder `infrastructure-as-code/terraform/` is reserved). To ensure honesty and accuracy, no Terraform code is included at this stage, as the author is currently undergoing formal Terraform training.

## 8. Auto Scaling & Load Balancing
- **Launch Template:** Configured with Apache `httpd` via User Data to pass ALB Health Checks.
- **Public IP Assignment:** Enabled in the Launch Template, as ALB in a Public Subnet requires the target EC2 to be reachable via its Public IP in this specific lab architecture.