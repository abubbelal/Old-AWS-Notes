# Databases 101

#### What is a relational Database? 
Relational databases are what most people are used to. Think of a traditional spreadsheet:
- Database --> File
- Tables
- Columns
- Rows

#### Relational Databases on AWS
- SQL Server
- Oracle
- MySQL Server
- PostgreSQL
- Aurora
- MariaDB

#### RDS has two key features:
- Multi-AZ - For disaster recovery
	- spread database across multiple availability zones
- Read Replicas - For performance
	- Boost database performance

**Multi-AX vs Read Replicas**

![[firefox_4Xngq6IoH0.png]]

![[firefox_6sfOxTnM7a.png]]

![[firefox_lW90wpFHGo.png]]

#### Non Relational Databases are as follows :

- Collection = table
- Document = row
- Key value pairs = fields or volumns

![[firefox_qGIVH5wpSj.png]]

#### What is data warehousing? 

Used for business intelligence. Tools like Cognos, Jaspersoft, SQL Server Reporting Services, Oracle Hyperion, SAP Business Warehouse. 

Used to pull in very large and complex data sets. Usually used by management to do queries on data (such as current performance vs targets etc)

##### OLTP vs OLAP 
Online Transaction Processing (OLTP) differs from Online Analytics Processing (OLAP) in terms of the types of queries you will run. 

Data warehousing databases use different type of architecture both from a database perspective and infrastructure layer.

Amazon's Data Warehouse solution is called **RedShift**

**OLTP Example: **
Order number 2120121
Pulls up a row of data such as 
Name, Date, Address to Deliver to,
Delivery Status etc.

**OLAP Example:**
Net profit for EMEA and Pacific for the digital radio product. Pulls in large numbers of records

Sum of Radios Sold in EMEA
Sum or Radios Sold in Pacific
Unit Cost of Radio in Each Region
Sales Price of each radio
Sales price - unit cost

#### What is ElastiCache?

ElastiCache is a web service that makes it easy to deploy, operate, and scale an in-memory cache in the cloud. The service improves the performance of web applications by allowing you to retrieve information from fast managed, in-memory caches, instead of relying entirely on slower disk-based databases.

ElastiCache supports two open-source in-memory caching engines:

- Memcached
- Redis

### Examp Tips 

**RDS (OLTP)**
- SQL
- MySQL
- PostgreSQL
- Oracle
- Aurora
- MariaDB

DynamoDB (No SQL)
RedShift OLAP

**ElastiCache**
- Memcached
- Redis 

Redshift for business intelligence or data warehousing
Elasticache to speed up performance of existing databases (frequent identical queries).

# Create RDS Instance - Demo 
#InstallingWordpress #CreatingRDS

Amazon Console --> RDS --> create database --> Select Database type, select mysql for this --> can change the DB instance identifies, NOTE: this is not the database name, just an identifier within AWS --> Create credentials for the database root (master) --> Under additional connectivity, VPC security group, create new, provide a name, no preference --> Under additional configuration, provide initial database name, we don't need automated backups --> create database

![[firefox_oPmpbrLai5.png]]

![[firefox_e0uBnu2cU4.png]]

EC2 --> create new instance --> default is fine, but include the following bootstrap script: 
```bash
#!/bin/bash
yum install httpd php php-mysql -y
cd /var/www/html
wget https://wordpress.org/wordpress-5.1.1.tar.gz
tar -xzf wordpress-5.1.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.1.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
service httpd start
chkconfig httpd on
```
--> for security group select the WebDMZ --> Launch 


EC2 --> security groups --> select the security group created in the first part when we made the RDS instance (rds-launch-wizard) --> edit in bound rules --> mysql/aurrora, and for source add the WebDMZ --> save

This allows our web instance to communicate with our rds instance. 

![[firefox_iMRd5Z6gRh.png]]

RDS --> Databases --> select the database --> connectivity --> take a note of the endpoint, which is the dns endpoint for us to connect our rds instance to, we need this to install wordpress

open the EC2 instance in a browser using the public ip address, and configure the wordpress database 

