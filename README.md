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
![Alt text](image-5.png)

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
![Alt text](image-4.png)

### Create Backend Services
- Create RDS instance
    - Search for Amazon RDS on the console.
    - Go to RDS and create an instance.
    - Choose standard create and select MySQL as database engine.
    ![Alt text](image-3.png)
    - Choose the required version for the project (MySQL 5.7 is required for my project).
    ![Alt text](image-1.png)
    - Give your database a name and provide credentials.
    ![Alt text](image-2.png)
    - Specify allocated storage.
    ![Alt text](image-6.png)
    - under Additional configuration -> Database options provide the database name you want to create, otherwise no database will be created.
    ![Alt text](image-7.png)
    - Store the database credentials by clicking on the view connection details button after instance is created.
    ![Alt text](image-11.png)

- Create and configure message broker (RabbbitMQ)
    - Search for AmazonMQ on the console.
    - Go to AmazonMQ and clcik on create a broker.
        - Select broker engine.
        ![Alt text](image-8.png)
        - Select deployment mode.
        ![Alt text](image-9.png)
        - Configure the broker settings. 
        ![Alt text](image-10.png)
        - Review and create the broker.

- Configure and create cache service (Memcached Cluster)
    - Search for Elasticache on the console.
    - Go to Elasticache console.
    - Create a parameter group:
        - On the left-top hamburger menu select parameter groups.
        - Click on create parameter group.
        - Provide group name, description and family and create.
        ![Alt text](image-12.png)
    - Create a subnet group:
        - On the left-top hamburger menu select subnet groups.
        - Click on create subnet group.
        - Provide group name, description and VPC then create.
        ![Alt text](image-13.png)
    - Create memcached cluster:
        - On the left-top hamburger menu select memcached cluster.
        - Click on create cluster.
        - Configure cluster settings.
        ![Alt text](image-14.png)
        - provide memory as t2.micro to avail free tier services.
        ![Alt text](image-15.png)
        - Choose the subnet group you created.
        ![Alt text](image-16.png)
        - Advanced settings.
        ![Alt text](image-17.png)
        