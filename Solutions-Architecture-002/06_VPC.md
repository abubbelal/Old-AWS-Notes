# VPC Overview 
#### What is a VPC? 
Amazon Virtual Private Cloud (Amazon VPC) lets you provision a logically isolated section of the Amazon Web Services (AWS) Cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways. 

You can easily customize the network congfiguration for your Amazon Virtual Private Cloud. For example, you can create a public-facing subnet for your webservers that has access to the Internet and place your backend systems such as databases or application servers in a private-facing subnet with no Internet access. You can leverage multiple layers of security, including security groups and network access control lists, to help control access to Amazon EC2 instances in each subnet

Additionally, you can create a Hardware Virtual Private Network (VPN) connection between your corporate datacenter and your VPC and leverage the AWS cloud as en extension of your corporate datacenter. 

![[firefox_RtmWUlTTkU.png]]

**NOTE: good site** CIDR.xyz 
helps us determine what our first IP address is, last ipaddress and total available ip address, plus subnet mask 

![[firefox_FULljxOWxL.png]]

#### VPC Features 
- Launch instances into a subnet of your choosing
- Assign custom IP address ranges in each subnet
- Configure route tables between subnets
- Create internet gateway and attach it to our VPC
- Much better security control over your AWS resources
- Instance security groups 
- Subnet network access control lists (ACLS)

 **Default VPC vs Custom VPC**

 - Default VPC is user friendly, allowing you to immediately deploy instances
 - All subnets in default VPC have a route out to the internet
 - Each EC2 instance has both a public and private IP address


#### VPC Peering 
- Allows you to connect one VPC with another via a direct network route using private IP addresses 
- Instances behave as if they were on the same private network
- You can peer VPC's with other AWS accounts as well as with other VPCs in the same account
- Peering is in a star configuration: ie 1 central VPC peers with 4 oters. No Transitive Peering
- You can peer between regions 

![[firefox_aNBBNWJR8G.png]]

### Exam Tips 

Remember: 
- think of a VPC as a logical datacenter in AWS
- Consists of IGWs (Or virtual private gateways), Route Tables, Network Access Control Lists, Subnets, and Security Groups 
- 1 Subnet = 1 Availability Zone
- Security Groups are Stateful; Network Access Control Lists are Stateless
- No Transitive Peering

# Create Custom VPC - Demo 
AWS Console --> VPC, under Networking & Content Delivery --> Your VPCs -> Create VPC --> provide it a name, for CIDR using ipv4 is fine 
![[firefox_dAhVjPKwzR.png]] --> Create

This created the following: 
- No subnets, No Internet gateways -- didn't create these
- Route table, under Route Tables
- Created Network ACLs
- Created Network Security Group

What the VPC looks like so far 
![[firefox_nQqkaylAA5.png]]

**Creating some Subnets**
VPC Dashboard --> Subnets --> Create subnet --> provide a name tag, select our vpc we created, choose preferred availability zone,  for CIDR just use ipv4 --> Create 

![[firefox_efreSQG7eE.png]]

That created our subnet for the VPC
We can create more subnets under the VPC
![[firefox_aE9luWIr7d.png]]

We now have 2 subnets under the VPC, subnets do not span availability zones; its one subnet per availability zone

Now lets make one subnet public, and one private

**Creating public subnet**
In order for the subnet to be publicly accessible we need our EC2 instance to launch in it with public ip address: so we need to auto assign our public ip addresses 

Select the subnet --> Actions --> Modify --> Auto-assign ip settings --> Auto-assign IPv4 --> Save

What our VPC looks like now 
![[firefox_mO4H9OCD5g.png]]

**Adding Internet Gateway**

VPC Dashboard --> Internet Gateways --> Create Internet Gateway --> Provide a name --> Create --> Select the Internet gateway created --> Actions --> attach to vpc --> select the vpc we want to attach to --> Attach 

*you can only have one internet gateway attached to a vpc*

**Route Tables**
Need to configure the route table, so we have a route out to the internet
VPC Dashboard --> Select VPC --> Create Route Table --> provide a name, select a vpc --> Create 

Select the newly create route table --> Routes --> Edit Routes --> Add Route --> we can use ipv4 or ipv6, for instance select the internet gateway --> Create

![[firefox_kp7wgeKlLN.png]]

We now have a route to the internet through the internet gateway, using our public subnet. 

**Now any subnet associated with this route table will automatically be public**
Selected the route table --> Subnet Associations --> Edit Subnet Associations --> Select the subnet to assocaite it with this route table --> Save 

#### Configuring the EC2 instances 
Create two instances, one for public subnet and one for private subnet 

EC2 --> Launch --> Default --> Network: choose the VPC, subnet: choose the public subnet --> Storage --> Tags --> Security groups --> Create new ("WebDMZ"), Add rule: Http --> Review Launch --> Launch --> Get keypair --> Launch 

EC2 --> Default --> Network: choose VPC, Subnet: choose private, disable auto-assign public ip --> Next --> Tags --> Security Group use default --> Review and Launch --> Launch --> Launch 

The public instance will have a public ipv4 address
![[firefox_pqOQSq79kI.png]]


SSH into the public EC2 Instance 

Our VPC looks like this now 
![[firefox_cm10bMxUeS.png]]

### Exam Tips 
- When you create a VPC a default Route Table, Network Access Control List (NACL) and a default Security Group 
- It won't create any subnets, nor will it create a default internet gateway
- US-east-1a in your aWS can be a completely different availability zone to US-east1a in another account. The AZ's are randomized
- Amazon always reserves 5 IP addresses within your subnets
- You can only have 1 Internet Gateway per VPC
- Security Groups can't span VPCs

## Part 2

Right now we can only communicate with our public instance, lets find a way to get into the private instance

EC2 console --> Security Group --> Create security group --> choose a VPC --> add inbound rules: ALL ICMP - IPv4, for source we can select the security group or the ip address; Add another rule for HTTP, and another for HTTPS, another for SSH, and another for the database 
![[firefox_0JMtZ0b1be.png]]
--> Create Security group 


Now for the private ec2 instance --> select it --> security --> modify security group, and select the one made earlier --> assign security groups

Now you can SSH into the private instance through the public ec2 instance using the private instance's private ip address when we SSH

# NAT Instances and NAT Gateways - Demo 
Network Address Translation

We want to enable our EC2 instances and our private instance to still be able to go out to the internet and download software. Currently only the public instance can do that because it is attached to the internet gateway and has the subnet associated with it. 

The private instance cannot go out to the internet as of now.

**We will use NAT instances or NAT gateways to be able to access internet through the private EC2**

NAT instances are individual EC2 instances 
NAT gateways are highly available, spread across regions and not dependent on a single instance

![[firefox_y7LZQGBKML.png]]

### NAT INSTANCE
**Creating NAT Instances**

EC2 --> Launch Instance --> Community AMIs, search for NAT --> Select the first one --> Default --> Network: VPC, subnet: public subnet -->Next --> Storage --> Tags --> Configure Security Group --> Existing security group ("WebDMZ") --> Launch --> Launch

NAT Instance acts as a gateway to our internet gatway; like a bridge between our private subnets through our public subnet, through our internet gateway. What we need to do is disable the source and destination checks.

EC2 --> Running instances --> Select the NAT EC2 --> Actions --> Enable Source/destination check --> Disable it

Now to get the private EC2 instance to communicate with the NAT EC2 we need to create a route.

And we'll create the route in the default route table, so that the EC2 instances can communicate with the NAT instance

VPC Dashboard --> Route tables --> Main route table for the Custom VPC --> Routes --> Edit routes --> Add route --> To let it access it internet, we set the destination to 0 and target it to an instance >> NAT instance --> SAVE route 

![[firefox_ZRJ9lSijAJ.png]]

Now back to EC2

SSH into the public EC2 instance --> then SSH into the private EC2 instance --> We are now able to access the internet through the NAT Instance 

