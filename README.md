
(https://github.com/user-attachments/assets/1f327ea2-1c21-448e-8080-d10cea35bff3)

# AWS Architecture for Hosting a Dynamic Web Application

This project demonstrates how to host a dynamic web application using AWS resources. It leverages AWS services to ensure high availability, scalability, and security.

## Architecture Overview

This architecture is designed to host a highly available and scalable dynamic web application on AWS. It uses multiple Availability Zones, load balancing, auto-scaling, and managed database services to ensure seamless performance and reliability.

### Key Components

1. **Amazon Route 53**
   - Acts as the DNS service, routing traffic from end users to the web application.
   
2. **Amazon S3**
   - Stores static content, such as images, CSS, and JavaScript files.
   
3. **IAM Role and IAM User**
   - Provides controlled access to AWS resources. IAM Roles are assigned to services like EC2 instances, while IAM Users are for administrators.
   
4. **EC2 Instances (Web Servers)**
   - EC2 instances are deployed in an Auto Scaling Group across multiple Availability Zones to ensure high availability and redundancy.
   
5. **Elastic Load Balancer (Application Load Balancer)**
   - Distributes incoming traffic to EC2 instances across different Availability Zones for load balancing and failover support.
   
6. **Amazon RDS (Relational Database Service)**
   - Managed relational database service that stores dynamic content. A primary database is in one availability zone, with a standby replica in another to provide failover support.

7. **Amazon VPC (Virtual Private Cloud)**
   - This entire infrastructure is hosted within a VPC with both public and private subnets to isolate resources.
     - **Public Subnets**: For internet-facing resources like the NAT Gateway.
     - **Private Subnets**: For internal resources such as EC2 instances and RDS databases.
   
8. **NAT Gateway**
   - Allows instances in private subnets to access the internet without exposing them to incoming traffic from the internet.

9. **EC2 Instance Connect Endpoint**
   - Used by the administrator for managing and connecting to the EC2 instances securely without exposing the instances to the internet.

10. **Auto Scaling Group**
    - Automatically adjusts the number of EC2 instances depending on the traffic load, ensuring that the application scales dynamically based on demand.

11. **Amazon Certificate Manager**
    - Provides SSL certificates to secure communications between clients and the application using HTTPS.

### Security Groups and Subnets

- **Public Subnets**: Resources that require direct access from the internet, such as the NAT Gateway and Load Balancer.
- **Private Subnets (App)**: Web servers (EC2 instances) are deployed in private subnets to limit their exposure to external traffic.
- **Private Subnets (Data)**: RDS databases are deployed in private subnets to further enhance security, ensuring only internal application traffic can access the database.

### High Availability and Fault Tolerance

- **Multi-AZ Deployment**: EC2 instances and RDS databases are distributed across multiple Availability Zones (AZs), ensuring that the application continues to function even if one AZ experiences an outage.
- **Auto Scaling**: Automatically adds or removes EC2 instances based on traffic, ensuring the application can handle varying load without manual intervention.
- **Standby RDS Instance**: In case of failure of the primary RDS instance, the standby replica in another Availability Zone takes over, ensuring minimal downtime.

---

## How to Deploy This Architecture

### Prerequisites

1. **AWS Account**: Make sure you have an active AWS account.
2. **IAM User with Sufficient Privileges**: Ensure you have the necessary permissions to create and manage resources like EC2, RDS, VPC, and IAM.
3. **Domain Name**: For Route 53 to work, you should have a registered domain.

### Deployment Steps

1. **Set Up VPC and Subnets**: 
   - Create a VPC with public and private subnets in at least two Availability Zones.
   - Attach Internet Gateway to the public subnets and configure routing tables.

2. **Create Security Groups**:
   - Define security groups for EC2 instances, RDS databases, and load balancers with appropriate inbound and outbound rules.

3. **Provision EC2 Instances**:
   - Launch EC2 instances within the private subnets. Ensure they are part of the Auto Scaling Group.

4. **Set Up Load Balancer**:
   - Create an Application Load Balancer in the public subnet and configure listeners for HTTP/HTTPS traffic.
   - Point the Load Balancer to the EC2 instances in the private subnet.

5. **Set Up RDS**:
   - Launch an RDS instance with Multi-AZ deployment in the private subnet for database management.
   - Configure a standby instance for high availability.

6. **Set Up Route 53**:
   - Create a hosted zone in Route 53 and configure DNS routing to the load balancer's domain name.

7. **Set Up SSL/TLS with Certificate Manager**:
   - Use AWS Certificate Manager to request and manage SSL certificates.
   - Attach the SSL certificate to the load balancer for HTTPS traffic.

### Monitoring and Management

- **CloudWatch**: Use Amazon CloudWatch to monitor EC2 instance health, Auto Scaling activities, and RDS performance.
- **CloudTrail**: Enable AWS CloudTrail for auditing user actions and resource changes in your AWS environment.
