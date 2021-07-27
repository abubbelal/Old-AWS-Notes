# Elastic Load Balancers

A physical or virtual device thats designed to help balance the network load across multiple web servers. 

Load balancer types:
1. Application Load Balancer
2. Network Load Balancer
3. Classic Load Balancer

**Application Load Balancers** are best for load balancing of HTTP and HTTPS traffic. They operate at Layer 7 and are application aware. They are intelligent, and you can create advanced routing, sending specified requests to spcific web servers. 

**Network Load Balancer**
are best suited for load balancing of TCP traffic where extremem performance is required. Operating at the connection level (Layer 4), Network Load Balancer are capable ofhandling millions of requests per second, while maintaining ultra-low latencies. Used for extremem performance!

**Classic Load Balancer**
are the legacy Elastic Load Balancers. You can load balance HTTP/HTTPS applications and use Layer 7-specific features, such as X-Forwarded and sticky sessions. You can also use strict Layer 4 load balancing for applications that rely purely on the TCP protocol. 

- If your application stops responding, the ELB (Classic Load Balancer) responds with a 504 error. This means that the application is having issues. This could be either at the Web Server layer or at the Database Layer. Identify where the application is failingm and scale it up or out where possible. 

- 504 --> gateway time out --> this could mean the application is having issues, could be at web server layer or the database layer NOT the load balancer. Find where your application is failing and scale it up where possible

**X-Forwarded-For Header**
![[firefox_JL3elNyn4K.png]]

Allows you have the users ip address in the EC2 even when traffic is coming from elastic load balancer.

### Exam Tips 

3 Different Load balancer types:
1. Application Load Balancer
2. Network Load Balancer
3. Classic Load Balancer

- 504 Error ==> means the gateway has timed out. This means that the application not responding within the idle timeout period.
- Trouble shoot the application. IS it the Web Server or the Database Server?

- If you need the IPv4 address of your end user, look for the X-Forwarded-For header

# Load Balancers and Health Checks - Demo

AWS Console --> EC2 --> Launch 2 different instances in 2 different Availability Zones, use bootstrap script to create a simple output in each web server. 

EC2 Console --> Load Balancer --> Classic Load Balancer --> provide a name -> HTTP port 80 is fine for web server --> default is fine --> 
![[firefox_CQTMqZhPFs.png]]
--> select both EC2 load balancers, enable Cross-Zone load balancing --> Next --> Create 

Load Balancers --> select the load balancer --> Copy the DNS Name --> you can use this to visit the servers instead of their public ip address. 
Now if either of the web servers go down, the load balancer will traffic the route to the other server. 

**Now lets create a different load balancer**
first delete the previous load balancer. 

EC2 --> Target Groups : A target group is basically where your load balance is going to root requests to the targets in that target group and using the target group that you specify and perform health checks. 

Basically allows you to have a group of EC2 instances for US for example, and another group of EC2 instances for EU, etc. 

Target Groups --> provide a name, Instance is fine, HTTP port 80 is fine, path: /index.html, edit the health checks timing --> Create 

Select the Target Group --> Targets --> edit --> Select the two EC2 instances we made --> Save 

Load Balancer --> Application Load Balancer --> provide a name, HTTP is fine, choose all available availability zone --> Next --> Configure Routing --> select use exisitng target group: and choose the one we made in the previous step --> Create

### Exam Tips 

- Instances monitored by ELB are reported as; InService, or OutofService
- Health Checks check the instance health by talking to it
- Load Balances have their own DNS name. You are never given an IP address
- Read the ELB FAQ for Classic Load Balancers


# Advanced Load Balancer Theory 

**Sticky Sessions**
Classic Load Balancer routes each request independently to the registered EC2 instance with the smallest load. 

Sticky sessions allow you to bind a user's session to a specific EC2 instance. This ensures that all requests from the user during the session are sent to the same instance. 

You can enable Sicky Sessions for Application Load Balancers as well, but the traffic will be sent at the Target Group Level. 

**No Cross Zone Load Balancing** 
![[firefox_D7swY41PnE.png]]

**With Cross Zone Load Balancing**
![[firefox_RBG5Xogfg0.png]]

**What are Path Patterns**
You can create a listener with rules to forward requests based on the URL path. This is known as path-based routing. If you are running microservices, you can route traffic to multiple back-end services using path-ased routing. For example, You can route general requests to one target group and requests to render images to another target group.

![[firefox_lbTx8fPA2J.png]]

