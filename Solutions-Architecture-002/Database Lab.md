# Deploying an Amazon RDS Multi-AZ and Read Replica

## Introduction

Account ID: 610252748050
![[firefox_W5IuzuNYJF.png]]

![[firefox_3VBJBJ7Ity.png]]

![[firefox_jRKyidwYzG.png]]

In this hands-on lab, we will work with Relational Database Service (RDS). This lab will provide you with hands-on experience with:

-   Enabling Multi-AZ and backups
-   Creating a read replica
-   Promoting a read replica
-   Updating the RDS endpoint in Route 53

Multi-AZ is strictly for failover, as the standby instances cannot be read from by an application. Read replicas are used for improved performance and migrations. With read replicas, you can write to the primary database and read from the read replica. Because a read replica can be promoted to be the primary database, it makes for a great tool in disaster recovery and migrations.

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Enable Multi-AZ Deployment

1.  Navigate to **EC2** > **Load Balancers**.
2.  Copy the DNS name of the load balancer.
3.  Open a new browser tab, and enter the DNS name.
    -   We will use this web page to test failovers and promotions in this lab.
4.  Back in the AWS console, navigate to **RDS** > **Databases**.
5.  Click on our database instance.
6.  Click **Modify**.
7.  Under _Multi-AZ deployment_, click **Create a standby instance (recommended for production usage)**.
8.  Click **continue** at the bottom of the page.
9.  Under _Scheduling of modifications_, select **Apply immediately**, and then click **Modify DB Instance**.
10.  Once the instance shows Multi-AZ status as **Available** (it could take about 10 minutes), select the database instance.
11.  Click **Actions** > **Reboot**.
12.  On the reboot page, select **Reboot With Failover?**, and click **Confirm**.

### Create a Read Replica

1.  With the database instance still selected, click **Actions** > **Create read replica**.
2.  For _Destination region_, select **US East (N. Virginia)**.
3.  In the _Settings_ section, under _DB instance identifier_, enter "wordpress-rr".
4.  Leave the other defaults, and click **Create read replica**. It will take a few minutes for it to become available.
5.  Refresh the web page we navigated to earlier to see if our application is still there. It should be fine.

### Promote the Read Replica and Change the CNAME Records Set in Route 53 to the New Endpoint

