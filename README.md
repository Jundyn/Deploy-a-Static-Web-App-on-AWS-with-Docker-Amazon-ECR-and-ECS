# Deploying a Static Web App on AWS with Docker, Amazon ECR, and ECS

Welcome to the deployment guide for the static HTML web app on AWS! This guide covers the steps and configurations used to deploy a static web app using Docker, Amazon ECR, and ECS. The project aims to demonstrate the integration of these technologies to ensure a scalable, secure, and highly available web application.

## Project Overview

This project involves deploying a static HTML web application on AWS, leveraging Docker for containerization, Amazon ECR for image storage, and Amazon ECS for container orchestration. The architecture ensures high availability, scalability, and secure access to the web app.

## Architecture Diagram

Refer to the ![Alt text](https://github.com/Jundyn/Deploy-a-Static-Web-App-on-AWS-with-Docker-Amazon-ECR-and-ECS/blob/main/1._Docker.jpg) for a visual representation of the architecture.

## Components Used

1. **Virtual Private Cloud (VPC)**
   - Configured with a 3-Tier VPC setup including Public Subnets, Private App Subnets, and Private Data Subnets across 2 Availability Zones.

2. **Public Subnets**
   - Hosts the NAT Gateway and Application Load Balancer for handling incoming traffic.

3. **Internet Gateway**
   - Allows communication between instances within the VPC and the internet.

4. **Private Subnets**
   - Hosts the web server in a secure environment.

5. **AWS Fargate**
   - Manages containerized applications without the need for server management.

6. **Application Load Balancer**
   - Distributes incoming web traffic across multiple instances to ensure high availability.

7. **Availability Zones**
   - Enhances fault tolerance and availability by distributing resources across multiple zones.

8. **Auto Scaling Group**
   - Automatically adjusts the number of instances to match demand and maintain availability.

9. **Route 53**
   - Manages domain registration and DNS routing.

10. **Docker**
    - Used to containerize the web application. Dockerfile is provided in the repository.

11. **Security Groups**
    - Controls inbound and outbound traffic to ensure security.

12. **GitHub**
    - Hosts the source code, Dockerfile, and other project files.

13. **Certificate Manager**
    - Manages SSL/TLS certificates to secure data in transit.

14. **Simple Notification Service (SNS)**
    - Configured to send notifications about Auto Scaling Group activities.

15. **IAM Roles**
    - Provides permissions for accessing AWS resources and managing secrets.

## Dockerfile

The Dockerfile used to build the Docker image for the static web app is as follows:

```Dockerfile
FROM amazonlinux:latest

# Install dependencies
RUN yum update -y && \
    yum install -y httpd && \
    yum search wget && \
    yum install wget -y && \
    yum install unzip -y 

# Change directory
WORKDIR /var/www/html

# Download web files
RUN wget ![Alt text](https://github.com/Jundyn/Deploy-a-Static-Web-App-on-AWS-with-Docker-Amazon-ECR-and-ECS/blob/main/jupiter.zip)

# Unzip folder
RUN unzip jupiter.zip

# Copy files into html directory
RUN cp -r jupiter-main/* /var/www/html/

# Remove unwanted folder
RUN rm -rf jupiter-main jupiter.zip

# Expose port 80 on the container
EXPOSE 80

# Set the default application that will start when the container starts
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

## Deployment Steps

1. **Set Up the VPC and Subnets**
   - Create a 3-Tier VPC with Public and Private Subnets across 2 Availability Zones.

2. **Deploy Infrastructure Components**
   - Set up NAT Gateway, Application Load Balancer, and other necessary components.

3. **Build and Push Docker Image**
   - Build the Docker image using the provided Dockerfile and push it to Amazon ECR.

4. **Configure AWS Fargate**
   - Set up ECS to run the Docker container using AWS Fargate for serverless container management.

5. **Set Up Auto Scaling Group**
   - Configure Auto Scaling Group to manage instance scaling based on demand.

6. **Configure Route 53**
   - Register the domain name and set up DNS records to route traffic to the Application Load Balancer.

7. **Secure the Application**
   - Use AWS Certificate Manager to handle SSL/TLS certificates for encrypted communication.

8. **Configure SNS Notifications**
   - Set up SNS to receive notifications about activities and changes in the Auto Scaling Group.

9. **Review Security Groups**
   - Ensure that security groups are properly configured to allow necessary traffic and enhance security.

## Web page display

 ![Alt text](https://github.com/Jundyn/Deploy-a-Static-Web-App-on-AWS-with-Docker-Amazon-ECR-and-ECS/blob/main/WebPage.png)

 Deploying a static HTML web app on AWS using Docker, Amazon ECR, and ECS can be straightforward, but several issues occurred during deployment. Here are some of the problems and how it was resolved:

### 1. **Docker Image Build Issues**

**Problem:**
- Errors during the Docker image build process due to incorrect Dockerfile syntax/missing dependencies.

**Solution:**
- **I Checked Dockerfile Syntax:** I ensured that the Dockerfile syntax is correct. Refer to [Dockerfile best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) for guidance.
- **Verify Dependencies:** I ensured that all required packages and dependencies are correctly installed. Reviewed the logs for missing packages or errors during the build process.
- **Test Locally:** Built and run the Docker image locally to verify that it worked before pushing it to Amazon ECR.

### 2. **Amazon ECR Push/Pull Issues**

**Problem:**
- Problems with pushing the Docker image to Amazon ECR or pulling it from ECR.

**Solution:**
- **Authentication:** I ensured that AWS CLI was properly authenticated to interact with Amazon ECR. Use `aws ecr get-login-password` to authenticate Docker.
- **Repository Existence:** Verify that the ECR repository exists and is correctly named.
- **Permissions:** I ensured that IAM permissions for pushing and pulling images. The role or user has the `ecr:PutImage`, `ecr:InitiateLayerUpload`, `ecr:UploadLayerPart`, and `ecr:CompleteLayerUpload` permissions.

### 3. **ECS Task and Service Issues**

**Problem:**
- ECS tasks fail to start or services didn't deploy as expected due ecs/ecr authorization issue.

**Solution:**
- **Task Definition:** I ensured that the ECS task definition is correctly configured, including the Docker image URL, port mappings and IAM Role policy/Permission.
- **Logs and Metrics:** I used event logs/CloudWatch logs and metrics to diagnose issues with ECS tasks. Review the logs for error messages and troubleshoot accordingly.

### 8. **IAM Permissions Issues**

**Problem:**
- Insufficient IAM permissions prevent deployment or access to resources.

**Solution:**
- **Verify IAM Roles:** Checked that IAM roles and policies provided the necessary permissions for ECS, ECR, and other AWS services involved in the deployment.

I carefully addressed these issues and implementing these solutions lead to the deployment of a static HTML web app on AWS with ECS and ECR. 


## References

- [AWS Documentation](https://docs.aws.amazon.com/)
- [Docker Documentation](https://docs.docker.com/)