### Exam Tips 
- Sticky sessions enable your users to stick to the same EC2 instance. Can be useful if you are storing information locally to that instance.
- Cross Zone Load Balancing enables you to load balance across multiple availability zones
- Path patterns allow you to direct traffic to different EC2 instances based on the URL contained in the request

# Auto Scaling 

**3 Components of Auto scaling**

1. Groups
	- Logical component. Webserver group or Application group or Database group etc.
2. Configration Templates
	- Groups uses a launch template or a launch configuration as a configuration template for its EC2 instances. You can specify information such as the AMI ID, instance type, key pair, security groups, and block device mapping for your instances.
3. Exaling Options
	- Scaling Options provides several ways for you to scale your Auto Scaling groups. For example, you can configure a group to scale based on the occurence of spcified conditions (dynamic scaling) or on a schedule

![[firefox_l8LqEI7m1X.png]]

**Scaling Options**

- Maintain current instance levels at all times
- Scale Manually
- Scale based on a schedule
- Scale based on demand
- Use predictive scaling

**Maintain current instance levels at all times**

You can configure your Auto Scaling groups to maintain a specified number of running instances at all times

To maintain the current instance levels, Amazon EC2 Auto Scaling performs a periodic health check on running instances within an Auto Scaling group.

When Amazon EC2 Auto Scaling finds an unhealthy instance, it terminates that instance and launches a new one. 

![[firefox_um8kzXtxQN.png]]

**Scale Manually**

Manual scaling is the most basic way to scale your resources, where you specify only the change in the maximum, minimum or desired capacity of your Auto Scaling group. 

Amazon EC2 Auto scaling manages the process of creating or terminating instances to maintain the updated capacity 

![[firefox_z8Prhjgb9i.png]]

**Scale based on a schedule**

Scaling by a schedule means that scaling actions are performed automatically as a function of time and date

This is useful when you know exactly when to increase or decrease the number of instances in your group, simply because the need arises on a predictable schedule. 

**Scale based on demand**

A more advanced way to scale your resources - using scaling policies - lets you define parameters that control the scaling process. 

For example, let's say that you have a web application that currently runs on two instances and you want the CPU utilization of the Auto Scaling group to stay at around 50% when the load on the application changes. This method is useful for scaling in response to changing conditions, when you don't know when those conditions will change. you can set up Amazon EC2 Auto Scaling to respond for you. 

**Predictive Scaling**

You can also use Amazon EC2 auto scaling in combination with AWs auto scaling to scale resources across multiple services. 

AWS auto scaling can help you maintain optimal availability and performance by combining predictive scaling and dynamic scaling (proactive and reactive approaches, respectively) to scale your Amazon EC2 capacity faster


# Launch Configurations and Auto Scaling Groups - Demo

EC2 --> Auto scaling, under load balancing --> launch configuration, default AMI and instance type --> Configure details, provide a name, you can set a specified IAM role, you can set script in the advanced detail
![[HA Architecture]]
Default storage is fine, and Create --> Create an Auto Scaling group using this launch configuration --> provide group name, enter group size, you can choose specified subnets --> Configure scaling policies, define condition to scale 
![[firefox_Y5ZROTwZrc.png]]

--> Next --> Create Auto Scaling

This will launch the different EC2 instances. If you delete an autoscaling group, it will also delete the EC2 instances beneath it.

# HA Architecture

Everything fails. Everything. So it's best to plan for failure. 
- Always design for failure
- use multiple AZs and multiple regions where ever you can
- know the difference between multiZa and read replicas for RDS
- know the difference between scaling out and scaling up
- Read carefully and always consider the cost element
- know the different S3 storage classes
	- Highly Available
		- Standard S3, Standard S3 Infrequently Accessed
	- Not highly available
		- reduced redundancy storage, S3 single AZ 

# Building Fault-Tolerant - Demo

Diagram of what we need. 

## Wordpress Set up

S3 --> Set up the S3 buckets --> Create --> provide a name (wp-code), set a region --> Create

Create another bucket --> (wp-media) --> Create

CloudFront --> Create Distribution --> Get Started, web distribution: 
origin name ==> wp-media bucket, everything else default --> Create distribution 

VPC --> Security Groups --> WebDMZ,inbound rules with ports HTTP 80, SSH 22 
Another security group for RDS: Inbound rules -> with ports: Mysql/Aurora 3306 open to WebDMZ Security group