### NAT Gateway

VPC Console --> NAT Gateways --> Create Gateway --> Subnet: choose public subnet id, Elastic IP: Create New IP --> Create NAT Gateway

Edit Route Tables --> Select the main Route Table --> routes --> edit routes --> 000 for detination, and for target, select NAT Gateway, and select the gateway just created --> save route

Now we can Access internet through private EC2 instance through NAT Gateways

### Exam Tips 
NAT Instances 

- When creating a NAT instance, Disable Source/Destination Check on the instance
- NAT instances must be in a public subnet
- THere must be a route out of the private subnet to the NAT instance, in order for this to work
- The amount of traffic that NAT instances can support depends on the instance size. If you are bottlenecking, increase the instance size
- You can create high availability Using Autoscaling Groups, Multiple subnets in different AZs, and a script to automate failover
- Behind a Security Group


NAT Gateways 

- Redundant inside the AZ
- Preferred by the enterprise
- Starts at 5Gbps and scales currently to 45Gbps 
- No need to patch
- Not associated with security groups
- Automatically assigned a public ip address
- Remember to update your route tabnles
- No need to disable Source/Destination checks
- If you have resources in multiple Availlability Zones and they share one NAT gateway, in the event that the NAT gateway's Availability Zone is down, resources in the other Availability Zones lose internet access. To create an Availability Zone-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone 


# Network Access Control Lists vs Security Groups - Demo 

What our network looks like right now 
![[firefox_o6tXueRGBL.png]]

VPC Console --> Network ACLs --> Create new Network ACL --> provide a name, choose the custom VPC --> Create 

By default, new network ACLs deny everything. 

VPC Console --> Network ACL --> Select the Network ACL just created --> Subnet Associations --> Edit subnet associations --> Select the public subnet to this --> Edit 

This will also remove that subnet from the default subnet; that was created when we made a VPC

Now we need to edit inbound rules on the Network ACL to let some connections through.

VPC Console --> Network ACL --> Select the Network ACL  created --> Inbound rules --> Edit rules --> Add rules --> provide a rule number (increment by a 100), allow 80,
https 443, ssh 22 --> save 

![[firefox_rpyuV4zrGR.png]]

This allows inbound on all those ports, we also need to specify the outbound rules 

same thing as inbound but this time for outbound. But we also allow Ephemeral Ports

![[firefox_WLx6w9064S.png]]

**Ephemeral Ports**

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports

We can also add deny rules to inbound rules and outbound rules. But make sure to set the deny rule before the allow rules 

When we created a custom VPC, it created for us a default Network ACL; and everytime we add a subnet to our VPC, it will associate it with this default Network ACL. You can associte with a new Network ACL, but the subnet itself can only be associated with one Network ACL at any given time. But Network ACLs can have multiple subnets on them. 

Network ACL rules are evaluated in numerical order: and any deny first will be denied, even if its allowed later. Network ACL rules are evaluated before security group rules; if some rule is denied in the Network ACL rules it won't even reach the security group. 

### Exam Tips 
Remember: 
- Your VPC automatically comes with a default network ACL, and by default it allows all outbound and inobund traffic
- You can create custom network ACLs. By default, each custom network ACL denies all inbound and outbound traffic until you add rules.
- Each subnet in your VPC must be associated with a network ACL. If you don't explicitly associate a subnet with a network ACL, the subnet is atuomatically associated with the default network ACL
- Block IP addresses using network ACLs not security groups  -- cant do it with security groups
- You can associate a network ACL with multiple subnets; however, a subnet can be associated with only one network ACL at a time. When you associate a network ACL with a subnet, the previous association is removed
- Network ACLs contain a numbered list of rules that is evaluated in order, starting with the lowest numbered rule
- Network ACLs have separate inbound and outbound rulesm and each rule can either allow or deny traffic
- Network ACLs are stateless; responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa)


# VPC Flow Logs - Demo
VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data is sotred using AmazonCloudWatch Logs. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs.

#### VPC Flor Log Levels 
Flow logs can be created at 3 levels:

- VPC
- Subnet
- Network Interface Level 

Need to first create a new log group

AWS Console --> CloudWatch --> Logs --> Create Log Group --> Provide it a name --> Create

AWS Console --> VPC --> Your VPCs --> Custom VPC --> Actions --> Create Flow Log --> Choose a filter, choose the log group we created, choose an IAM role; or create one --> Create

![[firefox_XgEgnhKvlg.png]]

### Exam Tips 
- You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
- You can tag flow logs
- After you've created a flow log, you cannot change its configuration; for example, you can't assocaite a different IAM role with the flow log

Not all IP Traffic is monitored
- Traffic generated by instances when they contact the Amazon DNS server. If you use your own DNS server, then all traffic to that DNS server is logged
- Traffic generated by a Windows instance for Amazon Windows Lincese activation
- Traffic to and from 169.254.169.254 for instance metadata
- DHCP traffic
- Traffic to the reserved IP address for the default VPC router

# Bastions

**A Bastion Host:**
A bastion host is a special purpose computer on a network specfically designed and configured to withstand attacks. The computer generally hosts a single application, for example a proxy server, and all other services are removed or liomited to reduce the threat to the computer. It is hardened in this manner primarily due to its location and purpose, which is either on the outside of a firewall or in a demilitarized zone (DMZ) and usually involves access from untrusted networks or computers

![[firefox_ag8XJS5Mfy.png]]

### Exam Tips 
- A NAT Gateway or NAT Instance is sued to provide internet traffic to EC2 instances in a private subnet
- A Bastion is used to securely administer EC2 instances (Using SSH or RDP). Bastions are called Jump Boxes in Australia
- You cannot use a NAT Gateway as a Bastion host

# Direct Connect 
AWS Direct Connect is a cloud service solution that makes it easy to establish a dedicated network connection from your premises to AWS. Using AWS Direct Connect, you can establish private connectivity between AWS and your datacenter, office, or colocation environment, which in many cases can reduce your network costs, increase bandwidth throughput, and provide a more consistent network experience than Internet based connections. 

![[firefox_8PbyjC4HEI.png]]

### Exam Tips 
- Direct Connect directly connects your data center to AWS
- Userful for high throughput workloads (ie lots of network traffic)
- Or if you need a stable and reliable secure connection

# Setting up Direct Connect
Steps: 
- Create a virtual interface in the direct connect console. This is a PUBLIC virtual interface
- Go to the VPC console and then to VPC connections. Create a customer gatway
- Create a virtual private gateway
- Attach the virtual private gateway to the deisred VPC
- Select VPN Connections and create a new VPC Connection
- Select the virtual private gateway and the customer gateway
- Once the vpn is available, set up the VPN on the customer gateway or firewall

https://www.youtube.com/watch?v=dhpTTT6V1So

# Global Accelerator 

AWS Global Accelerator is a service in which you create accelerators to improve availability and performance of your applications for local and global users. 

Global Accelerator directs traffic to optimal endpoints over the AWS global network. This improves the availability and performance of your internet applications that are used by a global audience. 

By default, Global Accelerator provides you with two static IP addresses that you associate with your accelerator. Alternatively, you can bring your own IP adresses. 


![[firefox_8UXYG9RWzn.png]]

With / Without Global Accelerator
![[firefox_sq0CEIJy8y.png]]


**AWS Global Accelerator includes the following components**
- Static IP addresses
- Accelerator
- DNS Name
- Network Zone
- Listener
- Endpoint Group
- Endpoint

**Static IP address**
By default, global Accelerator provies you with two static IP addresses that you associate with your accelerator: Or you can bring your own (1.2.3.4 , 5.6.7.8)

**Accelerator**
An Accelerator directs traffic to optimal endpoints over the AWS global network to improve the availability and performance of your internet applications. Each accelerator includes one or more listeners

**DNS Name**
Global Accelerator assigns each accelerator a default Domain Name System (DNS) name - similar to a1234567890abcdef.awsglobalaccelerator.com -- that points to the static IP addresses that Global Accelerator assigns to you

