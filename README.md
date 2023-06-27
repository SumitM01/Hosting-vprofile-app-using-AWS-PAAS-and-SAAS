# Hosting-vprofile-app-using-AWS-PAAS-and-SAAS
This project configures and hosts the entire technology stack of the application in the form of an artifact on AWS using Elastic Beanstalk service and PAAS and SAAS services

## Overview
In this project I have hosted the entire stack of the vprofile project on AWS for production workload. If we look at the scenario there are lot of services running in tandem so that the service properly delivered to the customer. Managing these services requires huge expenditure, time and resources. To tackle this problem we can host the entire technology stack on AWS using its Paas and SaaS capabilities. We pay as we go, automation is easy. As the entire stack will be managed by AWS human intervention will be minimal and operational expenditure will be minimum.

## Services Used
- AmazonMQ
- Amazon RDS
- Amazon Elasticache
- Elastic Beanstalk
- AWS Cloudfront
- Route 53
- AWS S3

## Project Architecture
![project architecture](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/2684851a-e707-4fbf-ba4e-c8dc5e8cd8c2)

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
![initial_backend_sg](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/6d25c864-bc5a-4fbd-9ab6-aa07bc113065)


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
   ![RDS_1](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/783e7acc-6e9a-45d8-bc2f-05955697c89d)

    - Choose the required version for the project (MySQL 5.7 is required for my project).
   ![RDS_2](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/9b6bcc54-81c4-4098-b054-ff103d3fc2a9)

    - Give your database a name and provide credentials.
    ![RDS_4](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/2e757163-11f5-4c10-82ef-2bfad25354ce)

    - Specify allocated storage.
   ![RDS_5](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/445469c8-17d3-46f4-880b-7f114ae28809)

    - under Additional configuration -> Database options provide the database name you want to create, otherwise no database will be created.
    ![RDS_6](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/5c263a12-fd5d-4254-9125-8cc160154233)

    - Store the database credentials by clicking on the view connection details button after instance is created.
   ![RDS_7](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/7221ce31-8da4-4d76-84a3-a8c80a983c80)


- Create and configure message broker (RabbbitMQ)
    - Search for AmazonMQ on the console.
    - Go to AmazonMQ and clcik on create a broker.
        - Select broker engine.
       ![rmq_1](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/74194933-7d78-4046-b297-2a314096fa6c)

        - Select deployment mode.
![rmq_2](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/5d1f122e-ed21-4ecd-ac96-cb0f3d959ef3)
        - Configure the broker settings. 
![rmq_3](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/1f6ef158-bef4-4840-b2b4-c7e9bf58e60b)
        - Review and create the broker.

- Configure and create cache service (Memcached Cluster)
    - Search for Elasticache on the console.
    - Go to Elasticache console.
    - Create a parameter group:
        - On the left-top hamburger menu select parameter groups.
        - Click on create parameter group.
        - Provide group name, description and family and create.
![ec-para-grp](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/e7677b4a-0b7b-4729-95e5-8a6bc26f31e9)
    - Create a subnet group:
        - On the left-top hamburger menu select subnet groups.
        - Click on create subnet group.
        - Provide group name, description and VPC then create.
![ec-sub-grp](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/3800f1eb-4aec-4fa7-b78e-439f62359680)
    - Create memcached cluster:
        - On the left-top hamburger menu select memcached cluster.
        - Click on create cluster.
        - Configure cluster settings.
![ec_memcache_1](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/079b9d19-3af7-46bd-beb2-2c8e0964331b)
        - provide memory as t2.micro to avail free tier services.
![ec_memcache_2](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/9040ea0a-cfca-46a3-a722-62004f51ac9e)
        - Choose the subnet group you created.
![ec_memcache_3](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/e99fae95-9f3a-465b-9444-2fa8c3b34ffd)
        - Advanced settings.
![ec_memcache_4](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/da3b1691-90a5-4d10-9d61-475a3233ef5b)

### Create Elastic Beanstalk Environment
- Search for Elastic Beanstalk on the console.
- Go to Elastic Beanstalk and click on Create application.
- Configure the environment:
    - Provide the application name.
![eb_1](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/764486e9-bdae-4195-ad2f-0265047a64ec)
    - Provide environment name and check and specify domain.
![eb_2](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/c0a365ab-cbe0-4a57-a971-acbe9ec3054b)
    - Select the application platform and its version.
![eb_3](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/2bde87f2-9e4c-4da2-ac2a-35daaf6db123)
    - Choose application code, presets and click on next.
![eb_4](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/63dd84e5-83ee-4599-a94f-22be2c785c37)

- Configure Service Access:
    - Create a new service role, provide the created ec2 key-pair and the created IAM user from earlier.
![eb_5](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/fbf47887-f6ec-41f3-8291-7be8ea32a846)
    - Check the activated option under instance settings.