RDS --> create database --> mysql, dev/test, provide a name(wp-db), create password, db instance = t2.micro, VPC should point to RDS, name the database under additional configuration (wp-db) --> create database

IAM --> roles --> create --> ec2 --> s3fullaccess --> provide name (S3forWP) --> create role

EC2 --> launch instance --> default is fine, change role to S3forWP, include bootstrap for setting up the web server: install apache, mysql, wordpress etc. 
```bash
#!/bin/bash
yum update -y # update the ec2
yum install httpd php php-mysql -y # install the services
cd /var/www/html 
echo "healthy" > healthy.html # create basic html page
wget https://wordpress.org/wordpress-5.1.1.tar.gz # download latest wordpress
tar -xzf wordpress-5.1.1.tar.gz # unzip the wordpress zip file
cp -r wordpress/* /var/www/html/ # copy the file to apache root
rm -rf wordpress # remove the unzipped wordpress folder
rm -rf wordpress-5.1.1.tar.gz # remove the zipped wordpress folder
chmod -R 755 wp-content  # change permission 
chown -R apache:apache wp-content # change owner
wget https://s3.amazonaws.com/bucketforwordpresslab-donotdelete/htaccess.txt # download htaccess
mv htaccess.txt .htaccess # move the file so its hidden
chkconfig httpd on # make sure the apache server is turned on on restart
service httpd start # start the apacher server
```

--> Next --> default storage is fine, security group, use WebDMZ --> launch 

## Setting up EC2 

EC2 --> select running instance --> SSH into this EC2 through the public ipaddress 
```bash 
ssh ec2-user@[public ip address] -i [securityKEy].pem
```

```bash
cd /var/www/html
cat .htaccess
```

Browser --> public ip address -> should see the worpdress installation  --> enter database name (wp-db), username, password, and for database host; we need the RDS endpoint, 
RDS --> select RDS instance -> copy the RDS endpoint and paste this into the wordpress database host --> Submit 

**If it fails**
copy the code wordpress provides, go to EC2 terminal, and create the file `wp-config.php` and paste the code in this file. --> next 

Site title, username, password etc. 

wordpress/wp-admin --> enter the credentials from previous step 

**Adding resilience**

We want to make it so that everytime someone uploads a file to the site, that file is also stored in S3 for redundancy; and eventually we can force cloudfront to serve those files using the cloudfront distribution rather than the images in the EC2 instance for increased performance.


```bash
aws s3 ls
aws s3 cp --recursive /var/www/html/[path of image] [s3:bucket path media] # copy images to media bucket
aws s3 cp --recursive /var/www/html [s3:code bucket path] # copy entire site to code bucket
```

let apache know we allow rewrites
```bash
cd /etc/httpd
cd conf
# create a backup 
cp httpd.conf httpd-backup.conf
nano httpd.conf
# edit AllowOverride controls to AllowOverride All
# save and exit out
# might need to restart the server
```

rewrite the .htaccess file to get media files from s3 bucket instead

now sync the s3 bucket with the changes in root server folder

```bash
aws s3 sync /var/www/html s3:[code bucket path]
```