Depending on the use case, you can use your accelerator's static IP addresses or DNS name to route traffic to your accelerator, or set up DNS records to route traffice using your own custom domain name

**Network Zone**
A network zone servies the static IP addresses for your accelerator from a unique IP subnet. Similar to an AWS availability Zone, a network zone is an isolated unit with its own set of physical infrastructure

When you configure an accelerator, by default, Global Accelerator allocates two IPv4 addresses for it. If one IP address from a network zone becomes un available due to IP address blocking by certain client networks, or network disruptions, client applications can retry on the healthy static IP address from the other isolated network zone. 

**Listener**
A listener processes inbound connections from clients to Global Accelerator, based on the port (or port range) and protocol that you configure. Global Accelerator supports both TCP and UDP protocols.

Each listener has one or more endpoint groups associated with it, and traffic is forwarded to endpoints in one of the groups

You associate endpoint groups with listeners by specifying the Regions that you want to distribute traffic to. Traffic is distributed to optimal endpoints within the endpoint groups associated with a listener.

**Endpoint Group**
Each endpoint group is associated with a specific AWS Region. Endpoint groups include one or more endpoints in the Region. 

You can increase or reduce the percentage of traffic that would be otherwise directed to an endpoint group by adjusting a settting called a traffic dial. 

The traffic dial lets you easily do performance testing or blue/green deployment testing for new releases across different AWS Regions, of example. 

**Endpoint**
Endpoints can be Network Load Balancers, Application Load Balancers, EC2 isntances, or Elastic IP addresses. 

An application load balancer endpoint can be an internet-facing or internal. Traffic is routed to endpoints based on configuration options that you choose, such as endpoint weights. 

For each endpoint, you can configure weights, which are numbers that you can use to specify the proportion of traffic to route to each one. This can be useful, for example, to do performance testing within a Region.

#### Demo 

First create an endpoint (can be an Application Load Balancer, EC2, etc)
EC2 Console --> launch instance --> default is fine, open HTTP ports; use WebDMZ security group --> launch

Now we have an endpoint, where global accelerator will send all traffic to

Now lets create a Global Accelerator

AWS Console --> Global Accelerator --> Create Accelerator --> provide a name --> Next --> ports to listen to 
![[firefox_5M9A6m2Gyn.png]] 
--> Next --> choose end point regions --> Add the endpoint, our EC2 instance 
![[firefox_AyrNdP4Ady.png]]
--> Create Accelerator

Straight away we are assigned:
- assigned 2 static ip addresses
- dns name

### Exam Tips 
Know what a Global Accelerator is and where you would use it
- AWS Global Accelerator is a service in which you create accelerators to imnprove availability and performance of your applications for local and global users
- You are assigned two static IP addresses (or alternatively you can bring your own)
- You can control traffic using traffic dials. This is done within the endpoint group
- You can control weighing to individual end points using weights

# VPC Endpoints 

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection. Instances in your VPC do not require public IP addresses to communicate with resources in the service Traffic between your VPC and the other service does not leave the Amazon network.

Endpoints are virtual devices. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic

**There are two types of VPC endpoints**
- Interface Endpoints
- Gateway Endpoints

**Gateway Endpoints**
Currently Gateway Endpoints support
- Amazon S3
- DynamoDB

#### S3 Gateway Endpoint

The Network so far 
![[firefox_9p3ZLYm1ns.png]]

Need to change this architecture to:
![[firefox_HYcmNrSFnQ.png]]

Make sure there is a role to access S3, or create an S3 admin access, and add this role to the EC2 instance (private server). 

SSH into the public EC2, then SSH into the private EC2 through this. 


### Exam Tips 
**An interface endpoint is an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service. The following services are supported**

| Amazon API Gateway                             | Amazon Kinesis Data Streams                   |
| ---------------------------------------------- | --------------------------------------------- |
| AWS CloudFormation                             | Amazon SageMaker and Amazon SageMaker Runtime |
| AWS CloudWatch Events                          | Amazon SageMaker Notebook Instance            |
| Amazon CloudWatch Logs                         | AWS Secrets Manager                           |
| AWS CodeBuild                                  | AWS Security Token Service                    |
| AWS Config                                     | AWS Service Catalog                           |
| Amazon EC2 API                                 | Amazon SNS                                    |
| Elastic load Balancing API                     | Amazon SQS                                    |
| AWS Key Management Service                     | AWS Systems Manager                           |
| Endpoint services hosted by other AWS accounts | Supported AWS Marketplace partner services    |

Basically need to know: You need to attach an ENI to an EC2 instance, and that'll allow you communicate with these services. Through their internal network, and without an internet gateway. 

Two types of VPC Endpoints: 
- Interface Endpoints
- Gateway Endpoints

Gateway endpoints support:
- AWS S3
- DynamoDB

# AWS PrivateLink 
Opening your service in a VPC to another VPC

![[firefox_7LYXyzAYwY.png]]

We can either:
- Open the VPC up to the internet:
	- Security considerations; everything in the public subnet is public
	- A lot more to manage
- Use VPC Peering
	- You will have to create and manage many different peering relationships
	- The whole network will be accessible. This isn't good if you have multiple applications within your VPC. 

**Opening your services in a VPC to another VPC using private link**
- The best way to expose a service VPC to tens, hundreds, or thousands of customer VPCs
- Doesn't require VPC peering; no route tables, NAT, IGWs, etc
- Requires a Network Load balancer on the service VPC and an ENI on the customer VPC

![[firefox_NB9WqNGNLh.png]]

# AWS Transit Gateway 
Typical Network Architecture
![[firefox_PTnsUSTK2b.png]]

Simplifying your network topology through Transit Gateways
![[firefox_7pZs3XxWIv.png]]

**Transit Gateways**
- Allows you to have transitive peering between thousands of VPCs and on-premises data centers
- Works on a hub-and-spoke model
- Works on a regional, basis but you can have it across multiple regions
- You can use it across multiple AWS accounts using RAM (Resource Access Manager)
- You can use route tables to limit how VPcs talk to one another 
- Works with Direct Connect as well as VPN connections
- Supports IP multicast (not supported by any other AWS service)

# AWS VPN CloudHub
![[firefox_8LnpI0zyDV.png]]

- If you have multiple sites, each with its own VPN connection, you can use AWS VPN CloudHub to connect those sites together
- Hub-and-spoke model
- Low ccost; easy to manage
- It operates over the public internet, but all traffic between the customer gateway and the AWS VPN CloudHub is encrypted

# AWS Network Costs 
![[firefox_HrCtIZ4GGj.png]]

- use private IP addresses over public IP addresses to save on costs. This then utilizes the AWS backbone network
- If you want to cut all network costs, group your EC2 instances in the same availability Zone and use private IP addresses. This will be cost-free, but make sure to keep in mind single point of failure issues. 

# VPC Summary 

Remember: 
- think of a VPC as a logical datacenter in AWS
- Consists of IGWs (Or virtual private gateways), Route Tables, Network Access Control Lists, Subnets, and Security Groups 
- 1 Subnet = 1 Availability Zone
- Security Groups are Stateful; Network Access Control Lists are Stateless
- No Transitive Peering

When Creating your VPC 
- When you create a VPC a default Route Table, Network Access Control List (NACL) and a default Security Group 
- It won't create any subnets, nor will it create a default internet gateway
- US-east-1a in your aWS can be a completely different availability zone to US-east1a in another account. The AZ's are randomized
- Amazon always reserves 5 IP addresses within your subnets
- You can only have 1 Internet Gateway per VPC
- Security Groups can't span VPCs

