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
- Create an EC2 key-pair to be used to launch ec2 instances.
- Create an IAM user to be used with Elastic Beanstalk with the following rules:
    - AWSElasticBeanstalkRoleSNS
    - AdministratorAccess-AWSElasticBeanstalk
    - AWSElasticBeanstalkCustomPlatformforEC2Role
    - AWSElasticBeanstalkWebTier
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

### Create Elastic Beanstalk Environment
- Search for Elastic Beanstalk on the console.
- Go to Elastic Beanstalk and click on Create application.
- Configure the environment:
    - Provide the application name.
    ![Alt text](image-18.png)
    - Provide environment name and check and specify domain.
    ![Alt text](image-19.png)
    - Select the application platform and its version.
    ![Alt text](image-20.png)
    - Choose application code, presets and click on next.
    ![Alt text](image-21.png)

- Configure Service Access:
    - Create a new service role, provide the created ec2 key-pair and the created IAM user from earlier.
    ![Alt text](image-22.png)
    - Check the activated option under instance settings.
    ![Alt text](image-23.png)

- Setup networking, database and tags:
    - Leave it as it is.

- Configure instance traffic and scaling:
    - Provide details for instance configuration.
    ![Alt text](image-24.png)
    - Specify necessary configuration for instance scaling.
    ![Alt text](image-25.png)
    - Choose the instance type.
    ![Alt text](image-26.png)

- Configure updates, monitoring and rolling:
    - Select rolling under deployment policy of application deployments and specify the percentage or fixed number.
    ![Alt text](image-27.png)
    
- Review and create application.
![Alt text](image-28.png)

### Database initialization
- Launch an ec2 instance by creating a separate security group with inbound rule as ssh allowed from your IP and userdata as specified below.
![Alt text](image-30.png)
![Alt text](image-29.png)
- Connect to the instance using ssh and become the root user.
- Run the following command to connect to mysql-client for validation.
![Alt text](image-31.png)
- Clone to the specified repository and insert the database file into the mysql database using the displayed commands.
![Alt text](image-32.png)
- Validate the database insertion by reconnecting to the client and accessing the database and tables.
![Alt text](image-33.png)

### Elastic Beanstalk and Security group modification
- Go to configuration under elastic beanstalk environment.
- Modify the following under 'Configure instance traffic and scaling':
    - Add a custom 443 HTTPS listener with a validated certificate through route 53. (Mine was already verified)
    ![Alt text](image-34.png)
    - Change healthcheck path to /login.
    ![Alt text](image-35.png)
    - Enable stickiness under sessions.
    ![Alt text](image-36.png)
- Click on update and wait for the environment to be fully updated.
![Alt text](image-38.png)
- Modify 'vprofile-backend-sg' and add the following inbound rules.
![Alt text](image-37.png)

### Build and deploy artifact
- Checkout to the following repository branch 'aws-Refactor'
- Edit application.prperties file present in /src/main/resources folder.
![Alt text](image-39.png)
- Run the following command to build the source code and generate the artifact.
```bash
mvn install
```
- In the AWS console, under Elastic Beanstalk page click on upload and deploy.
- Choose the artifact present as 'vprofile-v2.war' under target directory, give a version and deploy.
- Wait for the application to be fully deployed.

### Create a record under Route 53
- Under the hosted zone of your domain create a new cname record which points to the elastic beanstalk load balancer's endpoint.
![Alt text](image-40.png)

### Configure and create Cloudfront Distribution
- In the console search for AWS Cloudfront.
- Click on create distribution.
    - Specify origin domain, protocol.
    ![Alt text](image-41.png)
    - Select caching policies.
    ![Alt text](image-42.png)
    - Provide price class and custom SSL certificate.
    ![Alt text](image-43.png)
    - Choose WAF policy and create distribution.
    ![Alt text](image-44.png)
    - Wait for the distribution to be enabled all over the specifed zones.
    ![Alt text](image-45.png)

### Access the application
- Type the custom domain of your application to access it.
![Alt text](image-46.png)

## Conclusion 
As, described in the above steps, I have successfully configured and Refactored the application on AWS using its PaaS and SaaS services. Hope you find it helpful.

## References
- https://github.com/devopshydclub/vprofile-project/tree/aws-Refactor
- https://www.udemy.com/course/decodingdevops