**Bucket policy to make files public**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
        ],
      "Resource": [
        "arn:aws:s3:::BUCKET_NAME/*"
        ]
    }
  ]
}
```


**Create Application load balancer**

move the ec2 instance behind the load balancer

EC2 --> load balancer --> create load balancer --> application --> provide a name, internet facing, http 80 is fine, select all availablility zone --> Next --> WebDMZ --> Configure Routing: new target group, provide a name, target: instance, health check, /healthy.html, advanced health check settings: 
![[firefox_QY3Crpq8p5.png]]
--> Next --> register targets, select the Instance  --> Next --> Create 

## CloudFormation
- Is a way of completely scripting your cloud environment
- Quick start is a bunch of CoudFormation templates already built by AWS Solutions Architects allowing you to create complex environments very quickly 

# Elastic Beanstalk

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without worrying about the infrastructure that runs those applications. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring

# HA with Bastion Hosts 

**Scenario 1**
![[firefox_pwusPAMESN.png]]
- more expensive 

**Scenario 2**
![[firefox_u35qJUZq64.png]]
- lower cost
- more down time, while the other bastion is provision in another subnet

### Exam Tips 
- Two hosts in two separate AZ. Use a Network Load Balancer with static IP addresses and health checks to fail over from one host to the other. 
- Can't use an Application Load Balancer, as it is layer 7 and you need to use layer 4
- One host in one AZ behind an Auto Scaling group with health checks and a fixed EIP. If the host fails, the health check will fail and the auto scaling group will provision a new EC2 instance in a separate AZ. You can use a user data script to provision the same EIP to the new host. This is the cheapest option, but its not 100% fault tolerant


# On-Premises Strategies with AWS
**You need to be aware of what high-level AWS services you can use on-premises for:**

- Database Migration Service (DMS) 
- Server Migration Service (SMS)
- AWS Application Discovery Service
- VM Import/Export
- Download Amazon Linux 2 as an ISO 

**Database Migration Service**
- Allows you to move databases to and from AWS
- Might have your DR environment in AWS and your on-premises environment as your primary 
- Works with most popular database technologies, such as Oracle, MySQLm DynamoDB, etc
	- Supports homogeneous migrations
	- and heterogenous migrations

![[firefox_3RTkZwY1A6.png]]

**Server Migration Service**

- Server migration service supports incremental replication of your on-premises servers in to AWS
- Can be used as a backup tool, multi-site strategy (on-premises and off-premises), and a DR tool

**AWS Application Discovery Service**

- AWS application discovery service helps enterprise customers plan migration projects by gathering information about their on-premises data centers
- You install the AWS application discovery agentless connector as a virtual appliance on VMware vCenter
- It will then build a server utilization map and dependency map of your on-premises environment
- The collected data is retained in encrypted format in an AWS application Discovery Service data store. You can export this data as a CSV file and use it to estimate the Total Cost of Ownership (TCO) of running on AWS and to plan your migration to AWS
- This data is also available in AWS Migration Hub, where you can migrate the discovered servers and track their progress as they get migrated to AWS

**VM Import/Export**

- Migrate existing applications in to EC2
- Can be used to create a DR strategy on AWS or use AWS as a second site
- You can also use it to export your AWS VMs to your on-premises data center

**Download Amazon Linux 2 as an ISO**
- Works with all major virtualization providers, such as VMware, Hyper-V, KVM, VirtualBox (Oracle, etc)


# HA Summary 

3 Different Load balancer types:
1. Application Load Balancer
2. Network Load Balancer
3. Classic Load Balancer

504 Error ==> means the gateway has timed out. This means that the application not responding within the idle timeout period.
- Trouble shoot the application. IS it the Web Server or the Database Server?
- If you need the IPv4 address of your end user, look for the X-Forwarded-For header

Load Balancers and Health Checks
- Instances monitored by ELB are reported as; InService, or OutofService
- Health Checks check the instance health by talking to it
- Load Balances have their own DNS name. You are never given an IP address
- Read the ELB FAQ for All Load Balancers

Advanced Load Balancer Theory
- Sticky sessions enable your users to stick to the same EC2 instance. Can be useful if you are storing information locally to that instance.
- Cross Zone Load Balancing enables you to load balance across multiple availability zones
- Path patterns allow you to direct traffic to different EC2 instances based on the URL contained in the request

CloudFormation
- Is a way of completely scripting your cloud environment
- Quick start is a bunch of CoudFormation templates already built by AWS Solutions Architects allowing you to create complex environments very quickly 

Elastic Beanstalk:

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without worrying about the infrastructure that runs those applications. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring

Bastions in Action

- Two hosts in two separate AZ. Use a Network Load Balancer with static IP addresses and health checks to fail over from one host to the other. 
- Can't use an Application Load Balancer, as it is layer 7 and you need to use layer 4
- One host in one AZ behind an Auto Scaling group with health checks and a fixed EIP. If the host fails, the health check will fail and the auto scaling group will provision a new EC2 instance in a separate AZ. You can use a user data script to provision the same EIP to the new host. This is the cheapest option, but its not 100% fault tolerant

**You need to be aware of what high-level AWS services you can use on-premises for:**

- Database Migration Service (DMS) 
- Server Migration Service (SMS)
- AWS Application Discovery Service
- VM Import/Export
- Download Amazon Linux 2 as an ISO 


---

# CloudFormation - Lab

![[firefox_pnL9Gf71ZL.png]]


```json
//createstack.json
{
  "Resources": {
    "catpics": {
      "Type": "AWS::S3::Bucket"
    }
  }
}
```

```json
//templatestructure.json
{
  "AWSTemplateFormatVersion" : "2010-09-09",
  "Description" : "this template does XXXX", //this must come after AWSTemplateFormatVersion if it exists
  "Metadata" : {  // multi purpose section, also optional
  },
  "Parameters" : { //define information you want the template to ask for, optional
  },
  "Mappings" : { // data that could be used contionally, 
  },
  "Conditions" : { // control whether certain resources are created or assigned based on a value
  },
  "Transform" : { // used for serverless components, optional
  },
  "Resources" : { //only required part of any template, need to specify what resources are being used
  },
  "Outputs" : { //optional, allows you to return a value, once the stack is complete
  }
}
```

```yaml
# templatestructure.yaml
---
AWSTemplateFormatVersion: "2010-09-09"
Description:
  this template does XXXX