NAT Instances 
- When creating a NAT instance, Disable Source/Destination Check on the instance
- NAT instances must be in a public subnet
- There must be a route out of the private subnet to the NAT instance, in order for this to work
- The amount of traffic that NAT instances can support depends on the instance size. If you are bottlenecking, increase the instance size
- You can create high availability Using Autoscaling Groups, Multiple subnets in different AZs, and a script to automate failover
- Behind a Security Group


NAT Gateways 
- Redundant inside the AZ
- Preferred by the enterprise
- Starts at 5Gbps and scales currently to 45Gbps 
- No need to patch
- Not associated with security groups
- Automatically assigned a public ip address
- Remember to update your route tabnles
- No need to disable Source/Destination checks
- If you have resources in multiple Availlability Zones and they share one NAT gateway, in the event that the NAT gateway's Availability Zone is down, resources in the other Availability Zones lose internet access. To create an Availability Zone-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone 

Network ACL: 
- Your VPC automatically comes with a default network ACL, and by default it allows all outbound and inobund traffic
- You can create custom network ACLs. By default, each custom network ACL denies all inbound and outbound traffic until you add rules.
- Each subnet in your VPC must be associated with a network ACL. If you don't explicitly associate a subnet with a network ACL, the subnet is atuomatically associated with the default network ACL
- Block IP addresses using network ACLs not security groups  -- cant do it with security groups
- You can associate a network ACL with multiple subnets; however, a subnet can be associated with only one network ACL at a time. When you associate a network ACL with a subnet, the previous association is removed
- Network ACLs contain a numbered list of rules that is evaluated in order, starting with the lowest numbered rule
- Network ACLs have separate inbound and outbound rulesm and each rule can either allow or deny traffic
- Network ACLs are stateless; responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa)

ELBs and VPCs
- You need a minimum of two public subnets to deploy an internet facing loadbalancer

VPC Flow Logs
- You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
- You can tag flow logs
- After you've created a flow log, you cannot change its configuration; for example, you can't assocaite a different IAM role with the flow log

Not all IP Traffic is monitored
- Traffic generated by instances when they contact the Amazon DNS server. If you use your own DNS server, then all traffic to that DNS server is logged
- Traffic generated by a Windows instance for Amazon Windows Lincese activation
- Traffic to and from 169.254.169.254 for instance metadata
- DHCP traffic
- Traffic to the reserved IP address for the default VPC router

Bastions vs NAT Gateways/Instances
- A NAT Gateway or NAT Instance is used to provide internet traffic to EC2 instances in a private subnet
- A Bastion is used to securely administer EC2 instances (Using SSH or RDP). Bastions are called Jump Boxes in Australia
- You cannot use a NAT Gateway as a Bastion host

Direct Connect
- Direct Connect directly connects your data center to AWS
- Userful for high throughput workloads (ie lots of network traffic)
- Or if you need a stable and reliable secure connection

Steps to creating a direct connect connection: 
- Create a virtual interface in the direct connect console. This is a PUBLIC virtual interface
- Go to the VPC console and then to VPC connections. Create a customer gatway
- Create a virtual private gateway
- Attach the virtual private gateway to the deisred VPC
- Select VPN Connections and create a new VPC Connection
- Select the virtual private gateway and the customer gateway
- Once the vpn is available, set up the VPN on the customer gateway or firewall

Global Accelerator 
- AWS Global Accelerator is a service in which you create accelerators to imnprove availability and performance of your applications for local and global users
- You are assigned two static IP addresses (or alternatively you can bring your own)
- You can control traffic using traffic dials. This is done within the endpoint group
- You can control weighing to individual end points using weights

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection. Instances in your VPC do not require public IP addresses to communicate with resources in the service Traffic between your VPC and the other service does not leave the Amazon network.

Endpoints are virtual devices. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic

AWS PrivateLink 
- The best way to expose a service VPC to tens, hundreds, or thousands of customer VPCs
- Doesn't require VPC peering; no route tables, NAT, IGWs, etc
- Requires a Network Load balancer on the service VPC and an ENI on the customer VPC

Transit Gateway 
- Allows you to have transitive peering between thousands of VPCs and on-premises data centers
- Works on a hub-and-spoke model
- Works on a regional, basis but you can have it across multiple regions
- You can use it across multiple AWS accounts using RAM (Resource Access Manager)
- You can use route tables to limit how VPcs talk to one another 
- Works with Direct Connect as well as VPN connections
- Supports IP multicast (not supported by any other AWS service)

AWS VPN CloudHub
- If you have multiple sites, each with its own VPN connection, you can use AWS VPN CloudHub to connect those sites together
- Hub-and-spoke model
- Low ccost; easy to manage
- It operates over the public internet, but all traffic between the customer gateway and the AWS VPN CloudHub is encrypted

AWS Network Costs 
- use private IP addresses over public IP addresses to save on costs. This then utilizes the AWS backbone network
- If you want to cut all network costs, group your EC2 instances in the same availability Zone and use private IP addresses. This will be cost-free, but make sure to keep in mind single point of failure issues. 

# Creating a Basic VPC and Associated Components in AWS
**AccountId**: 784424801992
![[firefox_8wKZo3CCMs.png]]

Network we need to set up
![[firefox_EDYmYZLCCq.png]]


**Introduction**

In this hands-on lab, we will create a VPC with an internet gateway, as well as create subnets across multiple Availability Zones.

**Solution**

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

![[firefox_xGJtxlEefX.png]]
![[firefox_lj7JGUvR2q.png]]
Doesn't have any internet gateways, subnets; but does have it's default route tables; best to make custom route table

![[firefox_Zb4hwO3yJw.png]]
![[firefox_OsOpParHda.png]]

By default it isn't attached to anything, we can attach this to our custom VPC
![[firefox_PEx3b7cNy5.png]]

Now we need to define our two subnets (public and private)
![[firefox_RVjJ0CO8v6.png]]
![[firefox_LMUpk5JiAS.png]]
The IPv4 CIDR Block of `172.16.1.0/24` is whats going to allow any EC2 instances assigned to this subnet, this prefix. So this CIDR block will give any EC2 instance assigned to this subnet the prefix of `172.16.1` 

Now do the same for the private subnet. 
![[firefox_VpIsovVTWc.png]]

Now we can create our first route table
- there will be a default route table already there, made when we created the VPC

![[firefox_cfMYLOikBv.png]]
![[firefox_JldXAlKaGZ.png]]
Now do the same again for private subnet
![[firefox_HgY1oQgoUW.png]]

Now we need to add the entry to the public route table, so it has a route to our internet gateway. 
select public route table --> routes --> edit routes 
![[firefox_PgRxVwCT5h.png]]
We need to add a route to the internet gateway here, so that this subnet -- once the route table is attached -- has access to the internet. 
![[firefox_cd6F3DfLDd.png]]
So now any traffic that is not destined to the local network is routed out to the internet gateway. 

Now we need to associate this route table to our public subnet
select public route table --> subnet associations --> edit subnet associations --> 
![[firefox_AuUtDiKDfE.png]]
Choose the public subnet --> 
![[firefox_YLwAGSHmQQ.png]]
Once the association is made: traffic going in/out of the public subnet will llook at this route table and see that it has internet gateway attached, and it'll allow the traffic to route between the internet gateway and our public subnet

Now do the same thing for the private route table. 
We need don't to define any routes for the private route table, we just need to associate it with a private subnet. 
![[firefox_W9m8CUudlP.png]]

Since this only has a local route; meaning it can only send traffic between the public and private subnet, it's not able to accesss the internet. Because the public subnet has the local route table entry, it's also able to communicate with the private subnet. 

We still haven't configured our Network ACL yet. 
when we go to VPC --> Network ACLs --> there is already a default NACL when we created the VPC, 
we need to create our own. 

![[firefox_eIw6XZsnmm.png]]
![[firefox_ZUC773jKN2.png]]
Now we need to edit the inbound rules on this custom NACL