![[firefox_Ue6PMSq7Cp.png]]

if the wordpress fails to write the wp-config.php file:
ssh into ec2 --> change directory to /var/www/html --> create a file wp-config.php --> and paste what the wordpress site provided --> go back to browser and click "run installation" --> configure the website --> install

#### Relational Databases
- RDS runs on virtual machines 
	- can't ssh into rds instance
- you cannot log in to these operating systems however
- Patching of the RDS operating system and DB is Amazon's responsibility
- RDS is NOT serverless
	- Except Amazon Aurora
- Aurora Serverless IS Serverless

# RDS Backups, Multi-AZ, Read Replicas

**There are two different types of backups for RDS**
- Automated Backups
- Database Snapshots

**Automated Backups**

Automated Backups allow you to recover your database to any point in time within a "retention period". The retention period can be between one and 35 days. Automated Backups will take a full daily snapshot and will also store transaction logs throughout the day. When you do a recovery, AWS will first choose the most recent daily backup, and then apply transaction logs relevant to that day. this allows you to do a point in time recovery down to a second, within the retention period. 

Automated backups are enabled by default. The backup data is tored in S3 and you get free storage space equal to the size of your database. So if you have an RDS instance of 10GB, you will get 10GB worth of storage. 

Backups are taken within a defined window. During the backup window, storage I/O may be suspended while your data is being backed up and you may experience elevated latency.

**Database Snapshots**

DB Snapshots are done manually (ie they are user initiated). They are stored even after you delete the original RDS instance, unlike automated backups. 

**Restirubg Backups**

Whenever you restore either an Automatic Backup or a manual Snapshot, the restored version of the database will be a new RDS instance with a new DNS endpoint. 

![[firefox_z7uaODspLq.png]]

**Encryption At Rest**

Encryption at rest is supported for MySQL, Oracle, SQL Server, PostgreSQL, MariaDB & Aurora. Encryption is done using the AWS key management service (KMS) service. Once your RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted, as are its automated backups, read replicas, and snapshots.

**What is Multi-AZ**

![[firefox_Wq6NFUndsE.png]]

Multi-AZ allows you to have an exact copy of your production database in another Availability Zone. AWS handles the replication for you, so when your production database is written to, this write will automatically be synchronized to the stand by database. 

In the event of planned database maintenance, DB Instance failure, or any Availability Zone Failure, Amazon RDS will automatically failover to the standby so that database operations can resume quickly without administrative intervention.

**Multi-AZ is for Disaster Recovery Only**
It is not primarily used for improving performance. For performance improvement, you need Read Replicas.

**Multi-AZ Support**
- SQL Server
- Oracle
- MySQL Server
- PostgreSQL
- MariaDB

#### What is a Read Replica

![[firefox_Lq56zFChsf.png]]

![[firefox_poFHbBHLKs.png]]

![[firefox_Cfm17n6hA9.png]]

Read replicas allow you to have a read-only copy of your production database. This is achieved by using Asynchronous replication from the primary RDS instance to the read replica. You use read replicas primarily for very read-heavy database workloads.

![[firefox_JkXhYOeqAA.png]]

**Read Replicas available for:**
- MySQL Server
- PostgreSQL
- MariaDB
- Oracle
- Aurora
- MSSQL

**Things to know**
- Used for scaling, not for DR
- Must have automatic backups turned on in order to deploy a read replica
- You can have up to 5 read replica copies of any database
- you can have read replicas of read replicas (but watch out for latency)
- Each read replica will have its own DNS end point
- you can have read replicas that have multi-AZ
- you can create read replicas of Multi-AZ source databases
- Read replicas can be promoted to be their own databases. This breaks the replication
- you can have a read replica in a second region

# RDS Backups, Multi-AZ, Read Replicas - Demo 

RDS --> Select the database from earlier --> Modify --> turn on multi-AZ --> continue --> modify 

Now the database is a Multi-AZ database --> Actions --> Reboot: 
you will notice "Reboot with Failover" this will force our available zone to change on reboot, basically changing zone with rebooting; cancel and go back