![eb_6](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/f51bb501-8540-400a-854a-8d520b689f9c)

- Setup networking, database and tags:
    - Leave it as it is.

- Configure instance traffic and scaling:
    - Provide details for instance configuration.
![eb_7](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/18d11c6e-eefa-485e-9efd-29855547a3ea)
    - Specify necessary configuration for instance scaling.
![eb_8](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/ec356440-7138-4527-91fd-9ad4a8ecfd18)
    - Choose the instance type.
![eb_9](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/a270f844-cdc0-43d3-ba2c-7c13c4d0fb93)

- Configure updates, monitoring and rolling:
    - Select rolling under deployment policy of application deployments and specify the percentage or fixed number.
![eb_10](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/e78ab150-c5df-4cc5-9bd2-6c6941ebca44)
    
- Review and create application.
![eb_12](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/e753447f-9a95-41c2-b701-1f3a6fd4e4d9)

### Database initialization
- Launch an ec2 instance by creating a separate security group with inbound rule as ssh allowed from your IP and userdata as specified below.
![mysql-client-user-data](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/27b6456c-3a08-4927-bcd0-c752badf9915)

![eb_11](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/70327000-2dc9-4361-a729-0f633851a057)

- Connect to the instance using ssh and become the root user.
- Run the following command to connect to mysql-client for validation.
![connect_mysql](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/f05fd1ef-006c-42cb-bddc-9ada46eb915a)
- Clone to the specified repository and insert the database file into the mysql database using the displayed commands.
![insert_db](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/6551c912-c47b-4518-a5e5-e9107551b300)
- Validate the database insertion by reconnecting to the client and accessing the database and tables.
![validate_db](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/3a14f414-be29-48bf-8a05-0755eba74e39)

### Elastic Beanstalk and Security group modification
- Go to configuration under elastic beanstalk environment.
- Modify the following under 'Configure instance traffic and scaling':
    - Add a custom 443 HTTPS listener with a validated certificate through route 53. (Mine was already verified)
![eb_13](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/8091a8cc-8224-41e0-bd03-203104ec247d)
    - Change healthcheck path to /login.
![eb_14](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/c57d1d77-989c-4664-aa04-0d4039fbeec9)
    - Enable stickiness under sessions.
![eb_15](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/9df0fe10-8e14-4cba-a8f1-5d948f57ded3)
- Click on update and wait for the environment to be fully updated.
![eb_16](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/f302fa39-7cb8-4bf8-bc40-a0033a30a69d)
- Modify 'vprofile-backend-sg' and add the following inbound rules.
![modify_backend_sg](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/a3faa079-dfb8-494a-a5c3-a3601818bc4a)

### Build and deploy artifact
- Checkout to the following repository branch 'aws-Refactor'
- Edit application.prperties file present in /src/main/resources folder.
![update_applicationprop_file](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/33f8a059-318a-4192-99c1-e846543a483a)
- Run the following command to build the source code and generate the artifact.
```bash
mvn install
```
- In the AWS console, under Elastic Beanstalk page click on upload and deploy.
- Choose the artifact present as 'vprofile-v2.war' under target directory, give a version and deploy.
- Wait for the application to be fully deployed.

### Create a record under Route 53
- Under the hosted zone of your domain create a new cname record which points to the elastic beanstalk load balancer's endpoint.
![add_route53_rec](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/670836e5-d95a-45df-930f-ebbb7872f402)

### Configure and create Cloudfront Distribution
- In the console search for AWS Cloudfront.
- Click on create distribution.
    - Specify origin domain, protocol.
![cdn_1](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/ef6ea742-9d9f-4528-b059-47715a99aaa0)
    - Select caching policies.
![cdn_2](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/313ef39c-d75b-431b-a921-5bd61170e529)
    - Provide price class and custom SSL certificate.
![cdn_3](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/a391ed93-25d4-477d-9aab-ad11da0622e3)
    - Choose WAF policy and create distribution.
![cdn_4](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/82dd002c-aac7-45ac-ba70-17241375e83b)
    - Wait for the distribution to be enabled all over the specifed zones.
![cdn_5](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/078e79d2-3e60-4647-9f07-1b670a637976)

### Access the application
- Type the custom domain of your application to access it.
![app_deploy_success](https://github.com/SumitM01/Hosting-vprofile-app-using-AWS-PAAS-and-SAAS/assets/65524561/064c06fa-f125-4804-b41b-94c78ad53025)

## Conclusion 
As, described in the above steps, I have successfully configured and Refactored the application on AWS using its PaaS and SaaS services. Hope you find it helpful.

## References
- https://github.com/devopshydclub/vprofile-project/tree/aws-Refactor
- https://www.udemy.com/course/decodingdevops