![[firefox_15XFygO4Fg.png]]
Add new Rule --> Lets add a rule for HTTP, and SSH and allow all IPs
![[firefox_zMjZE4kzVj.png]]
Now just those ports are allowed connection, all others are implicitly denied. 
![[firefox_khQURpIMZ8.png]]
Now we need to set the outbound rules
Because NACL rules are stateless, we have to define it for both inbound and outbount; right now http and ssh is allowed in but not out. 
First lets allow the ephemeral port; this port range says whenever traffic is sent into your subnet, that traffic comes from a random port on the source computer. As that traffic comes into the subnet, we have to send it back to the source computer on the same port. So that port is going to be one of the ports in this range here `1024-65535`. 
![[firefox_s2TvSA8dic.png]]

We now have the public NACL ready, and it needs to be associated with the public subnet. 
![[firefox_RytmG4Om1S.png]]
![[firefox_bZrz4YJRRy.png]]

We now have our public subnet accessible to the internet, because we have the internet gateway attached to the VPC. We have a route table that has a route to the internet gateway. we have our subnet created, and the subnet has the NACL and the route table associated with it. 
![[firefox_EDYmYZLCCq.png]]

Now we need to create the NACL for the private subnet. 
![[firefox_QFTHXXDeSZ.png]]
Edit it's inbound rules 
Explicitly allow only the SSH port, and set the source to the to the subnet range that we set on our public subnet: which was `172.16.1.0/24`.  This allows SSH into the private subnet only through the public subnet range devices.

![[firefox_MoMdJpyN9i.png]]
Now we have to edit the outbound rules as well. Allowing the ephemeral ports
![[firefox_RfPOXZmOhg.png]]

Last thing is to associate the private NACL with the private subnet. 
![[firefox_sFbl1bHvGo.png]]

Now if we have an EC2 instance in the public subnet and an EC2 in the private subnet; they will be able to communicate with each other. That's the only traffic allowed into our private subnet right now, and public subnet has SSH and HTTP access through the NACL via the route table, and through the internet gateway. 

### Create a VPC

1.  Navigate to the VPC dashboard.
2.  Click **Your VPCs** in the left-hand menu.
3.  Click **Create VPC**, and set the following values:
    -   _Name tag_: **VPC1**
    -   _IPv4 CIDR block_: **172.16.0.0/16**
4.  Leave the _IPv6 CIDR block_ and _Tenancy_ fields as their default values.
5.  Click **Create**.

### Create an Internet Gateway, and Connect It to the VPC

1.  Click **Internet Gateways** in the left-hand menu.
2.  Click **Create internet gateway**.
3.  Give it a _Name tag_ of "IGW".
4.  Click **Create internet gateway**.
5.  Once it's created, click **Actions** > **Attach to VPC**.
6.  In the _Available VPCs_ dropdown, select our **VPC1**.
7.  Click **Attach internet gateway**.

### Create a Public and Private Subnet in Different Availability Zones

#### Create Public Subnet

1.  Click **Subnets** in the left-hand menu.
2.  Click **Create subnet**, and set the following values:
    -   _Name tag_: **Public1**
    -   _VPC_: **VPC1**
    -   _Availability Zone_: **us-east-1a**
    -   _IPv4 CIDR block_: **172.16.1.0/24**
3.  Click **Create**.

#### Create Private Subnet

1.  Click **Create subnet**, and set the following values:
    -   _Name tag_: **Private1**
    -   _VPC_: **VPC1**
    -   _Availability Zone_: **us-east-1b**
    -   _IPv4 CIDR block_: **172.16.2.0/24**
2.  Click **Create**.

### Create Two Route Tables, and Associate Them with the Correct Subnet

#### Create and Configure Public Route Table

1.  Click **Route Tables** in the left-hand menu.
2.  Click **Create route table**, and set the following values:
    -   _Name tag_: **PublicRT**
    -   _VPC_: **VPC1**
3.  Click **Create**.
4.  With _PublicRT_ selected, click the **Routes** tab on the page.
5.  Click **Edit routes**.
6.  Click **Add route**, and set the following values:
    -   _Destination_: **0.0.0.0/0**
    -   _Target_: **Internet Gateway** > **IGW**
7.  Click **Save routes**.
8.  Click the **Subnet Associations** tab.
9.  Click **Edit subnet associations**.
10.  Select our **Public1** subnet.
11.  Click **Save**.

#### Create and Configure Private Route Table

1.  Click **Create route table**, and set the following values:
    -   _Name tag_: **PrivateRT**
    -   _VPC_: **VPC1**
2.  Click **Create**.
3.  With _PrivateRT_ selected, click the **Subnet Associations** tab.
4.  Click **Edit subnet associations**.
5.  Select our **Private1** subnet.
6.  Click **Save**.

### Create Two Network Access Control Lists (NACLs), and Associate Each with the Proper Subnet

#### Create and Configure Public NACL

1.  Click **Network ACLs** in the left-hand menu.
2.  Click **Create network ACL**, and set the following values:
    -   _Name tag_: **Public\_NACL**
    -   _VPC_: **VPC1**
3.  Click **Create**.
4.  With _Public\_NACL_ selected, click the **Inbound Rules** tab below.
5.  Click **Edit inbound rules**.
6.  Click **Add Rule**, and set the following values:
    -   _Rule #_: **100**
    -   _Type_: **HTTP (80)**
    -   _Port Range_: **80**
    -   _Source_: **0.0.0.0/0**
    -   _Allow / Deny_: **ALLOW**
7.  Click **Add Rule**, and set the following values:
    -   _Rule #_: **110**
    -   _Type_: **SSH (22)**
    -   _Port Range_: **22**
    -   _Source_: **0.0.0.0/0**
    -   _Allow / Deny_: **ALLOW**
8.  Click **Save**.
9.  Click the **Outbound Rules** tab.
10.  Click **Edit outbound rules**.
11.  Click **Add Rule**, and set the following values:
  - Rule #_: **100**
  - Type_: **Custom TCP Rule**
  - Port Range_: **1024-65535**
  - Destination_: **0.0.0.0/0**
  - Allow / Deny_: **ALLOW**
12.  Click **Save**.
13.  Click the **Subnet associations** tab.
14.  Click **Edit subnet associations**.
15.  Select the **Public1** subnet, and click **Edit**.

#### Create and Configure Private NACL

1.  Click **Create network ACL**, and set the following values:
    -   _Name tag_: **Private\_NACL**
    -   _VPC_: **VPC1**
2.  Click **Create**.
3.  With _Private\_NACL_ selected, click the **Inbound Rules** tab below.
4.  Click **Edit inbound rules**.
5.  Click **Add Rule**, and set the following values:
    -   _Rule #_: **100**
    -   _Type_: **SSH (22)**
    -   _Port Range_: **22**
    -   _Source_: **172.16.1.0/24**
    -   _Allow / Deny_: **ALLOW**
6.  Click **Save**.
7.  Click the **Outbound Rules** tab.
8.  Click **Edit outbound rules**.
9.  Click **Add Rule**, and set the following values:
    -   _Rule #_: **100**
    -   _Type_: **Custom TCP Rule**
    -   _Port Range_: **1024-65535**
    -   _Destination_: **0.0.0.0/0**
    -   _Allow / Deny_: **ALLOW**
10.  Click **Save**.
11.  Click the **Subnet associations** tab.
12.  Click **Edit subnet associations**.
13.  Select the **Private1** subnet, and click **Edit**.


# Working with AWS VPC Flow Logs For Network Monitoring

![[firefox_wZC8V9koce.png]]

![[firefox_pn2Nf6KF1M.png]]

![[firefox_sF638uVIX4.png]]

Additional Resources