Now modify the database and turn on backups; this is needed to turn on read replica --> now select the database --> action --> create read replica --> you can change the region --> provide a DB instance identifier --> create read replica 

Now select the replica database created --> actions --> we can promote the replica , and make this another primary database and create replicas of it, --> we don't need this, so just delete the replica

### Examp Tips 

**2 Types of Backups for RDS**
- Automated Backups
- Database Snapshots

**Read Replicas**
- can be multi-az
- used to increase performance
- must have backups turned on
- can be in different regions
- can be mysql, postgresql, mariadb, oracle, aurora, mssql
- can be promoted to master, this will break the read replica

**MultiAZ**
- used for DR
- you can force a failover from one AZ to another by rebooting the RDS instance

Encryption at rest is supported for mysql, oracle, sql server, postgresql, maridb & aurora. Encryption is done using the AWS key Management Service (KMS) service. Once your RDS instance is encrypted,  the data stored at rest in the underlying storage is encrypted, as are its automated backups, read replicas, and snapshots

# DynamoDB 
#### What is DynamoDB? 

Amazon DynamoDB is a fast and flexible NoSQL database service all applications that need consistent, single-digit millisecond latency at any scale. It is a fully managed database and supports both document and key-value data models. Its flexible data model and reliable performance make it a great fit for mobile, web, gaming, ad-tech, IoT, and many other applications.

#### The basics of DynamoDB are as follows:
- Stored on SSD storage
- Spread across 3 geographically distinct data centres
- Eventual Consistent Reads (default)
- Strongly consistent Reads

##### Eventaul Consistent Reads
- consistency across all copies of data is sually reached within a second. Repeating a read after a short time should return the updated data (best read performance)

##### Strongly Consistent Reads 
- A strongly consistent read returns a result that reflects all writes that received a successful response prior to the read

**The basics of DynamoDB**
- Stored on SSD Storage 
- Spread across 3 geographically distinct data centres
- Eventual consistent reads (default)
- strongly consistent reads 

# Advanced DynamoDB 
#### DynamoDB Accelerator (DAX)
- Fully managed, highly available, in-memory cache
- 10X performance improvement
- Reduces request time from milliseconds to microseconds -- even under load
- No need for developers to manage caching logic
- Compatible with DynamoDB API calls

![[firefox_ZFspqt8rVr.png]]

##### Transactions
- Multiple "all-or-nothing" operations
- Financial transactions
- Fulfilling orders
- Two underlying reads or writes -- prepare/commit
- Up to 25 items or 4 MB of data

#### On-Demand Capacity 
- Pay-per-request pricing
- Balance cost and performance
- No minimum capacity
- No charge for read/write -- only storage and backups 
- Pay more per request than with provisioned capacity
- Use for new product launches 

#### On-Demand Backup and Restore
- full backups at any time
- zero impact on table performance or availability 
- Consistent within seconds and retained until deleted
- Operates within same region as the source table

#### Point-in-Time Recovery (PITR)
- Protects against accidental writes or deletes
- Restore to any point in the last 35 days
- incremental backups
- not enabled by default
- lastest restorable: five minutes in the past

### Streams 
- time-ordered sequence of item-level changes in a table
- stored for 24 hours
- inserts, updates and deletes
- Combine with lambda functions for functionality like stored procedures

![[firefox_pY8gvI7LvT.png]]
![[firefox_DamXt4bhxR.png]]

#### Global Tables 
**Managed multi-master, multi0region replication**

- Globally distributed applications
- Based on DynamoDB streams
- Multi-region redundancy for DR or HA (High Availability)
- No application rewrites
- Replication latency under one second 

#### Database Migration Service (DMS) 

![[firefox_enPThOwNAW.png]]

#### Security 
- Encryption at rest using KMS 
- Site-to-site VPN 
- Direct Connect (DX)
- IAM policies and roles
- Fine-grained access
- CloudWatch and CloudTrail
- VPC endpoints

# Redshift