1.  Once the read replica is available, check the circle next to it.
2.  Click **Actions** > **Promote**.
3.  Leave the defaults, and click **Continue**, and then click **Promote Read Replica**.
4.  Use the web page to monitor for downtime.
5.  Once the read replica is available, click to view it.
6.  In the _Connectivity & security_ section, copy the endpoint.
7.  Navigate to **Route 53** > **Hosted zones**.
8.  Select the private hosted zone.
9.  Select the **db.mydomain.local** record.
10.  Click **Edit**.
11.  Leave the _Record name_ as **db**.
12.  Replace what's currently in the _Value_ box with the endpoint you copied.
13.  Click **Save changes**.
14.  Monitor using the web page for downtime. (There shouldn't be any.)


# Database Quiz 
1. AWS's NoSQL product offering is known as **\_\_\_\_**.
	- DynamoDB
2. Which set of RDS database engines is currently available?
	- Amazon Aurora, MySQL, MariaDB, Oracle, SQL Server, and PostgreSQL
		- Amazon RDS supports Amazon Aurora, MySQL, MariaDB, Oracle, SQL Server, and PostgreSQL database engines.
3. If you want your application to check RDS for an error, have it look for an **\_\_** node in the response from the Amazon RDS API.
	- Error
		- Typically, you want your application to check whether a request generated an error before you spend any time processing results. The easiest way to find out if an error occurred is to look for an `Error node` in the response from the Amazon RDS API. Reference: [Troubleshooting applications on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/APITroubleshooting.html "null").
4. Amazon's ElastiCache uses which two engines?
	- Redis & Memcached
5. Which of the following data formats does Amazon Athena support?
	- JSON, Apache Parquet, Apache ORC
		- Amazon Athena is an interactive query service that makes it easy to analyse data in Amazon S3, using standard SQL commands. It will work with a number of data formats including "JSON", "Apache Parquet", "Apache ORC" amongst others, but "XML" is not a format that is supported.
6. MySQL installations default to port number **\_\_\_\_**.
	- 3306
		- The default endpoint port for MySQL installations is 3306.
7. Which AWS Database service is the most suitable for OLTP workloads?
	- RDS
		- Amazon RDS with provisioned IOPS delivers fast, predictable, and consistent I/O performance that is great for OLTP database workloads.
8. You can SSH into and control the operating system where your Amazon RDS MySQL instance is running.
	- False
		- You cannot SSH into or control an RDS operating system. Only DB Instances deployed within a VPC can be accessed by EC2 Instances deployed in the same VPC.
9. What data transfer charge is incurred when replicating data between Availability Zones for your Amazon RDS MySQL in a Multi-AZ deployment?
	- There is no charge associated with this action.
		- Data transferred between Availability Zones for replication of Multi-AZ deployments is free. Reference: [Amazon RDS for MySQL Pricing: Data Transfer](https://aws.amazon.com/rds/mysql/pricing/ "null").
10. In RDS, when are changes to the backup window implemented?
	- During the next scheduled maintenance window or immediately
		- When applying changes to the backup window, you can choose either to have the changes done during the next scheduled maintenance window or immediately. The schedule itself is modified right away.
11. Which AWS service is ideal for Business Intelligence Tools/Data Warehousing?
	- Redshift
12. Under what circumstances would I choose provisioned IOPS over standard storage when creating an RDS instance?
	- If you use online transaction processing in your production environment.
		- Provisioned IOPS becomes important when you are running production environments requiring rapid responses, such as those which run e-commerce websites. Without high performant responses from an RDS instance page loads of the website could suffer resulting in loss of business. If your workloads are not latency sensitive or you are running a test environment the additional cost of provisioned IOPS will not be cost beneficial to your project.
13. RDS Reserved instances are available for multi-AZ deployments.
	- True
		- Reserved DB instance benefits apply for both Multi-AZ and Single-AZ configurations. Reference: [Reserved DB instances for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithReservedDBInstances.html "null").
14. You are hosting a MySQL database on the root volume of an EC2 instance. The database is using a large number of IOPS, and you need to increase the number of IOPS available to it. What should you do?
	- Add 2 additional EBS SSD volumes and create a RAID 0 volume to host the database.
		- RAID 0 provides performance improvements compared with a single volume as data can be read and written to multiple disks simultaneously. 2 disks, each with a bandwidth of 4,000 IOPS will provide a combined bandwidth of 8,000 IOPS.
15. If I wanted to run a database on an EC2 instance, which of the following storage options would Amazon recommend?
	- EBS
		- Elastic Block Storage (EBS) is recommended block level storage for EC2 instances if you were running a database on an EC2 instance. If the question didn't focus on the solution being on the instance, RDS would be the preferred choice.
16. In RDS, what is the maximum value I can set for my backup retention period?
	- 35 days
17. What happens to the I/O operations of a single-AZ RDS instance during a database snapshot or backup?
	- I/O may be briefly suspended while the backup process initializes (typically under a few seconds), and you may experience a brief period of elevated latency.
18. When creating a single-AZ Amazon RDS instance, you can select the Availability Zone into which you deploy it.
	- True
		- When you create a DB instance, you can choose an Availability Zone or have AWS choose one for you. An Availability Zone is represented by an AWS Region code followed by a letter identifier (for example, us-east-1a). Reference: [RDS - Regions, Availability Zones, and Local Zones](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html "null")
19. Which of the following is most suitable for OLAP?
	- Redshift
		- Redshift would be the most suitable for online analytics processing.
20. Which of the following DynamoDB features are chargeable, when using a single region?
	- Storage of Data, Read and Write Capacity
		- There will always be a charge for provisioning read and write capacity and the storage of data within DynamoDB, therefore these two answers are correct. There is no charge for the transfer of data into DynamoDB, providing you stay within a single region (if you cross regions, you will be charged at both ends of the transfer). There is no charge for local secondary indexes.
21. Which of the following AWS services is a non-relational database?
	- Amazon DynamoDB
		- Amazon DynamoDB is a non-relational database that delivers reliable performance at any scale. It's a fully managed, multi-region, multi-master database that provides consistent single-digit millisecond latency, and offers built-in security, backup and restore, and in-memory caching. Reference: [What Is a Key-Value Database?](https://aws.amazon.com/nosql/key-value/ "null")
22. How many copies of my data does RDS - Aurora store by default?
	- 6
		- Amazon Aurora automatically maintains 6 copies of your data across 3 Availability Zones. Reference: \[[https://aws.amazon.com/rds/aurora/faqs/#Backup\_and\_Restore\]](https://aws.amazon.com/rds/aurora/faqs/#Backup_and_Restore] "null")(Amazon Aurora FAQs).
23. With new RDS DB instances, automated backups are enabled by default?
	- True
24. Which of the following is NOT a feature supported by DynamoDB?
	- Amazon DynamoDB supports MongoDB workloads.
		- This is not a feature supported by DynamoDB. Amazon DocumentDB (with MongoDB compatibility) is a fast, scalable, highly available, and fully managed document database service that supports MongoDB workloads.
25. If you are using Amazon RDS Provisioned IOPS storage with a Microsoft SQL Server database engine, what is the maximum size RDS volume you can have by default?
	- 16TB
		- You can create Amazon RDS for SQL Server database instances with up to 16TB of storage. The 16TB storage limit is available when using the Provisioned IOPS and General Purpose (SSD) storage types. Reference: [Amazon RDS for SQL Server Increases Maximum Database Storage Size to 16TB](https://aws.amazon.com/about-aws/whats-new/2017/08/amazon-rds-for-sql-server-increases-maxiumum-database-storage-size-to-16-tb/#:~:text=Amazon%20RDS%20for%20SQL%20Server%20Increases%20Maximum%20Database%20Storage%20Size%20to%2016TB,-Posted%20On%3A%20Aug&text=You%20can%20now%20create%20Amazon,Purpose%20(SSD)%20storage%20types. "null")
26. DB security groups are used with DB instances that are not in a VPC and on the EC2-Classic platform. When you create a DB security group, you need to specify a destination port number.
	- False
		- You don't need to specify a destination port number when you create DB security group rules. The port number defined for the DB instance is used as the destination port number for all rules defined for the DB security group. Reference: [DB security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html "null")