Log in to the live AWS environment using the credentials provided. Make sure you are using us-east-1 (N. Virginia) as the selected region.
CloudWatch Log Metric Filter Pattern
```
[version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
```
Custom Log Data to Test
```
2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 22 6 1 40 1563108188 1563108227 REJECT OK
2 086112738802 eni-0d5d75b41f9befe9e 182.68.238.8 172.31.83.158 42227 22 6 1 44 1563109030 1563109067 REJECT OK
2 086112738802 eni-0d5d75b41f9befe9e 42.171.23.181 172.31.83.158 52417 22 6 24 4065 1563191069 1563191121 ACCEPT OK
2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 80 6 1 40 1563108188 1563108227 REJECT OK
```
Create Athena Table
```SQL
CREATE EXTERNAL TABLE IF NOT EXISTS default.vpc_flow_logs (
  version int,
  account string,
  interfaceid string,
  sourceaddress string,
  destinationaddress string,
  sourceport int,
  destinationport int,
  protocol int,
  numpackets int,
  numbytes bigint,
  starttime int,
  endtime int,
  action string,
  logstatus string
) PARTITIONED BY (
  dt string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' LOCATION 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/' TBLPROPERTIES ("skip.header.line.count"="1");
```
Create Partitions
```SQL
ALTER TABLE default.vpc_flow_logs
ADD PARTITION (dt='{Year}-{Month}-{Day}') location 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/{Year}/{Month}/{Day}';
```
Analyze Data
```SQL
SELECT day_of_week(from_iso8601_timestamp(dt)) AS
  day,
  dt,
  interfaceid,
  sourceaddress,
  destinationport,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
order by sourceaddress
LIMIT 100;
```
## Introduction

Monitoring network traffic is a critical component of security best practices to meet compliance requirements, investigate security incidents, track key metrics, and configure automated notifications. AWS VPC Flow Logs captures information about the IP traffic going to and from network interfaces in your VPC. In this hands-on lab, we will set up and use VPC Flow Logs published to Amazon CloudWatch, create custom metrics and alerts based on the CloudWatch logs to understand trends and receive notifications for potential security issues, and use Amazon Athena to query and analyze VPC Flow Logs stored in S3.

## Solution

Log in to the live AWS environment using the credentials provided.

Once inside the AWS account, make sure you are using `us-east-1` (N. Virginia) as the selected region.

### Create CloudWatch Log Group and VPC Flow Logs to CloudWatch and S3

#### Create VPC Flow Log to S3

![[firefox_Xo4WFXjAQk.png]]
![[firefox_NWFDmt5hGk.png]]
![[firefox_uufhTrwEDw.png]]
The bucket policy will be automatically modified when we created the flow flog, so the flow log can write to this bucket.

1.  Navigate to **VPC** and select **Your VPCs**.
2.  Select the `A Cloud Guru` VPC.
3.  At the bottom of the screen, select the _Flow logs_ tab.
4.  Click **Create flow log**, and set the following values:
    -   _Filter_: **All**
    -   _Maximum aggregation interval_: **1 minute**
    -   _Destination_: **Send to an Amazon S3 bucket**
    -   _S3 bucket ARN_:
        1.  Navigate to S3 in a new browser tab.
        2.  Select the provided bucket (it should have `vpcflowlogsbucket` in its name).
        3.  Click **Copy ARN**.
        4.  Return to the VPC tab and paste in the value.
5.  Leave the rest as their defaults and click **Create flow log**.
6.  Select the _Flow logs_ tab and verify the flow log shows an _Active_ status.
7.  In the S3 browser tab, click to open the bucket.
8.  Click the _Permissions_ tab.
9.  Scroll down to _Bucket policy_.
10.  Notice the bucket path in the policy includes **AWSLogs**.
    
    > _Note_: It can take 5–15 minutes before logs appear, so let's move on while we wait for that to happen.
    

#### Create CloudWatch Log Group

![[firefox_kwNOEzq8t3.png]]
![[firefox_BhTALPh1C2.png]]


1.  In a new browser tab, navigate to **CloudWatch** and select **Log groups**.
2.  Click **Create log group**.
3.  In _Log group name_, enter "VPCFlowLogs".
4.  Click **Create**.

#### Create VPC Flow Log to CloudWatch
![[firefox_Xo4WFXjAQk.png]]
![[firefox_z2OyZ233Ni.png]]


1.  Back in the VPC browser tab, click **Your VPCs** in the left-hand menu.
2.  Select the `A Cloud Guru` VPC.
3.  Select the _Flow Logs_ tab.
4.  Click **Create flow log**, and set the following values:
    -   _Filter_: **All**
    -   _Maximum aggregation interval_: **1 minute**
    -   _Destination_: **Send to CloudWatch Logs**
    -   _Destination log group_: **VPCFlowLogs**
    -   _IAM role_: Select the role with **DeliverVPCFlowLogsRole** in the name.
5.  Leave the rest as their defaults and click **Create flow log**.
6.  Under the _Flow logs_ tab, verify the flow log shows an _Active_ status.
7.  In the CloudWatch browser tab, click the `VPCFlowLogs` log group to open it.
    
    > _Note_: It can take 5–15 minutes before logs start to show up, so let's move on while we wait for that to happen.
    

#### Generate Traffic
![[firefox_c9BqT1qk6b.png]]

1.  In a new browser tab, navigate to **EC2**.
2.  Under _Resources_ on the EC2 dashboard, select **Instances (running)**.
3.  Select the provisioned `Web Server` instance.
4.  At the bottom under _Details_, copy the public IPv4 address to the clipboard.
5.  Open a terminal session and log in to the EC2 instance via SSH (the password is provided on the lab page):
    
    ```
    ssh cloud_user@<PUBLIC_IP>
    ```
    
6.  Exit the terminal:
    
    ```
    logout
    ```
    
7.  Return to the EC2 dashboard.

![[firefox_j1QXIrgpJM.png]]
![[firefox_YWpp8N4Ieg.png]]
![[firefox_18ghSG67gN.png]]
8.  With `Web Server` selected, click **Actions** > **Security** > **Change security groups**.
9.  Under _Associated security groups_, click **Remove** to remove the attached security group.
10.  In the search bar, search for and select the security group with `HTTPOnly` in the name.
11.  Click **Add security group**.
12.  Click **Save**.
13.  Return to the terminal and attempt to connect to the EC2 instance again via SSH using the provided lab credentials.
    
    > **Note:** We expect this connection to time out since we just selected a security group with no SSH access.
    
14.  After about 15 seconds, press **Ctrl-C** to cancel the SSH command.
15.  Return to the EC2 dashboard.
16.  With `Web Server` selected, click **Actions** > **Security** > **Change security groups**.
17.  Under _Associated security groups_, click **Remove** to remove the `HTTPOnly` security group.
18.  In search bar, search for and select the security group with `HTTPAndSSH` in the name.
19.  Click **Add security group**.
20.  Click **Save**.
21.  Attempt to log in to the EC2 instance again via SSH using the credentials provided. This time, it should work.
22.  Exit the terminal:
    
    ```
    logout
    ```
    

### Create CloudWatch Filters and Alerts

#### Create CloudWatch Log Metric Filter

1.  Navigate to **CloudWatch** and select **Log groups**.
2.  Select the `VPCFlowLogs` log group. You should now see a log stream.
    
    > **Note:** If you don't see a log stream listed yet, wait a few more minutes and refresh the page until the data appears.

![[firefox_VelCIUluy5.png]]
    
3.  Go back to the _VPCFLowLogs_ page and select the _Metric filters_ tab.
4.  Click **Create metric filter**.
5.  Enter the following in the _Filter pattern_ field to track failed SSH attempts on port 22:
    
    ```
    [version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
    ```
    
6.  In the _Select log data to test_ dropdown, select **Custom log data**.
    
7.  Paste the following in the text box:
    
    ```
    2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 22 6 1 40 1563108188 1563108227 REJECT OK
    2 086112738802 eni-0d5d75b41f9befe9e 182.68.238.8 172.31.83.158 42227 22 6 1 44 1563109030 1563109067 REJECT OK
    2 086112738802 eni-0d5d75b41f9befe9e 42.171.23.181 172.31.83.158 52417 22 6 24 4065 1563191069 1563191121 ACCEPT OK
    2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 80 6 1 40 1563108188 1563108227 REJECT OK
    ```
    
