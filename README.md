# Hosting-vprofile-app-using-AWS-PAAS-and-SAAS
This project configures and hosts the entire technology stack of the application in the form of an artifact on AWS using Elastic Beanstalk service and PAAS and SAAS services

## Overview
In this project I have hosted the entire stack of the vprofile project and hosted it on AWS for production workload. If we look at the scenario there are lot of services running in tandem so that the service properly delivered to the customer. Managing these services requires huge expenditure, time and resources. To tackle this problem we can host the entire technology stack on AWS using its Paas and SaaS capabilities. We pay as we go, automation is easy. As the entire stack will be managed by AWS human intervention will be minimal and operational expenditure will be minimum.

## Services Used
- AmazonMQ
- Amazon RDS
- Amazon Elasticache
- Elastic Beanstalk
- AWS Cloudfront
- Route 53
- AWS S3

## Project Architecture
![Alt text](<project architecture-1.png>)

The following things take place after the user enters the URL:
- The domain is configured using Route 53 and is routed to a Cloudfront(CDN) distribution.
- The request is then routed to the Elastic Beanstalk Security group which has two main functions:
    - Forward the request through to the application load balancer.
    - Host the application on the ec2 instances.
- Elastic Beanstalk (EB) uploads the artifact into the S3 bucket and hosts the application from the bucket.
- The EB  security group is connected with the backend security group to allow visiting traffic to the backend services.
- Backend service group includes services like:
    - AmazonMQ service
    - Amazon Relational Database Service (RDS)
    - Amazon Elasticache 

## Implementation Details
### Login to your AWS account
Access the management console and log into it.

### Create security group for backend services
Initially just create a rule allowing SSH access from your IP and create the group.
![Alt text](initial_backend_sg.png)

### Create Backend Services
- Create RDS instance
    - Search for Amazon RDS on the console.
    - Go to RDS and create an instance.
    - Choose standard create and select MySQL as database engine.
    ![Alt text](RDS_1.png)
    - Choose the required version for the project (MySQL 5.7 is required for my project).
    ![Alt text](image-1.png)
    - Give your database a name and provide credentials.
    ![Alt text](image-2.png)