# High Level Services

## AWS Global Infrastructure
The global infrastructure is the regions around the world in which AWS is based, on top on Global infrastructre site top level services
- Compute
- Storage
- Databases 
- Migration and Transfer
- Network & Content Delivery
- Developer Tools
- Robotics
- Blockchain
- Satellite
- Management & Governance
- Media Services
- Machine Learning
- Analytics
- Security, Identity & Compliance
- Mobile
- AR & VR 
- Application Integration
- AWS Cost Management
- Customer Engagement
- Business Applications
- Desktop & App Streaming
- IOT
- Game Development

![[firefox_ks5Lb8sFzf.png]]

There are:
- 19 regions
	- A geographical area, consists of 2 or more availability zones
- 57 availability zones
	- Think of this as a datacenter, or several datacenters
![[firefox_ZJoI2mrv7l.png]]

- Edge locations are endpoints for AWS which are used for caching content. Typically this consists of CloudFront, Amazon's Content Delivery Network (CDN)
	- There are more edge locations than regions, currently over 150 edge locations 

High level services we need for Solutions Architecture Associate

![[firefox_qZm9TcREaJ.png]]

And these are the core services we need

![[firefox_wTZ3XTKz7i.png]]

## Short Quiz
1. The VPC service is a member of which group of AWS services in the 'All services' view of the AWS Portal?
	- A Virtual Private Cloud (VPC) is a virtual network dedicated to a single AWS account. It is logically isolated from other virtual networks in the AWS cloud. VPC is found in the "Networking & Content Delivery" section of the AWS Portal.
2. What does an AWS Region consist of?
	- AWS has the concept of a Region, which is a physical location around the world where data centers are clustered. Each group of logical data centers is called an Availability Zone. Each AWS Region consists of multiple, isolated, and physically separate AZ's within a geographic area. Reference: [Regions and Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/ "null").
3. Which of the below are database services from AWS?
	- DynamoDB is a fast and flexible non-relational database service for any scale. DynamoDB enables customers to offload the administrative burdens of operating and scaling distributed databases to AWS so that they don’t have to worry about hardware provisioning, setup and configuration, throughput capacity planning, replication, software patching, or cluster scaling.
	- Amazon Relational Database Service (Amazon RDS) is a managed service that makes it easy to set up, operate, and scale a relational database in the cloud. Amazon RDS gives you access to the capabilities of a familiar MySQL, MariaDB, Oracle, SQL Server, or PostgreSQL database.
4. Considering the AWS Global Cloud Infrastructure, which of the following is correct in terms of numbers (greater to lesser)?
	- The number of Edge Locations is greater than the number of Availability Zones, which is greater than the number of Regions. Reference: [Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/ "null").
5. Which of the below are factors that have helped make public cloud so powerful?
	- Public cloud allows organizations to try out new ideas, new approaches and experiment with little upfront commitment. If it doesn't work out, organizations have the ability to terminate the resources and stop paying for them.
6. Which statement best describes Availability Zones?
	- An Availability Zone (AZ) is a distinct location within an AWS Region. Each Region comprises at least two AZs.
7. In which of the following is CloudFront content cached?
	- CloudFront content is cached in Edge Locations.
8. Which of the below are storage services in AWS?
	- S3 and EFS both provide the ability to store files in the cloud. EC2 provides compute, and is often augmented with other storage services. VPC is a networking service.
9. Which of the below are AWS compute services?
	-	Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides resizable compute capacity in the cloud. It is designed to make web-scale computing easier for developers.
	- AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.
10. What is an AWS region?
	- A region is a geographical area divided into Availability Zones. Each region contains at least two Availability Zones.
11. Which of the following are a part of AWS’ Networking and Content Delivery services?
	- VPC allows you to provision a logically isolated section of the AWS where you can launch AWS resources in a virtual network. CloudFront is a fast, highly secure and programmable content delivery network (CDN). EC2 provides compute resources while RDS is Amazon's Relational Database System
12. What is an Amazon VPC?
	- VPC stands for Virtual Private Cloud.