Amazon Redshift is a fast and powerful, fully managed, petabyte-scale data warehouse service in the cloud. Customers can start small fo rjust $0.25 per hour with no commitmets or upfront costs and scale to a petabyte or more for $1000 per terabyte per year, less than a tenth of most other data warehousing solutions

#### OLTP vs OLAP 

**OLAP Example:**
Net profit for EMEA and Pacific for the digital radio product. Pulls in large numbers of records

Sum of Radios Sold in EMEA
Sum or Radios Sold in Pacific
Unit Cost of Radio in Each Region
Sales Price of each radio
Sales price - unit cost

Data warehousing databases use different type of architecture both from a database perspective and infrastructure layer

Amazon's data warehouse solution is called Redshift

**Redshift can be configured as follows:**
- Single Node (160Gb)
- Multi-Node
	- Leader Node (manages client connections and recevies queries)
	- Compute Node (store data and perform queries and computations). Up to 128 Compute Nodes

**Advanded Compressions**

Columnar data stores can be compressed much more than row-based data stores because similar data is tored sequentially on disk. Amazon Redshift employs multiple compression techniques and can often achieve significant compression relative to traditional relational data stores. In addition, Amazon Redshift doesn't require indexes or materialized views, and so uses less space than traditional relational database systems. When loading data into an empty table, Amazon Redshift automatically samples your data and selects the most appropriate compression scheme.

**Massively Parallel Processing (MPP)**
Amazon Redshift automatically distributes data and query load across all nodes. Amazon Redshift makes it easy to add nodes to your data warehouse and enables you to maintain fast query performance as your data warehouse grows. 

**Backups**
- Enabled by default with a 1 day retention period
- Maximum retention period is 35 days
- Redshift always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in Amazon S3)
- Redshift can also asynchronously replicate your snapshots to S3 in another region for disaster recovery

#### redshift Pricing
- Compute Node Hours (total number of hours you run across all your compute nodes for the billing period. You are billed for 1 unit per node per hour, so a 3-node data warehouse cluster running persistently for an entire month would incur 2,160 instance hours. You will not be charged for leader node hours; only compute nodes will incur charges)
- Backup
- Data transfer (only within a VPC, not outside it)

#### Security Considerations
- Encrypted in transit using SSL
- Encrypted at rest using AWS-256 encryption 
- By default Redshift takes care of key management
	- Manage your own keys through HSM 
	- AWS Key management service 

#### Redshift Availability
- Currently only available in 1 AZ
- Can restore snapshots to new AZs in the event of an outage

### Exam Tips 

- Redshift is used for business intelligence
- Available in only 1 AZ

**Backups**
- Enabled by default with a 1 day retention period
- Maximum retention period is 35 days
- Redshift always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in Amazon S3)
- Redshift can also asynchronously replicate your snapshots to S3 in another region for disaster recovery

# Aurora 
#### What is Aurora 
Amazon Aurora is a MySQL and PostgreSQL - compatible relational database engine that combines the speed and availability of high-end commercial databases with the simplicity and cost-effectiveness of open source databases

Amazon Aurora provides up to five times better performance than MySQL and three times better than PostgreSQL databases at a much lower price point, whilst delivering similar performance and availability

**Things to know**
1. Start with 10GB, scales in 10GB increments to 64TB (storage autoscaling)
2. Compute resources can scale up to 32vCPUs and 244GB of memory
3. 2 copies of your data is contained in each availability zone, with minimum of 3 availablility zones. 6 copies of your data

**Scaling Aurora**
- Aurora is designed to transparently handle the loss of up to two copies of data without affecting database write availability and up to three copies without affecting read availability
- Aurora storage is also self-healing. Data blocks and disks are continuously scanned for errors and repaired automatically

**Three types of Aurora Replicas available**
- Aurora Replicas (currently 15)
- MySQL Read Replicas (currently 5)
- PostgreSQL (currently 1)

![[firefox_LThhn2v6Wl.png]]

**Backups with Aurora**
- Automated backups are always enabled on Amazon Aurora DB instances. Backups do not impact database performance
- You can also take snapshots with aurora. This also does not impact on perfromance
- You can share aurora snapshots with other AWS accounts

