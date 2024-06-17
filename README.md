# Collaborations

This repository contains all the public projects/repositories I collaborated with.

# Projects/Repositories

## 1. **[Ranky][https://github.com/MKGF/Ranky]** - Bot Discord, Java Application.

#### Infrastructure Creation

I orchestrated the deployment of AWS infrastructure using Terraform, focusing on provisioning key components:

- **IAM:**
  - Creation of SSH key pair for EC2 instances and printing it to a file via CLI.
  - Configuration of IAM roles and policies made for EC2 instance.

- **Networking:**
  - Setup of a simple network infrastructure including VPC, subnet, internet gateway, AWS route table, route table association, and security group made for EC2 instance.

- **ECR:**
  - Creation of an Amazon ECR repository to store Docker image generated locally.

- **Dockerfile:**
  - Construction of a simple OpenJDK-based image, incorporating the application's JAR file (previously built through the pipeline).
  - Implementation of an entrypoint to execute `java -jar path/app.jar --spring.profiles.active=secret`, activating the secret profile necessary for API communication.
  - Leveraging Terraform's `null_resource`, automated building, tagging (as `latest`), and uploading of the Docker image to the previously created ECR repository.

- **EC2:**
  - Deployment of an EC2 instance configured with an init script (user_data):
    - Installation and activation of Docker.
    - Authentication with ECR to pull the pre-uploaded Docker image from the repository.
    - Execution of the application.
  - Saving the EC2 public IP to a file via CLI, enabling future connections.

### Pipeline Creation

- **[AWS Create Infrastructure Pipeline][aws-create.yml]:**
  - A pipeline designed to deploy the entire AWS infrastructure, optimized for AWS Free Tier utilization.
  - Workflow includes:
    - Code checkout.
    - Terraform installation and configuration of AWS credentials.
    - Generation of secret files to enable later Terraform execution.
    - JDK setup (matching the version used in the Dockerfile's base image).
    - Test execution and JAR file compilation, moved to a Terraform-accessible directory for subsequent ECR upload.
    - Creation of an S3 bucket to store several files needed for next steps/workflows.
    - Terraform execution.
    - Upload of `terraform.tfstate`, `EC2 SSH key pair`, and `EC2 public IP` to S3 for state management and enabling future connections.

- **[AWS Destroy Infrastructure Pipeline][aws-destroy.yml]:**
  - Designed to automate the destruction of AWS infrastructure once the Free Tier period ends or the project no longer requires cloud hosting.
  - Workflow includes:
    - Checkout of code repository.
    - Terraform installation and configuration of AWS credentials.
    - Download of `terraform.tfstate` file from S3 bucket.
    - Execution of Terraform to destroy infrastructure.
    - Emptying and deletion of S3 bucket used for storing Terraform state files.

### Notes

I acknowledge opportunities for improvement, such as migrating the SSH key to AWS Secrets Manager rather than S3. However, our current focus remains on operating within AWS Free Tier constraints while striving for a cost-free project lifecycle. Future enhancements will address these practices as resource availability expands.

### Explore the Code

You can view the repository's code in its respective folder within this repository or directly in the original MKGF repository (recommended), which ensures you're accessing the most up-to-date version.