8.  Click **Test pattern**.
    ![[firefox_WIlE8RuSbA.png]]
9.  Click **Next**.
![[firefox_w8EOrYVxEb.png]]
10.  Set the following values:
    -   _Filter name_: **dest-port-22-rejects**
    -   _Metric namespace_: **VPC Flow Logs**
    -   _Metric name_: **ssh-rejects**
    -   _Metric value_: **1**
11.  Click **Next**.
12.  Click **Create metric filter**.

#### Create Alarm Based on the Metric Filter
![[firefox_1hP1DCHTwO.png]]
![[firefox_b6rE3j6NzT.png]]
![[firefox_aBgQO18GWj.png]]

1.  Once created, select the metric filter and click **Create alarm**.
2.  Change _Period_ to **1 minute**.
3.  Scroll down to _Conditions_ and set _Whenever SSHRejects is..._ to **Greater/Equal** than **1**.
4.  Click **Next**.
5.  In the _Notification_ section, set the following values:
    -   _Select an SNS topic_: **Create new topic**
    -   _Create a new topic..._: Default
    -   _Email endpoints that will receive the notification..._: Your email address
6.  Click **Create topic**.
7.  Click **Next**.
8.  In _Alarm name_, type "SSH Rejects" and click **Next**.
9.  Click **Create alarm**.
10.  Open your email inbox and click the **Confirm Subscription** link in the received SNS email.

#### Generate Traffic for Alerts

1.  In the terminal, log in to the `Web Server` instance via SSH using the lab credentials.
2.  Exit the terminal:
    
    ```
    logout
    ```
    
3.  In a new browser tab, navigate to **EC2** and select **Instances(running)**.
    
4.  Select the `Web Server` instance.
5.  Click **Actions** > **Security** > **Change Security Groups**.
6.  Under _Associated security groups_, click **Remove** to remove the attached security group.
7.  In the search bar, search for and select the `HTTPOnly` security group.
8.  Click **Add security group**.
9.  Return to the terminal and attempt to connect to the EC2 instance via SSH.
    
    > **Note:** We expect this to time out since we just selected a security group with no SSH access.
    
10.  Press **Ctrl-C** to cancel the SSH command.
11.  Return to EC2 dashboard.
12.  With the `Web Server` instance still selected, click **Actions** > **Security** > **Change Security Groups**.
13.  Click **Remove** to remove the `HTTPOnly` security group.
14.  Select again the `HTTPAndSSH` security group and click **Add security group**.
15.  Click **Save**.
16.  Go back to **CloudWatch** > **Alarms**. We should see our `SSHRejects` alarm enter an _In alarm_ state shortly.
    
    > **Note:** If you don't see the alarm listed yet, wait a few more minutes and refresh the page until it appears.
    

### Use CloudWatch Insights
![[firefox_DxLdWCEWLs.png]]
1.  Navigate to **CloudWatch** and select **Logs** > **Insights**.
2.  In the _Select log group(s)_ search window, select **VPCFlowLogs**.
3.  In the right-hand pane, select **Queries**.
4.  Under _Sample queries_, click **VPC Flow Logs** > **Top 20 source IP addresses with highest number of rejected requests**.
5.  Click **Apply**.
6.  Observe the query changes.
7.  Click **Run query**. After a few moments, we'll see some data start to populate.

### Analyze VPC Flow Logs Data in Athena

#### Record Reference Information to Be Used in Athena Queries

> **Note:** Before attempting to run a query in Athena, you have to specify an S3 bucket for the results to be saved.
![[firefox_K4Uu17m9gr.png]]
1.  In a new browser tab, navigate to S3.
2.  Select the provisioned bucket to open it.
3.  Navigate through the bucket folder structure: **AWSLogs** > **{ACCOUNT\_ID}** > **vpcflowlogs** > **us-east-1** > **{YEAR}** > **{MONTH}** > **{DAY}**.
4.  At the top right, click **Copy S3 URI**.

### Create the Athena Table
![[firefox_84PLuS5X2L.png]]
![[firefox_eKLxjKQEyw.png]]