Amazon Aurora Serverless is an on-demand, autoscaling configuration for the MySQL-compatible and PostgreSQL-compatible editions of Amazon Aurora. An Aurora Serverless DB cluster automatically starts up, shuts down, and scales capacity up or down based on your application's needs

Aurora serverless provides a relatively simple, cost-effective option for infrequent, intermittent or unpredicatable workloads

### Exam Tips 

- 2 copies of your data are contained in each availability zone, with a minimum of 3 availability zones. 6 copies of your data
- you can share aurora snapshots with other aws accounts
- 3 types of replicas available. aurora replicas, MySQL replicas & postgreSQL replicas. Automated failover is only available with Aurora Replicas
- Aurora has automated backups turned on by default. You can also take snapshots with Aurora. You can share these snapshots with other AWS accounts
- Use Aurora serverless if you want a simple, cost-effective option for infrequent, intermittent, or unpredictable workloads

# Elasticache

#### What is Elasticache

Elasticache is a web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud. The service improves the performance of web applications by allows you to retrieve information from fast, managed, in-memory caches, instead of relying entirely on slower disk-based databases

**Supports two open-source in-memory caching enginer**
- Memcached
- Redis 

![[firefox_zn8euSjBjj.png]]

### Exam Tips 
- Use Elasticache to increase database and web application performance
- Redis is multi-AZ
- you can do backups and restores of Redis
- if you need to scale horizontally, use memcached

# Database Migration Service (DMS)

AWS Database Migration Service (DMS) is a cloud service that makes it easy to migrate relational databases, data warehouses, NoSQL databases, and other types of data stores. You can use AWS DMS to migrate your data into the AWS Cloud, between on-premises instances (through an AWS Cloud setup), or between combinations of cloud and on-premises setups

At its most basic level, AWS DMS is a server in the AWS Cloud that runs replication software. You create a source and target connection to tell AWS DMS where toe xtract from and load to. Then you schedule a task that runs on this server to move your data. AWS DMS creates the tables and associated primary keys if they don't exist on the target. You can pre-create the target tables manually, or you can use AWS Schema Conversion Tool (SCT) to create some or all of the target tables, indexes, views, triggers, etc

![[firefox_uVgEkOHr9y.png]]

#### Types of DMS Migrations
**Suports homogenous migrations**
**Also supports heterogenous migrations**

![[firefox_Zj1LOOJ4CC.png]]

**Source and Target Supports**
![[firefox_taneS96X23.png]]

#### DMS in Action 

**Solution Overview of DMS**
homogenous does not need SCT
![[firefox_8xoa0uUaDP.png]]

For heterogenous migration we need to use Schema Conversion Tool 
![[firefox_2Ike5vPUVr.png]]

### Exam Tips 
- DMS allows you to migrate databases from one source to AWS
- The source can either be on-premises, or inside AWS itself or another cloud provider such as Azure
- You can do homogenous migrations (same DB engines) or heterogenous migrations
- If you do a heterogenous migration, you will need the AWS Schema Conversion Tool (SCT)

# Caching Strategies on AWS 
**The following services have caching capabilities**
- CloudFront
- API Gateway
- ElastiCache - Memcached and Redis
- DynamoDB Accelerator (DAX) 

**Typical Architecture**
![[firefox_7IeAk89l7o.png]]

**A more complicated architecture**
![[firefox_cIcKylJk0w.png]]

### Exam Tips 
Caching is a balancing act between up-to-date, accurate information and latency. We can use the following services to cache on AWS:
1. CloudFront
2. API Gateway
3. Elasticache - Memcached and Redis
4. DynamoDB Accelerator (DAX)

# EMR (Elastic MapReduce) Overview 
Amazon EMR is the industry-leading cloud big data platform for processing vast amounts of data using open-source tools as Apache Spark, Apache Hive, Apache HBase, Apache Flink, Apache Hudi, and Presto. With EMR, you can run petabyte-scale analysis at less than half the cost of traditional on-premises solutions and over three times faster standard Apache Spark