Metadata:
  template metadata
Parameters:
  set of parameters
Mappings:
  set of mappings
Conditions:
  set of conditions
Transform:
  set of transforms
Resources:
  set of resources
Outputs:
  set of outputs
```

```json
//updatestack1.json
{
  "Resources": {
    "catpics": {
      "Type": "AWS::S3::Bucket"
    },
    "dogpics": {
      "Type": "AWS::S3::Bucket"
    }
  }
}
```

```json
//updatestack2.json
{
  "Resources": {
    "catpics": {
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "BucketName": "catsareawesomeraondomvlaue"
      }
    },
    "dogpics": {
      "Type": "AWS::S3::Bucket"
    }
  }
}
```

## Introduction

CloudFormation is a powerful automation service within AWS. It can be used to create simple or complex sets of infrastructure any number of times. This hands-on lab provides a gentle introduction to CloudFormation, using it to create and update a number of S3 buckets. By the end of this hands-on lab, you will be comfortable using CloudFormation and can begin experimenting with your own templates.

## Solution

Log in to the live AWS environment using the credentials provided, and make sure you are in the `us-east-1` (N. Virginia) region.

The CloudFormation templates and other hands-on lab files can be found [here](https://github.com/ACloudGuru-Resources/Course-Certified-Solutions-Architect-Associate/tree/master/labs/getting_started_with_cfn). You can navigate to the needed file and choose the `RAW` view in GitHub. You can save them from there by using the **Save As...** functionality in your browser.

A list of AWS resources and what happens when updates occur can be found [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html).

### Create a CloudFormation Stack

![[firefox_JjqZNNqsAp.png]]


1.  Download the [**createstack.json** file](https://raw.githubusercontent.com/linuxacademy/content-aws-csa2019/master/lab_files/01_aws_sa_fundamentals/getting_started_with_cfn/createstack.json) by right-clicking and selecting **Save As** functionality.
2.  In the AWS console, navigate to CloudFormation.
3.  Click **Create stack** > **With new resources (standard)**.
4.  Select **Template is ready**.
5.  Select **Upload a template file**.
6.  Click **Choose file**.
7.  Locate `createstack.json` in the AWS browser window that popped up.
8.  Select and upload it, and click **Next**.
9.  Name the stack "cfnlab".
10.  Click **Next** > **Next**.
11.  Accept the remaining defaults, and click **Create stack**.
12.  Refresh the page to watch the progress.
13.  Navigate to S3. We didn't specify a name in the `json` file for this bucket, so AWS names it with the **<STACK\_NAME>-<LOGICAL\_VOLUME\_NAME>-<RANDOM\_STRING>** format. Yours will be: _cfnlab-catpics-<RANDOM\_STRING>_.

### Update the CloudFormation Stack

1.  Save the [**updatestack1.json**](https://raw.githubusercontent.com/linuxacademy/content-aws-csa2019/master/lab_files/01_aws_sa_fundamentals/getting_started_with_cfn/updatestack1.json) and [**updatestack2.json**](https://raw.githubusercontent.com/linuxacademy/content-aws-csa2019/master/lab_files/01_aws_sa_fundamentals/getting_started_with_cfn/updatestack2.json) files like we did for `createstack.json`.

#### Update #1

1.  Navigate to CloudFormation.
2.  Select the `cfnlab` stack, and click **Update**.
3.  Select **Replace current template**.
4.  Select **Upload a template file**.
5.  Click **Choose file**, and select and upload `updatestack1.json`.
6.  Click **Next** > **Next** > **Next**.
7.  Click **Update stack**.
8.  Once it's finished updating, navigate to S3. We should see the new `dogpics` bucket.

#### Remove the Update

1.  Navigate back to CloudFormation.
2.  Select the `cfnlab` stack, and click **Update**.
3.  Select **Replace current template**.
4.  Select **Upload a template file**.
5.  Click **Choose file**, and select and upload `createstack.json` again.
6.  Click **Next** > **Next** > **Next**.
7.  Click **Update stack**.
8.  Once it's finished updating, navigate to S3. We should see the `dogpics` bucket is now gone.

#### Update #2

1.  Open the `updatestack2.json` file.
2.  Change the `123` characters in `catsareawesome123` to something unique (e.g., your birthday and today's date).
3.  Save the file.
4.  In the CloudFormation console, select the `cfnlab` stack, and click **Update**.
5.  Select **Replace current template**.
6.  Select **Upload a template file**.
7.  Click **Choose file**, and select and upload `updatestack2.json`.
8.  Click **Next** > **Next** > **Next**.
9.  Click **Update stack**.
10.  Once it's finished updating, navigate to S3. We should see two changes: The `dogpics` bucket is back, and our bucket name is updated to the new value.

### Add CloudFormation Stacks

#### Create a Stack with `updatestack2.json`

1.  Navigate to CloudFormation.
2.  Click **Create stack** > **With new resources (standard)**.
3.  Select **Template is ready**.
4.  Choose to **Upload a template** and **Choose file**.
5.  Select and upload `updatestack2.json`.
6.  Click **Next**.
7.  Name the stack "cfnlab2".
8.  Click **Next** > **Next**.
9.  Accept the remaining defaults, and click **Create stack**.
10.  Refresh the page to watch the progress.
11.  Note it eventually fails — we can't have another S3 bucket with the same name.

#### Create a Stack with `updatestack1.json`

1.  Navigate to CloudFormation.
2.  Click **Create stack** > **With new resources (standard)**.
3.  Select **Template is ready**.
4.  Choose to **Upload a template** and **Choose file**.
5.  Select and upload `updatestack1.json`.
6.  Click **Next**.
7.  Name the stack "cfnlab3".
8.  Click **Next** > **Next**.
9.  Accept the remaining defaults, and click **Create stack**.
10.  Refresh the page to watch the progress.
11.  Once it's complete, navigate to S3, where we should see two new buckets: `cfnlab3-catpics-<RANDOM_STRING>` and `cfnlab3-dogpics-<RANDOM_STRING>`.

### Delete CloudFormation Stacks

1.  Navigate to CloudFormation.
2.  Select `cfnlab`.
3.  Click **Delete**.
4.  In the dialog, click **Delete stack**.
5.  Click the stack, and then click the **Events** tab to see the resources being deleted.
6.  Select `cfnlab2`.
7.  Click **Delete**.
8.  In the dialog, click **Delete stack**.
9.  Select `cfnlab3`.
10.  Click **Delete**.
11.  In the dialog, click **Delete stack**.
12.  Once it's all done, navigate to S3. We should see all the `cfnlab` buckets are gone, as well as our `catsareawesome` bucket.


# Quiz 

1. Following an unplanned outage, you have been called into a planning meeting. You are asked what can be done to reduce the risk of bad deployments and single point of failure for your AWS resources. Which solutions can be used to mitigate the problem? The options do not necessarily need to work together.
	- Use multiple autoscaling groups and boundaries for a staged or 'canary' deployment process.
	- Use several Target groups or auto scaling groups under each Load Balancers.
	- Use Route 53 with health checks to distribute load across multiple ELBs.
	- Use a Classic Load Balancer to spread the load over several availability zones.
		- Cross-zone load balancing reduces the need to maintain equivalent numbers of instances in each enabled Availability Zone, and improves your application's ability to handle the loss of one or more instances.
			
			Using Route 53 in combination with ELBs is a good pattern to distribute regionally as well as across AZs.

			Although the methods vary, you can place multiple autoscaling or target groups behind ELBs.

			The purpose of a canary deployment is to reduce the risk of deploying a new version that impacts the workload.
2. You work for a manufacturing company that operate a hybrid infrastructure with systems located both in a local data center and in AWS, connected via AWS Direct Connect. Currently, all on-premise servers are backed up to a local NAS, but your CTO wants you to decide on the best way to store copies of these backups in AWS. He has asked you to propose a solution which will provide access to the files within milliseconds should they be needed, but at the same time minimizes cost. As these files will be copies of backups stored on-premise, availability is not as critical as durability. Choose the best option from the following which meets the brief.
	- Copy the files from the NAS to an S3 bucket with the Standard-IA class.
		- S3 Standard-IA provides rapid access to files and is resilient against events that impact an entire Availability Zone, while offering the same 11 9's of durability as all other storage classes. The trade-off is in the availability. It is designed for 99.9% availability over a given year, as opposed to 99.99% that S3 Standard offers. However in this brief as cost is more important than availability, S3 Standard-IA is the logical choice.
3. You manage a high-performance site that collects scientific data using a bespoke protocol over TCP port 1414. The data comes in at high speed and is distributed to an autoscaling group of EC2 compute services spread over three AZs. Which type of AWS Load Balancer would best meet this requirement?
	- Network Load Balancers (NLB)
		- The Network Load Balancer is specifically designed for high performance traffic that is not conventional Web traffic. The Classic LB might also do the job, but would not offer the same performance.
4. When an EC2 instance is being modified to have more RAM, is this considered Scaling Up or Scaling Out?
	- Scaling Up
		- Scaling out is where you have more of the same resource separately working in parallel (visualize services sitting side by side). Scaling Up is where you make it bigger and bigger like an ugly tower with more floors being added after the initial design was finished
5. You work for a major news network in Europe. They have just released a new mobile app that allows users to post their photos of newsworthy events in real-time. Your organization expects this app to grow very quickly, essentially doubling its user base each month. The app uses S3 to store the images, and you are expecting sudden and sizable increases in traffic to S3 when a major news event takes place (as users will be uploading large amounts of content.) You need to keep your storage costs to a minimum, and you are happy to temporarily lose access to up to 0.1% of uploads per year. With these factors in mind, which storage media should you use to keep costs as low as possible?
	- S3 Standard-IA
		- The key drivers here are availability and cost, so an awareness of cost is necessary to answer this. Glacier cannot be considered as it is not intended for direct access. S3 has an availability of 99.99%, S3 Standard-IA has an availability of 99.9% while S3-1Zone-IA only has 99.5%
6. You have a web site with three distinct services (mysite.co/accounts, mysite.co/sales, and mysite.co/support); each hosted by different web server Auto Scaling groups. You need to use advanced routing to send requests to specific web servers, based on configured rules. Which of the following AWS services should you use?
	- Application Load Balancers (ALB)
		- The ALB has functionality to distinguish traffic for different targets (mysite.co/accounts vs. mysite.co/sales vs. mysite.co/support) and distribute traffic based on rules for: target group, condition, and priority.
7. In discussions about Cloud services the words 'Availability', 'Durability', 'Reliability' and 'Resiliency' are often used. Which term is used to refer to the likelihood that you can access a resource or service when you need it?
	- Availability
		- Each word has a specific meaning and your ability to select the correct answer may depend on understanding the difference. Availability can be described as the % of a time period when the service will be able to respond to your request in some fashion
8. Which of the below are not components of Amazon EC2 Auto Scaling?
	- cfn-init
		- cfn-init is not a component of the EC2 Autoscaling service. Instead it is a feature which allows commands to be run, and software installed/configured on EC2 instances when they launch.
9. In discussions about Cloud services the words 'Availability', 'Durability', 'Reliability' and 'Resiliency' are often used. Which term is used to refer to the likelihood that a resource is able to recover from damage or disruption?
	- Resiliency
		- Resiliency is the ability of a workload to recover from infrastructure or service disruptions, dynamically acquire computing resources to meet demand, and mitigate disruptions, such as misconfigurations or transient network issues. Reference: [Resiliency](https://wa.aws.amazon.com/wat.concept.resiliency.en.html "null").
10. In discussions about Cloud services the words 'Availability', 'Durability', 'Reliability' and 'Resiliency' are often used. Which term is used to refer to the likelihood that a resource will continue to exist until you decide to remove it?
	- Durability
		- Each word has a specific meaning and your ability to select a correct answer may depend on understanding the difference. Durability refers to the on-going existence of the object or resource. Note that it does not mean you can access it, only that it continues to exist.
11. Placement Groups can either be of the type 'Cluster', 'Spread', or 'Partition'. Choose options from below which are only specific to Spread Placement Groups.
	- A spread placement group supports a maximum of seven running instances per Availability Zone.
		- A spread placement group supports a maximum of seven running instances per Availability Zone. For example, in a Region with three Availability Zones, you can run a total of 21 instances in the group (seven per zone). If you try to start an eighth instance in the same Availability Zone and in the same spread placement group, the instance will not launch. If you need to have more than seven instances in an Availability Zone, then the recommendation is to use multiple spread placement groups. Using multiple spread placement groups does not provide guarantees about the spread of instances between groups, but it does ensure the spread for each group, thus limiting impact from certain classes of failures. Reference: [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html "null")
12. A product manager walks into your office and advises that the simple single node MySQL RDS instance that has been used for a pilot needs to be upgraded for production. She also advises that they may need to alter the size of the instance once they see how many people use the system during peak periods. The key concern is that there can not be any outages of more than a few seconds during the go-live period. Which of the following might you recommend,
	- Consider replacing it with Aurora before go live.
	- Convert the RDS instance to a multi-AZ implementation.
		- There are two issues to be addressed in this question. Minimizing outages, whether due to required maintenance or unplanned failures. Plus the possibility of needing to scale up or down. Read-replicas can help you with high read loads, but are not intended to be a solution to system outages. Multi-AZ implementations will increase availability because in the event of a instance outage one of the instances in another AZs will pick up the load with minimal delay. Aurora provided the same capability with potentially higher availability and faster response.
13. In discussions about Cloud services the words 'Availability', 'Durability', 'Reliability' and 'Resiliency' are often used. Which term is used to refer to the likelihood that a resource will work as designed?
	- Reliability
		- Each word has a specific meaning and your ability to select a correct answer may depend on understanding the difference. Reliability is closely related to Availability, however a system can be 'Available' but not be working properly. Reliability is the probability that a system will work as designed. This term is not used much in AWS, but is still worth understanding.
14. Regarding the S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA, S3 One Zone-IA, S3 Glacier, and S3 Glacier Deep Archive Amazon S3 storage classes, objects are designed for **\_\_\_\_** durability.
	- 99.999999999 percent
		- S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA, S3 One Zone-IA, S3 Glacier, and S3 Glacier Deep Archive Amazon S3 storage classes, objects are designed for 99.999999999% (11 9’s) durability. Reference: [Performance across the S3 Storage Classes](https://aws.amazon.com/s3/storage-classes/#Performance_across_the_S3_Storage_Classes "null").
15. Which service works in conjunction with EC2 Autoscaling in order to provide predictive scaling based on daily and weekly trends?
	- AWS Autoscaling
		- EC2 Autoscaling works in conjunction with the AWS Autoscaling service to provide a predictive ability to your autoscaling groups
16. You are running an Amazon RDS Multi-AZ deployment. Can you use the secondary database as an independent read node?
	- Yes
		- In the event of a planned or unplanned outage of your DB instance, Amazon RDS automatically switches to a standby replica in another Availability Zone if you have enabled Multi-AZ. You can force a failover manually when you reboot a DB instance. Reference: [Failover Process for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html "null")
17. You need to use an object-based storage solution to store your critical, non-replaceable data in a cost-effective way. This data will be frequently updated and will need some form of version control enabled on it. Which S3 storage solution should you use?
	- S3 Standard
		- From the question, we can identify that:
			-   the data is non-replaceable (All S3 classes are at 11 9s of Durability now except for RRS)
			-   the data is frequently updated (Classes outside of S3 Standard & S3 Intelligent-Tiering have extra charges for frequently accessed data).
			-   cost-effective (S3 is more cost effective than S3 Intelligent-Tiering if the data is updated frequently)
			-   version control must be an available feature (S3 has versioning as a feature) All of these items combined make S3 the best option for the available information.
18. You are running an Amazon RDS Multi-AZ deployment. Can you use the secondary database as an independent read node?
	- No
		- You can't use the standby (secondary database) to offload reads from an application. The standby instance is only there for failover. If you want an independent read node, you need to create a special type of DB instance called a read replica, from a source DB instance. Reference: [Working with Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html "null")
19. Your company has built an internal scrum tool for running all your scrum ceremonies. Usage is predictably high between 9 - 10AM Mon-Fri and also 1PM - 2PM Thu and Fri. Which feature of autoscaling will easily prepare your system to handle the load?
	- Schedule scaling
		- Target tracking could work but you need to invest time in determining the correct metric to track (e.g. CPU, Memory, Load Balancer Requests). Also Manual Scaling requires that someone changes configuration to scale up and scale down every day. Finally over provisioning in order to cope with peak demand defeats the purpose of elastic scaling of your compute. For situations where your traffic is very predictable, the easiest way to scale with demand is to create scheduled scaling actions.