1.  Navigate to Athena.
2.  If you come to the Amazon Athena landing page, click **Get Started**. Otherwise, skip this step.
3.  Above the new query window, click **set up a query result location in Amazon S3**.
4.  In _Query result location_, enter **s3://** and paste in the S3 bucket path previously copied with a forward slash at the end (e.g., `s3://{BUCKET_NAME}/AWSLogs/{ACCOUNT_ID}/vpcflowlogs/us-east-1/{YEAR}/{MONTH}/{DAY}/`).
5.  Click **Save**.
6.  Paste the following DDL code in the new query window, replacing `{your_log_bucket}` and `{account_id}` with your unique values (You can obtain them from the bucket path you've been using.):
    ![[firefox_tvKaGc9CiT.png]]
    ```
    CREATE EXTERNAL TABLE IF NOT EXISTS default.vpc_flow_logs (
      version int,
      account string,
      interfaceid string,
      sourceaddress string,
      destinationaddress string,
      sourceport int,
      destinationport int,
      protocol int,
      numpackets int,
      numbytes bigint,
      starttime int,
      endtime int,
      action string,
      logstatus string
    )
    PARTITIONED BY (dt string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    LOCATION 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/'
    TBLPROPERTIES ("skip.header.line.count"="1");
    ```
    
7.  Click **Run query**. Once executed, a `Query successful` message should display.
    

#### Create Partitions to Be Able to Read the Data

1.  Click the `+` icon to open a new query window.
2.  Paste the following code:
    ![[firefox_CViOpD5N3n.png]]
    ```
    ALTER TABLE default.vpc_flow_logs
        ADD PARTITION (dt='{Year}-{Month}-{Day}')
        location 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/{Year}/{Month}/{Day}';
    ```
    
3.  Update the following variables with your unique values (You can obtain them from the bucket path you've been using.):
    
    -   **{your\_log\_bucket}**
    -   **{account\_id}**
    -   **{Year}**
    -   **{Month}**
    -   **{Day}**
4.  Click **Run query**. A `Query successful` message should display.
    

### Analyze VPC Flow Logs Data in Athena

1.  Open a new query window and paste in the following:
    ![[firefox_PoPgAiLJSk.png]]
    ```
    SELECT day_of_week(from_iso8601_timestamp(dt)) AS
         day,
         dt,
         interfaceid,
         sourceaddress,
         destinationport,
         action,
         protocol
       FROM vpc_flow_logs
       WHERE action = 'REJECT' AND protocol = 6
       order by sourceaddress
       LIMIT 100;
    ```
    
2.  Click **Run query**. Our formatted data should appear underneath.


# Quiz 
1. True or False: You can accelerate your application by adding a second Internet Gateway to your VPC.
	- Flase
		- You can only have one Internet Gateway per VPC.
2. VPC stands for:
	- Virtual Private Cloud
		- VPC stand for Virtual Private Cloud. It is a logically isolated (private) section where you can place AWS resources within a virtual network.
3. Which of the following statements are **NOT** true of EC2 instances in a VPC?
	- In Amazon VPC, an instance retains its public IP when stopped and started.
		- AWS releases your instance's public IP address when it is stopped, hibernated, or terminated. Your stopped or hibernated instance receives a new public IP address when it is started.
4. Are you permitted to conduct your own security assessments or penetration tests on your own VPC without alerting AWS first?
	- Depends on the type of security assessment or penetration test and the service being assessed. Some assessments can be performed without alerting AWS, some require you to alert.
		- AWS customers are welcome to carry out security assessments or penetration tests against their AWS infrastructure without prior approval for 8 services only. You should request authorization for other simulated events. Reference: [Penetration Testing](https://aws.amazon.com/security/penetration-testing/ "null").
5. When you create a custom VPC, which of the following are created automatically?
	- Network ACL, Security Group, Route Table
		- When you create a custom VPC, a default Security Group, network access control list (ACL), and route table are created automatically. You must create your own subnets, internet gateway, and NAT gateway (if you need one).
6. Which of the following options allows you to securely administer an EC2 instance located in a private subnet?
	- Bastion Host
		- A Bastion host allows you to securely administer (via SSH or RDP) an EC2 instance located in a private subnet. Don't confuse Bastions and NATs, which allow outside traffic to reach an instance in a private subnet.
7. Which of the following offers the largest range of internal IP addresses?
	- /16
		- The /16 offers 65,536 possible addresses.
8. Having just created a new VPC and launching an instance into its Public Subnet, you realize that you have forgotten to assign a Public IP to the instance during creation. What is the simplest way to make your instance reachable from the outside world?
	- Create an Elastic IP address and associate it with your instance
		- Although creating a new NIC & associating an EIP also results in your instance being accessible from the internet, it leaves your instance with 2 NICs & 2 private IPs as well as the Public Address and is therefore not the simplest solution. By default, any user-created VPC subnet WILL NOT automatically assign Public IPv4 Addresses to instances – the only subnet that does this is the “Default” VPC subnets automatically created by AWS in your account.
10. What is the name of the AWS Global Accelerator component that services the static IP addresses for your accelerator from a unique IP subnet?
	- Network Zone
		- A network zone services the static IP addresses for your accelerator from a unique IP subnet. Similar to an AWS Availability Zone, a network zone is an isolated unit with its own set of physical infrastructure. When you configure an accelerator, by default, Global Accelerator allocates two IPv4 addresses for it. If one IP address from a network zone becomes unavailable due to IP address blocking by certain client networks, or network disruptions, then client applications can retry on the healthy static IP address from the other isolated network zone. Reference: [AWS Global Accelerator components](https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-components.html "null")
11. In a default VPC, when you launch an EC2 instance and don't specify a subnet, the EC2 instances are assigned 2 IP addresses at launch. What are they?
	- In a default VPC, when you launch an EC2 instance and don't specify a subnet, the EC2 instances are assigned 2 IP addresses at launch. What are they?
		- In a default VPC, when you launch an EC2 instance and don't specify a subnet, it's automatically launched into a default subnet in your default VPC. The default subnet may MapPublicIPOnLaunch set to the value of true. So when it is launched, a public and private IP is available for the instance.
12. To save administration headaches, a consultant advises that you leave all security groups in web-facing subnets open on port 22 to 0.0.0.0/0 CIDR. That way, you can connect wherever you are in the world. Is this a good security design?
	- No
		- 0.0.0.0/0 would allow ANYONE from ANYWHERE to connect to your instances. This is generally a bad plan. The phrase 'web-facing subnets' does not mean just web servers. It would include any instances in that subnet some of which you may not want strangers attacking. You would only allow 0.0.0.0/0 on port 80 or 443 to connect to your public-facing Web Servers, or preferably only to an ELB. Good security starts by limiting public access to only what the customer needs. Please see the AWS Security white paper for complete details.
13. How many internet gateways can be attached to a custom VPC?
	- 1
		- Since an internet gateway is a highly available VPC component, only one is attachable to a custom VPC.
14. Which of these is NOT a component of the AWS Global Accelerator service?
	- CloudFront -- NOT 
	- Static IP Addresses, Endpoint Groups, Listeners -- ARE
		- AWS Global Accelerator and Amazon CloudFront are separate services that use the AWS global network and its edge locations around the world. CloudFront improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery). Global Accelerator improves performance for a wide range of applications over TCP or UDP by proxying packets at the edge to applications running in one or more AWS Regions.
15. What is the advantage of running your AWS VPN connection through your Direct Connect connection over using the ordinary Internet?
	- Faster Performance, IMproved Security
		- It is likely that if you choose to run your VPN through a Direct Connect from your datacenter to the AWS network that your VPN connection will be both faster, and more secure. However data charges are still incurred whilst using Direct Connect. Additionally Transit Gateway attachments may be made to VPN regardless of if it is through DX or not.
16. Which of the following is true?
	- Security groups are stateful and Network Access Control Lists (NACLs) are stateless.
		- Security groups are stateful and Network Access Control Lists are stateless. Stateful means if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules.
17. When peering VPCs, you may peer your VPC only with another VPC in your same AWS account.
	- False
		- You may peer a VPC to another VPC that's in your same account, or to any VPC in any other account.
18. By default, EC2 instances in new subnets in a custom VPC can communicate with each other across Availability Zones.
	- True
		- In a custom VPC with new subnets in each AZ, there is a route within the route table that supports communication across all subnets/AZs. Additionally, it has a Default SG with an "allow" rule: all traffic, all protocols, all ports, from resource using this default security group.
19. When I create a new security group, all outbound traffic is allowed by default.
	- True
		- By default, a security group includes an outbound rule that allows all outbound traffic. You can remove the rule and add outbound rules that allow specific outbound traffic only. If your security group has no outbound rules, no outbound traffic originating from your instance is allowed.
20. At which of the following levels can VPC Flow Logs be created?
	- VPC Level, Subnet Level, Network Interface Level
		- VPC Flow Logs can be created at the VPC, subnet, and network interface levels. VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
21. A VPN connection consists of which of the following components?
	- Customer Gateway, Virtual Private Gateway
		- A Virtual Private Gateway sits at the edge of your VPC and is a key component when using a VPN. It's responsible for site-to-site connection from on-premises to a VPC. A customer gateway is a resource that is installed on the customer side and provides a customer gateway inside a VPC.
22. You have five VPCs in a 'hub and spoke' configuration, with VPC 'A' in the center and individually peered with VPCs 'B', 'C', 'D', and 'E', which make up the spokes. There are no other VPC connections. Which of the following VPCs can VPC 'B' communicate with directly?
	- VPC 'A'
		- As transitive peering is not allowed, VPC 'B' can communicate directly only with VPC 'A'.
23. True or False: A subnet can span multiple Availability Zones.
	- False
		- Each subnet must reside entirely within one Availability Zone and cannot span across zones.
24. Which of the following is a chief advantage of using VPC gateway endpoints to connect your VPC to services such as S3 and DynamoDB?
	- VPC gateway endpoints ensure traffic between your VPC and the other service does not leave the Amazon network.
		- VPC gateway endpoints ensure traffic between your VPC and the other service does not leave the Amazon network.
25. True or False: An Application Load Balancer must be deployed into at least two Availability Zone subnets.
	- True
		- An Application Load Balancer must be deployed into at least two Availability Zone subnets.
26. How many Amazon VPCs are allowed per AWS account per AWS Region? (Before any support requests to increase the number).
	- 5
		- You can have up to five Amazon VPCs per AWS account per AWS Region, but you can place a support request to increase the number.
27. Security groups act like a firewall at the instance level, whereas `_________` are an additional layer of security that act at the subnet level. (Fill in the blank with the correct answer.)
	- Network ACLs
		- NACLs act on the subnet level, while security groups act on the instance level.
28. What is the purpose of an egress-only internet gateway?
	- Prevents IPv6 based internet resources to initiate a connection into a VPC
	- Allows VPC based IPv6 traffic to communicate to the internet
		- The purpose of an egress-only internet gateway is to allow IPv6 based traffic within a VPC to access the internet, whilst denying any internet based resources to connection back into the VPC.

29. Which of the following are true for security groups?
	- Security groups support "allow" rules only.
	- Security groups support "allow" rules only.
	- Security groups evaluate all rules before deciding whether to allow traffic.
		- Security groups control access at the instance-level (as they are associated with network interfaces), they support "allow" rules only, and they evaluate all rules before deciding whether to allow traffic into the instance(s). Security groups operate at the instance level (as they are associated with network interfaces), they support "allow" rules only, and they evaluate all rules before deciding whether to allow traffic.
		