The central component of Amazon EMR is the cluster. A cluster is a collection of Amazon Elastic Compute Cloud (Amazon EC2) instances. Each instance in the cluster is called a node. Each node has a role within the cluster, referred to as the node type. 

Amazon EMR also installs different software components on each node type, giving each node a role in a distributed application like Apache Hadoop.

#### Different Node Types
- Master Node:
	- A node that manages the cluster. The master node tracks the status of tasks and monitors the health of the cluster. Every cluster has a master node.
- Core Node:
	- A node with software components that runs tasks and stores data in the Hadoop Distributed File System (HDFS) on your cluster. Multi-node clusters have atleast one core node.
- Task Node:
	- A node with software components that only runs tasks and does not store data in HDFS. Task nodes are optional

![[firefox_Mkib5oZsfz.png]] 

If you lose the master node you lose all your log data

![[firefox_9Yon6vc9KP.png]]

If you have an EMR cluster and you need the logs data on your master node to persist, you need to: 

You can configure a cluster to periodically archive the log files stored on the master node to Amazon S3. This ensures the log files are available after the cluster terminates, whether this is through normal shitdown or due to an error. Amazon EMR archives the log files to Amazon S3 at five-minute intervals. 

This needs to be done when you first create the cluster, cannot be done later

![[firefox_tNG49HTY38.png]]

### Exam Tips 
- EMR is used for big data processing
- Consists of a master node, a core node, and (optionally) a task node
- By default, log data is stored on the master node
- You can configure replication to S3 on five-minute intervals for all log data from the master nodel however, this can only be configured when creating the cluster for the first time

# Database Summary 

**RDS (OLTP)**
- SQL
- MySQL
- PostgreSQL
- Oracle
- Aurora
- MariaDB

DynamoDB (No SQL)
RedShift OLAP

**ElastiCache**
- Memcached
- Redis 

Redshift for business intelligence or data warehousing
Elasticache to speed up performance of existing databases (frequent identical queries).

- RDS runs on virtual machines 
	- can't ssh into rds instance
- you cannot log in to these operating systems however
- Patching of the RDS operating system and DB is Amazon's responsibility
- RDS is NOT serverless
	- Except Amazon Aurora
- Aurora Serverless IS Serverless

**There are two different types of backups for RDS**
- Automated Backups
- Database Snapshots

**Read Replicas**
- can be multi-az
- used to increase performance
- must have backups turned on
- can be in different regions
- can be mysql, postgresql, mariadb, oracle, aurora, mssql
- can be promoted to master, this will break the read replica

**MultiAZ**
- used for DR
- you can force a failover from one AZ to another by rebooting the RDS instance

Encryption at rest is supported for mysql, oracle, sql server, postgresql, maridb & aurora. Encryption is done using the AWS key Management Service (KMS) service. Once your RDS instance is encrypted,  the data stored at rest in the underlying storage is encrypted, as are its automated backups, read replicas, and snapshots

basics of DynamoDB are as follows:
- Stored on SSD storage
- Spread across 3 geographically distinct data centres
- Eventual Consistent Reads (default)
- Strongly consistent Reads

Redshift
- Redshift is used for business intelligence
- Available in only 1 AZ

**Backups**
- Enabled by default with a 1 day retention period
- Maximum retention period is 35 days
- Redshift always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in Amazon S3)
- Redshift can also asynchronously replicate your snapshots to S3 in another region for disaster recovery

Aurora
- 2 copies of your data are contained in each availability zone, with a minimum of 3 availability zones. 6 copies of your data
- you can share aurora snapshots with other aws accounts
- 3 types of replicas available. aurora replicas, MySQL replicas & postgreSQL replicas. Automated failover is only available with Aurora Replicas
- Aurora has automated backups turned on by default. You can also take snapshots with Aurora. You can share these snapshots with other AWS accounts
- Use Aurora serverless if you want a simple, cost-effective option for infrequent, intermittent, or unpredictable workloads

Elasticache

- Use Elasticache to increase database and web application performance
- Redis is multi-AZ
- you can do backups and restores of Redis
- if you need to scale horizontally, use memcached