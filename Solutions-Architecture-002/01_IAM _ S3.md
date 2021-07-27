# Identity Access Management 101

# What is IAM
IAM allows you to manage users, groups, permissions and roles and their level of access to the AWS Console.

IAM offers the following features:
- Centralised control of your AWS Account
- Shared Access to your AWS account
- Granular Permissions
- Identity Federation (Including Active Directory, Facebook, Linkedin etc)
- Multifactor Authentication
- Provide Temporary access for users/devices and services where necessary
- Allows you to setup your own password rotation policy
- Integrates with many different AWS services
- Supports PCI DSC Compliance


### Users
End users such as people, employees of an organization, etc

### Groups
A colloection of users. Each user in the group will inherit the permissions of the group

### Policies
Policies are made up of documents, called policy documents. These documents are in JSON format, they give permissions as to what a USER/Group/Role is able to do

### Roles 
You create roles and then assign them to AWS Resources

---

# IAM Demo
- IAM is universal. It does not apply to regions at this time
- The "root account" is simply the account created when first setup your AWS Account. It has complete Admin Access.
- New Users have **NO permissions** when first created
- New Users are assigned **Access Key ID** & **Secret Access Keys** when first created
- **These are not the same as a password.** You cannot sue the Access key ID & Secret Access Key to Login in to the console. You can use this to access AWS view the APIs and Command Line, however.
- **You only get to view these once.** If you lose them, you have to regenerate them. So, have them in a secure location. 
- **Always setup Multifactor Authentication on your root account.**
- **You can create and customize your own password rotation policies**

When you go to IAM Console you can view the sign-in URL which can be customized.

![[firefox_EIfIVdS4JE.png]]

![[firefox_XSN06twLgH.png]]

![[firefox_97h7w8YIFj.png]]

![[firefox_iCMjtkhwSs.png]]

![[firefox_Oe03EPsBQY.png]]

You can also set a Password Policy 

![[firefox_9viIeceVuq.png]]

**Creating a Role**

![[firefox_rJyHYRFUQn.png]]

![[firefox_8rv7oEHc9R.png]]

# Creating a Billing Alarm
AWS Console --> CloudWatch --> Billing --> Create Alarm

![[firefox_FBhOVLipuo.png]]

Create New Topic --> Provide a name for topic --> enter email --> create topic 

![[firefox_8gQGFhWEfh.png]]

Next --> Enter Alarm Name --> Description --> Next

![[firefox_l6QRZgLX1b.png]]

Create Alarm

Go to Email and subscribe to the topic to enable it. Or else it will remain in pending state.

![[firefox_qN44GoCqb6.png]]

![[firefox_OtsrGnU5cq.png]]

![[firefox_cngzOOJFvW.png]]


# S3 
S3 provides developers and IT teams with secure, durable, highly-scalable object storage. Amazon S3 is easy to use, with a simple web services interface to store and retrieve any amount of data from anywhere on the web.

## So what is S3?

- S3 is a safe place to store your files. 
- It is Object-based storage
- The data is spread across multiple devices and facilities

**The basics of S3** are :
- S3 is **Object-based** - i.e. allows you to upload files
- Files can be from 0 Bytes to 5TB
- There is unlimited storage
- Files are stored in Buckets
	- Basically just a folder, in which you can place your files
- **S3 is a universal namespace.** That is, names must be unique globally.
	- https://somethingunique.s3.amazonaws.com
- When you upload a file to S3, you will receive a **HTTP 200 code** if the upload was successful

**S3 is Object based.** Think of objects just as files. Objects consist of the following:
- Key (This is simply the name of the object)
- Value (This is simply the data and is made up of a sequence of bytes)
- Version ID (Important for versioning)
- Metadata (Data about data you storing)
- Subresources
	- Access Control Lists
	- Torrents

## How does data consistency work for S3?
- Read after write consistency for PUTS of new Objects
- Eventual Consistency for overwrite PUTS and DELETES (can take some time to propogate)

In other words
- If you write a new file and read it immediately afterwards, you will be able to view that data
- If you update **AN EXISTING file** or delete a file and read it immediately, you may get the older version, or you may not. Basically changes to objects can take a little bit of time to propogate

**S3 has the following Guarantees from Amazon**
- Built for 99.99% availablility for the S3 platform
- Amazon Guarantee 99.9% availability
- Amazon guarantees 99.999999999% durability for S3 information (Remember 11 x 9x)

**S3 has the following features:**
- Tiered Storage Availablility
- Lifecycle Management
- Versioning
- Encryption
- MFA Delete
- Secure your data using **Access Control Lists** and **Bucket Policies**

**S3 Storage Classes**
1. S3 Standard 
	- 99.99% availability, 99.99999999% durability, stored redundantly across multiple devices in multiple facilities, and is designed to sustain the loss of 2 facilities concurrently
2. S3 - IA -- Infrequently Accessed
	- (Infrequently Accessed): For data that is acccessed less frequently, but requires rapid access when needed. Lower fee than S3, but you are charged a retrieval fee.
4. S3 One Zone - IA
	- For where you want a lower-cost option for infrequently accessed data, but do not require the multiple Availibility Zone data resilience
5. S3 - Intelligent Tiering
	- Designed to optimize costs by automatically moving data to the most cost-effective access tier, without performance impact or opertaional overhead
6. S3 Glacier
	- S3 Glacier is a secure, durable, and low-cost storage class for data archiving. You can reliably store any amount of data at costs that are competitive with or cheaper than on-premises solutions. Retrieval times configurable from minutes to hours
7. S3 Glacier Deep Archive
	- S3 Glacier Deep Archive is Amazon S3's lowest-cost storage class where a retrieval time of 12 hours is acceptable

### S3 Comparison

![[firefox_0hRlZ2M6Zd.png]]

## S3 Charges
**You are charged for S3 in the following ways:**

- Storage
- Requests
- Storage Management Pricing
- Data Transfer Pricing
- Transfer Acceleration
- Cross Region Replublication Pricing
	- Replicate objects in your buckets that are in US, to a bucket in Australia. Any object in US will automatically be replicated in Australia

**Amazon S3 Transfer Acceleration enables fast, easy and secure transfers of files over long distances between your end users and an S3 bucket. Transfer Acceleration takes advantage of Amazon CloudFront's globally distributed edge locations. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path.**

![[firefox_B411ybXQVG.png]]

#### Exam Tips 
- S3 is Object-based: i.e. allows you to upload files
- Files can be from 0 bytes to 5Tb
- There is unlimited storage
- Files are stored in buckets
- S3 is a universal namespace. names must be unique globally
	- https://uniquename.s3.amazonaws.com
- Not suitable to install an operating system on
	- only to store files
- Successful uploads will generate a HTTP 200 status code
- You can turn on MFA delete (Multi factor authentication)
- Key fundamentals
	- Key (name of object)
	- Value (the data and is made up of a sequence of bytes)
	- Version id (important for versioning)
	- Metadata (Data about data you are storing)
	- Subresources
		- access control lists
		- torrent
- Read and Write consistency for PUTS of new Objects 
- Eventual Consistency for overwrite PUTS and DELETES (can take sometime to propogate)
- Different Storage Classes
	- S3 Standard
	- S3 - IA
	- S3 One Zone - IA
	- S3 - Intelligent Tiering
	- S3 Glacier
	- S3 Glacier Deep Archive
- Read the S3 FAQs 

# Creating S3 Bucket - Demo 
AWS Console --> S3 (under Storage) --> Create Bucket --> Enter Bucket Name --> Create Bucket

![[firefox_eXJThXRZkz.png]]

#### Exam Tips 
- Bucket names share a common name space. You cannot have the same bucket name as someone else.
- When you view your bucket you view them globally but you can have buckets in individual regions
- You can replicate the contents of one bucket to another bucket automatically by using cross region replication
- You can change storage classes and encryption of your objects on the fly
- Restricting Bucket Access
	- Bucket Policies - Applies across the whole bucket
	- Object Policies - Applies to individual files
	- IAM policies to users and groups - applies to users and Groups

#### S3 Transfer Acceleration
Users all around the world can upload their files to their edge locations and those edge locations can use the Amazon backbone network to upload the files to the bucket in your region. 


## S3 Pricing Tiers 
### What Drives the price?

**What makes up the cost of S3?**
- Storage (by the gig)
- Requests and data retrievals
- Data transfer
- Management & Replication

**What are the different Tiers?**
1. S3 Standard
2. S3 - IA
3. S3 One Zone - IA 
4. S3 - Intelligent Tiering
5. S3 Glacier
6. S3 Glacier Deep Archive 

![[firefox_yPNF1razea.png]]

![[firefox_ESi0zRQZqO.png]]

#### Exam Tips
- Understand how to get the best value out of S3 
	1. S3 Standard
	2. S3 - IA
	3. S3 - Intelligent Tiering
	4. S3 One Zone - IA
	5. S3 Glacier
	6. S3 Glacier Deep Archive 

## S3 Security and Encryption 
### The Basics 
By default, all newly created buckets are **PRIVATE.** You can setup access control to your buckets using; 
- Bucket Policies
- Access Control Lists 

S3 buckets can be configured to create access logs which log all requests made to the S3 bucket. This can be sent to another bucket and even another bucket in another account. 

**Encryption In Transit in achieved by**
- SSL/TLS

**Encryption At Rest (Server Side) is achieved by**
- S3 Managed Keys - SSE-S3 
- AWS Key Management Service, Managed Keys - SSE-KMS
- Server Side Encryption With Customer Provided Keys - SSE-C
**Client Side Encryption**

**Encryption our objects**
AWS Console --> S3 --> Select your bucket --> Select Object --> Server Side Encryption Settings --> Edit --> Enable --> Save Changes 

**Confirm Encryption**
Go to same object --> scroll to Server side encryption settings --> Encryption should be enabled

![[firefox_pXYeHOk5cG.png]]

![[firefox_3L0mB35nlm.png]]

**Encrypting the Entire Bucket**
AWS Console --> S3 --> Choose the bucket --> Properties --> Default Encryption --> Edit --> Enable --> Save Changes 

Now every object in this bucket will be encrypted

![[firefox_bKNMEpAAf3.png]]

## S3 Versioning 
Using **Versioning** With S3: 
- Stores all versions of an object (including all writes and even if you delete an object)
- Great backup tool
- Once enabled, **Versioning cannot be disabled**, only suspended
- Integrates with **Lifecycle** rules
- Versioning's **MFA Delete** capability, which uses multi-factor authentication, can be used to provide an additional layer of security

### Se Versioning Demo 

AWS Console --> S3 --> Create Bucket --> Name the bucket --> for now we can unrestrict public acces --> enable versioning --> Create Bucket 

Select Bucket --> upload --> select files --> upload 

Make the file public 
select the file --> actions --> make public --> make public 

view object
select object --> click object url to view in browser

Make changes to the file

Upload another file (same file with the changes)

Make another change to file on local computer and upload the file

- Now in the bucket enable list versions
	- And this should show all the different versions of the file
- Each version has to be made public explicitly. 
- If you delete an object, it will delete the object, but if you enable the list versions; you can still view the other versions 

#### Exam Tips 
- Stores all versions of an object (including all writes and even if you delete an object)
- Great backup tool
- Once enabled, Versioning cannot be disabled, only suspended
- Integrates with Lifecycle rules
- Versioning's MFA Delete capability, which uses multi-factor authenticationm can be used to provide an additional layer of security

## Lifecycle Manage With S3 

AWS Console --> S3 --> Choose Bucket --> Management --> Create Life Cycle Rule --> Name the rule --> apply to all objects --> select all rule actions needed --> define your transitions --> create rule 

![[firefox_HfWhoNVaA8.png]]
![[firefox_9yvmn8GzUI.png]]

Can view an overview of the lifecycle at the bottom

![[firefox_iE3vBMoF9N.png]]

![[firefox_OJW5UGfj9I.png]]

#### Exam Tips 
- Remember what lifecycle management is
	- Automates moving your objects between the different storage tiers
	- Can be used in conjunction with versioning
	- Can be applied to current versions and previous versions

## S3 Object Lock and Glacier Vault Lock

### S3 Object Locks 

You can use S3 object lock to store objects using a **write once, and read many (WORM)** model. It can help you prevent objects frorm being deleted or modified for a fixed amount of time or indefinitely. 

You can use **S3 Object Lock** to meet regulatory requirements that require WORM storage, or add an extra layer of protection against object changes and deletion. 


**Different Modes**
- Governance Mode
	- In governance mode, users can't overwite or delete an object version or alter its lock settings unless they have special permissions. With governance mode, you protect objects against being deleted by most users, but you can still grant some users permission to alter the retention settings or delete the object if necessary. 
- Compliance Mode
	- In compliance mode, a protected object version can't be overwritten or deleted by any user, including the root user in your AWS account. When an object is locked in compliance mode, its retention mode can't be changed and its retention period can't be shortened. Compliance mode ensures an object version can't be overwritten or deleted for the duration of the retention period. 

**Retention Periods**

A retention period protects an object version for a fixed amount of time. When you place a retention period on an object version, Amazon S3 stores a timestamp in the object version's metadata to indicate when the retention period expires. After the retention period expires, the object version can be written or deleted unless you also placed a legal hold on the object version. 

**Legal Holds**

S3 Object Lock also enables you to place a legal hold on an object version. Like a retention period, a legal hold prevents an object version from being overwritten or deleted. However, a legal hold doesn't have an assocaited retention period and remains in effect until removed. Legal holds can be freely placed and removed by any user who has the `s3:PutObjectLegalHold` permission.

### Glacier Vault Lock

S3 Glacier Vault Lock allows you to easily deploy and enforce compliance controls for individual S3 Glacier vaults with a Vault Lock policy. You can specify controls, such as WORM, in a Vault Lock policy and lock the policy from future edits. Once locked, the policy can no longer be changed.

### Exam Tips
- Use S3 Object Lock to store objects using a write once, read many (WORM) model
- Object locks can be on indiviual objects or applied across the bucket as a whole
- Object locks come in two modes: **governance mode** and **compliance mode**
	- With governance mode, users can't overwrite or delete an object version or alter its lock settings unless they hace special permissions
	- With compliance mode, a protected object version can't be overwritten or deleted by any user, including the root user in your AWS account
- S3 Glacier Vault Lock allows you to easily deploy and enforce compliance controls for individual S3 Glacier vaults with a Vault Lock policy. You can specify controls such as WORM in a Vaul Lock policy and lock the policy from future edits. Once locked, the policy can no longer be changed. 

## S3 Performance

**What are S3 Prefixes?**
- S3 Prefix
`mybucketname/folder1/subfolder1/myfile.jpg -> /folder1/subfolder1`
`mybucketname/folder2/subfolder1/myfile.jpg -> /folder2/subfolder1`
`mybucketname/folder3/myfile.jpg -> /folder3`
`mybucketname/folder4/subfolder4/myfile.jpg -> /folder4/subfolder4`

**S3 Performance**

S3 has extremely low latency. You can get the first byte out of S3 within 100-200 milliseconds.

You can also achieve a high number of requests: 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix.

1. You can get better performance by spreading your reads across different prefixes. For example, if you are using two prefixes, you can achieve 11,000 requests per second. 
2. If we used all four prefixes in the example, you would achieve 22,000 requests per second
	- More prefixes we have, the better performance we can achieve

**S3 Limitations when using KMS**

- If you are using SSE-KMS to encrypt your objects in S3, you must keep in mind the KMS limits.
- When you upload a file, you will call `GenerateDataKey` in the KMS API
- When you download a file, you will call `Decrypt` in the KMS API

- Uploading / downloading will count toward the KMS quota
- Currently, you cannot request a quota increase for KMS
- Region-specific, however, it's either 5,500, 10,000, or 30,000 requests per second

**S3 Performance: Uploads**
- Multipart Uploads
	- Recommended for files over 100Mb
	- Required for files over 5GB
	- Parallelize uploads (increase efficiency)

![[firefox_8R23Lt0SGC.png]]

**S3 Performance: Downloads**
- S3 Byte-Range Fetches
	- Parallelize downloads by specifying byte ranges
	- If there's a failure in the download, it's only for a specific byte range

![[firefox_5L3FGPMoC5.png]]

**S3 Byte-Range Fetches**

-  Can be used to speed up downloads
- Can be used to just download partial amounts of the file (e.g., header information)

### Examp Tips 
- Understand Prefixes: `mybucketname/folder1/subfolder1/myfile.jpg > /folder1/subfolder1`
- You can also achieve a high number of requests: 3500 PUT/COPY/POST/DELETE and 5500 GET/HEAD requests per second per prefix
- You can get better performance by spreading your reads across different prefixes. For example, if you are using two prefixes, you can achieve 11000 requests per second

**If you are using SSE-KMS to encrypt your objects in S3, you must keep in mind the KMS limits. **

- Uploading / downloading will count toward the KMS quota
- Region-specific, however, it's either 5500, 10000, or 30000 requests per second.
- Currently, you cannot request a quote increase for KMS

**Use Multipart Uploads**

- Use multipart uploads to increase performance when uploading files to S3
- Should be used for any files over 100MB and must be used for any files over 5GB
- Use S3 byte-range fetches to increase performance when downloading files to S3

## S3 Select & Glacier Select 

### What is S3 Select ? 
 
 S3 Select enables applications to retrieve only a subset of data from an object by using simple SQL expressions. By using S3 Select to retrieve only the data needed by your application, you can achieve drastic performance increases - in many cases, you can get as much as a 400% improvement
 
 ![[firefox_sM8AkOJ7dV.png]]
 
 Let's assume all your data is stored in S3 in zip files that contain CSV files. Without S3 Select, you would need to download, decompress, and process the entire CSV to get the data you needed.
 
 With S3 Select, you can use a simple SQL expression to return on the data from the store you're interested in instead of retrieving the entire object. This means you're dealing with an order of magnitude less data, which improves the performance of your underlying applications.
 
 
 ### Glacier Select 
 
 Some companies un highly regulated industries -- e.g., financial services, healthcare, and others -- write data directly to Amazon Glacier to satisy compliance needs like SEC Rule 17a-4 or HIPAA. Many S3 users have lifecycle policies designed to save on storage costs by moving their data into Glacier when they no longer need to access it on a regular basis
 
 **Glacier Select allows you to run SQL queries against Glacier directly**
 
 ### Exam Tips 
 - Remember that S3 select us used to retrience only a subset of data from an object by using simple SQL expressions
 - Get data by rows or columns using simple SQL expressions
 - Save money on data transfer and increase speed

## AWS Organizations & Consolidated Billing

**What is AWS Organizations ?**
>AWS Organizations is an account management service that enables you to consolidate multiple AWS accounts into an organization that you create and centrally manage

![[firefox_rmptUnVnNO.png]]
**OU** --> Organizational Unit such as HR, Accounting, Marketing, etc

### Consolidated Billing 

![[firefox_dTu1n0aQX3.png]]

- Paying account is independent. Cannot access resources of the other accounts.
- All linked accounts are independent. 

**Advantages of Consolidated Billing**
- One bill per AWS account
- Very easy to track charges and allocate costs
- Volume pricing discount

### Follow

AWS Console --> AWS Organizations --> Create Organization --> This will make you the root account for the organization

**To Invide other accounts to the organization**
Go to Organization --> Add Account --> invite Account --> Enter email of account to be invited --> invite 

**Accepting Invitation to organization**
log into AWS Console --> Organizations --> Accept the link to the invitation

### Exam Tips
- Always enable multi-factor authentication on root account
- Always use a strong and complex password on root account
- Paying account should be used for billing purposes only. Do not deploy resources into the paying account
- Enable / Disable AWS services using Service Control Policies (SCP) either on OU or on individual accounts 

## Sharing S3 Buckets Across Accounts 

**Need to have an organization with multiple accounts**

### S3 - Cross Account Access 

3 Different ways to share S3 buckets across accounts 

- Using Bucket Policies & IAM (applies across the entire bucket). Programmatic Access Only
- Using Bucket ACLs & IAM (individual objects) . Programmatic Access Only
- Cross-account IAM Roles. Programmatic AND Console access

![[firefox_7hnLWlPgVO.png]]

AWS Console --> IAM --> Roles --> Create Role --> Another AWS Account --> Enter the account ID you want to create this role for --> Select the policies --> Provide a name for the role --> Create Role 

**The other account**
AWS Console --> IAM --> Create new User --> Select AWS Management Console Access --> Custom Password --> Add User to Group --> Select Administrator Access or other policies --> provide it a name --> Next --> Create User

**Sign in with the created user**
In AWS login --> Enter in the IAM user credentials --> From this account we can switch roles by simply providing the role name or account, role name alone is enough

### Exam Tips 
- 3 Different ways to share S3 buckets across accounts
	- using bucket policies & IAM (applies across the entire bucket). Programmatic Access Only
	- Using Bucket ACLs & IAM (individual objects). Programmatic Access Only
	- Cross-account IAM Roles. Programmatic AND Console Access

## Cross Region Replication - Demo

AWS Console --> S3 --> Create Bucket --> Provide name --> Select a different Region --> unselect block all public --> acknowledge --> disable versioning --> create bucket 

Select the Original Bucket --> Management --> Create Replication Rule --> give it a name --> Choose an IAM Role OR Create new role --> Select "This rule applies to all object in the bucket" --> Choose a bucket in this account --> select the bucket created in previous steps --> Enable bucket replication --> Select Change the storage class for the replicated objects --> We will keep it Standard IA --> Save

That created the replication rule, but it's not yet replicated across the bucket in the other region. 

Replication Rules only start working the moment you turn it on, it doesn't take the objects already in the bucket and replicate it; rather it replicates all the objects created after the replication and replicates it across the region. 

The permissions of any newly uploaded files in the bucket will also replicate across the regions 

### Exam Tips 
- Versioning must be enabled on both the source and destination buckets
- Files in an existing bucket are not replicated automatically
- All subequent updated files will be replicated automatically
- Delete markers are not replicated
- Deleting individual versions or delete markers will not be replicated
- Understand what Cross Region Replication is at a high level

## S3 Transfer Acceleration 
**What is S3 Transfer Acceleration**

S3 Transfer Acceleration utlises the CloudFront Edge Network to accelerate your uploads to S3. Instead of uploading directly to your S3 bucket, you can use a distinct URL to upload directly to an edge location which will then transfer that file to S3. You will get a distinct URL to upload to: `someaccount.s3-accelerate.amazonaws.com`

![[firefox_0oafaxBAiY.png]]

## AWS DataSync 

DataSync essential allows you to move large amounts of data into AWS. 

DataSync basically allows you to move large amounts of data into AWS, and typically use it on your, on premise data center.
You would stall the data sync agent as an agent and you would do this on a server that connects to your NAS or file system and that will then copy data to AWS and write data from AWS.

So it's a way of synchronizing your data and it automatically encrypts your data
and accelerates transfer over the wide area network. It performs automatic data integrity checks in transit and at rest as well and essentially what it does is it seamlessly connects to Amazon S3, EFS, or Amazon FSX for Windows file server to copy data and metadata to and from AWS.

It's a way of syncing your data to AWS.

![[firefox_kx0LQq8MZK.png]]


### Exam Tips
- You need to know that it's used to move large amounts of data from on-premise to AWS.  
- It's used with NFS and SMB compatible file systems.
- Replication can be done hourly, daily, or weekly
- you set it up is you just install the data sync agent to start the replication. 
- you can also use data sync to replicate EFS to EFS and so if you were doing it like that, essentially you install the data sync agent on an EC2 instance that was connected to it, EFS and then you can use that data sync agent to replicate your EFS to a, another copy or another EFS in the cloud as well.


## CloudFront 

**What is Cloud Front?**
> A content delivery network (CDN) is a system of distributed servers (network) that deliver webpages and other web content to a user based on the geographic locations of the user, the origin of the webpage, and a content delivery server.

**Without CloudFront**

![[firefox_7pCLN7PFgw.png]]

**CloudFront - Key Terminology**
- Edge Location - This is the location where content will be cached. This is separate to an AWS Region / AZ 
- Origin - This is the origin of all the files that the CDN will distribute. This can be an S3 Bucket, an EC2 Instance, an Elastic Load Balancer, or Route53.
- Distribution - This is the name given the CDN which consists of a collection of Edge Locations 
- Web Dsitribution - Typically for Websites
- RTMP - Used For Media Streaming

**With CloudFront**
![[firefox_JZbRK9zY5M.png]]

**CloudFront**
> Amazon CloudFront can be used to deliver your entire website, including dynamic, static, streaming, and interactive content using a global network of edge locations. Requests for your content are automatically routed to the nearest edge location, so content is delivered with the best possible performance.

- EdgeLocations are not just READ only - you can write to them too. (ie put an object on to them).
- Objects are cached for the life of the TTL (Time to Live)
- You can clear cached objects, but you will be charged
	- Called invalidating the cache


## Create A CloudFront Distribution - Follow 
AWS Console --> CloudFront --> Create Distribution --> We can choose Web for this example --> Select an origin domain name --> Restricting bucket access - basically means users won't be able to access the content through the bucket, rather through the cloudfront url - we can keep this off for now --> Create Distribution 

Copy the domain name url of the distribution --> Go to S3 --> select the S3 Bucket 

**Creating Invalidations**
Select the Distribution --> Invalidation Tab at the top --> Create Invalidation --> We can invalidate invidual objects, directories, files, etc --> create invalidation --> and it won't be on the edge location 


**Delete Distribution** 
- First make sure to disable the distribution - can take around 15 minutes or more
- Once it is disabled you can select the distribution and delete it

### Exam Tips 
- Edge Location - This is the location where content will be cached. This is separate to an AWS Region / AZ 
- Origin - This is the origin of all the files that the CDN will distribute. This can be an S3 Bucket, an EC2 Instance, an Elastic Load Balancer, or Route53.
- Distribution - This is the name given the CDN which consists of a collection of Edge Locations 
- Web Dsitribution - Typically for Websites
- RTMP - Used For Media Streaming

- EdgeLocations are not just READ only - you can write to them too. (ie put an object on to them).
- Objects are cached for the life of the TTL (Time to Live)
- You can invalidate cached objects, but you will be charged
- You can clear cached objects, but you will be charged
	- Called invalidating the cache

## CloudFront Signed URLs Cookies

**Short Review **

![[firefox_h2Wof1zyce.png]]

CloudFront is great for restricting content for paid users. 

**Use CloudFront Signed URLs or Signed Cookies**
1. A signed URL is for individual files. 1 file = 1 URL
2. A signed cookie is for multiple files. 1 cookie = multiple files

**When we create a signed URL or signed cookie, we attach a policy**
- The policy can include: 
	- URL expiration
	- IP ranges
	- Trusted signers (which AWS accounts can create signed URLs)

**OAI** - Origin Access Identity

![[firefox_jlH4UwxnLt.png]]

**CloudFront Signed URL**
- Can have different origins. Does not have to be EC2
- Key-pair account wide and amanged by the root user
- Can utilize caching features
- Can filter by date, path, IP address, expiration, etc

![[firefox_TLfAyT2TZn.png]]

**S3 Signed URL**
- Issues a request as the IAM user who creates the presigned URL
- Limited Lifetime 

![[firefox_QvD1Svkmf2.png]]

### Exam Tips 
- Use signed URLs/cookies when you want to secure content so that only the people you authorize are able to access it
- A signed URL is for individual files. 1 file = 1 URL
- A signed cookie is for multiple files. 1 cookie = multiple files
- If your origin is EC2, then use the CloudFront
- If your origin is a single file, use S3 signed url

## Snowball

**What is Snowball?**

Snowball is a petabyte-scale data transport solution that uses secure appliances tto transcer large amounts of data into and out of AWS. Using Snowball addresses common challenges with large scale data transferrs including high network costs, loong transfer times, and securit concerns. Ttransferring data witth Snowball is simple, fast, secure, and can be as little as one-fiifth the cost of high-speed internet

Snowball comes in either a 50TB or 80TB size. Snowball uses multiple layers of security designed to protect your data including tamper-resistant enclosures, 256-bit encryption, and an industry-standard Trusted Platform Module (TPM) designed to ensure both security and full chain-of-custody of your data. Once the data transfer job has been processed and verified, AWS performs a software earser of the Snowball appliance.

AWS Snowball Edge is a 100TB data transfer device with on-board storage and compute capabilities. You can use Snowball Edge to move large amounts of data into and out of AWS, as a temporary storage tier for large local datasets, or to support local workloads in remote or offline locations. 

Snowball Edge connects to your existing applications and infrastructure using standard storage interfaces, streamlining the data transfer process and minimizing setup and integration. Snowball Edge can cluster together to form a local storage tier and process your data on-premises, helping ensure your applications continue to run even when they are not able to access the cloud.

AWS Snowmobile is an Exabyte-scale data transfer service used to move extremely large amouts of data to AWS. You can transfer up to 100PB per Snowmobile, a 45-foot long ruggedized shipping container, pulled by a semi-trailer truck. Snowmobile makes it easy to move massive volumes of data to the cloud, including video libraries, image repositories, or even a complete data center migration. Transferring data with Snowmobile is secure, fast and cost effective.

**When to consider using Snowball**

![[firefox_Wmvdi1UL4g.png]]

### Exam Tips 
- Understand what Snowball is
- Snowball can
	- Import to S3
	- Export from S3 


## Storage Gateway 

**What is Storage Gateway**

AWS Storage Gateway is a  service thhat connects an on-premises software appliance with cloud-bassed storage to provide seamless and secure integratiion between an organization's on-premises IT environment and AWS's storage infrastructure. The service enables you to securely store data to the AWS cloud for scalable and cost-effective storage

- A virtual or physical device that will replicate your data into AWS 

![[firefox_GEKjWOTpcY.png]]

AWS Storage Gateway's software appliance is available for download as a virtual machine (VM) iimage that you install on a host in your datacenter. Storage Gateway supports either VMware ESXi or Microsoft Hyper-V. Once you've installed your gateway and associated it with your AWS account through the activation process, you can use the AWS Management Console to create the storage gateway option that is right for you

**The three different types of Storage Gateway are as follows:**
- File Gateway (NFS & SMB)
- Volume Gateway (iSCSI)
	- Stored Volumes
	- Cached Volumes 
- Tape Gateway (VTL)

- **File Gateway**
	- Files are stored as objects in your S3 buckets, accessed through a Network File System (NFS) mount point. Ownership, permissions, and timestamps are durably stored in S3 in the user-metadata of the object associated with the file. Once objects are transferred to S3, they can be managed as native S3 objects, and bucket policies such as versioning, lifecycle management, and cross-region replication apply directly to objeects stored in your bucket
	-	![[firefox_hNv03lrmOi.png]]
- **Volume Gateway**
- The volume interface presents your applications with disk volumes using the iSCSI block protocol.
- Data written to these volumes can be asynchronously backed up as point-in-time snapshots of your volumes, and stored in the cloud as Amazon EBS snapshots
- Snapshots are incremental backups that capture only changed blocks. All snapshot storage is also compressed to minimize your storage charges
	- **Stored Volumes**
		- Stored volumes let you store your primary data locally, while asynchronously backing up that data to AWS. Stored volumes provide your on-premises applications with low-latency access to their entire datasets, while providing durable, off-site backups. You can create storage volumes and mount them tas iSCSI devices from your on-premises application servers. Data written to your stored volumes is stored on your on-premises storage hardware. This data is asynchronously backed up to Amazon Simple Storage Service (Amazon S3) in the form of Amazon Elastic Block store (Amazon EBS) snapshots. 1GB - 16TB in size for Stored Volumes
		- ![[firefox_P6NwmBF43b.png]]
	-	**Cached Volumes**
		-	Cached volumes let you use Amazon Simple Storage Service (Amazon S3) as your primary data storage while retaining frequently accessed data locally in your storage gateway. Cached volumes minimize the need to scale your on-premises storage infrastructure, while still providing your applications with low-latency access to their frequently accessed data. You can create storage volumes up to 32 TB in size and attach to them as iSCSI devices from your on-premises application servers. Your gateway stores data that you write to these volumes in Amazon S3 and retains recently read data in your on-premises storage gateway's cache and upload buffer storage. 1GB - 32TB in size for Cached Volumes
		-	![[firefox_e4k54jDWZB.png]]
- **Tape Gateway (VTL)**
	- Tape Gateway offers a durable, cost-effective solution to archive your data in the AWS Cloud. The VTL interface it provides lets you leverage your existing tape-based backup application infrastructure to store data on virtual tape cartridges that you create on your tape gateway. Each tape gateway is preconfigured with a media changer and tape drives, which are available to your existing client backup applications as iSCSI devices. You add tape cartridges as you need to archive your data. Supported by NetBackup, Backup Exec, Veeam etc.
	- ![[firefox_ApjdeaOUGF.png]]

### Exam Tips 
- File Gateway 
	- For flat files, stored directly on S3
- Volume Gateway
	- Stored Volumes
		- Entire Dataset is stored on site and is asynchronously backed up to S3
	- Cached Volumes
		- Entire DAtaset is stored on S3 and the most frequently accessed data is cached on site
- Gateway Virtual Tape Library - can also get physical tape library

## Athena vs Macie

**What is Athena?**
Interactive query service which enables you to analyse and query data located in S3 using standard SQL

- Serverless, nothing to provision, pay per query / per TB scanned
- No need to set up complex Extract / Transform / Load (ETL)
- Works directly with data stored in S3 

**Athena Use Cases**

What can Athena be Used For?

- Can be used to query log files stored in S3, e.g. ELB logs, S3 access logs etc
- Generate business reports on data stored in S3
- Analyse AWS cost and usage reports
- Run queries on click-stream data

**What is Macie?**

What is PII (Personally Identifiable Information) ?

- Personal data used to establish an individual's identity 
- This data could be exploited by criminals, used in identity theft and financial fraud
- Home address, email address, SSN
- Passport number, driver's license number
- D.O.B, phone numer, bank account, credit card number

Security service which uses Machine Learning and NLP (Natural Language Processing) to discover, classify and protect sensitive data stored in S3 

- Uses AI to recognise if your S3 objects contain sensitive data such as PII
- Dashboards, reporting and alerts
- Works directly with data stored in S3
- Can also analyze CloudTrail logs
- Great for PCI-DSS and preventing ID theft

### Exam Tips
- Athena Exam Tips
	- Remember what Athena is and what it allows you to do:
		- Athena is an interactive query service
		- It allows you to query data located in S3 using standard SQL 
		- Serverless
		- Commonly used to analyse log data stored in S3
- Macie Exam Tips 
	- Remember what Macie is and what it allows you to do:
		- Macie uses AI to analyze data in S3 and helps identify PII
		- Can also be used to analyse CloudTrail logs for suspicious API activity
		- Includes Dashboards, Reports and Alerting
		- Great for PCI-DSS compliance and preventing ID theft

## S3 and IAM Summary 

Identity Access Management Consists of the following:
- Users
- Groups
- Roles
- Policies

- IAM is universal. It does not apply to regions at this time
- The "root account" is simply the account created when first setup your AWS account. It has complete Admin access
- New Users have NO permissions when first created
- New users are assigned Access Key ID & Secret Access Keys when first created
- These are not the same as a password. You cannot use the Access key ID & Secret Access Key to Login in to the console. You can use this to access AWS via the APIs Command Line, however
- You only get to view these once. If you lose them, you have to regenerate them. So, save them in a secure location.

- Always setup Multifactor Authentication on your root account
- you can create and customise your own password rotation policies

**S3**

- Remember that S3 is object-based: i.e. allows you to upload files
- Files can be from 0 bytes to 5 TB
- There is unlimited storage
- File are stored in Buckets
- S3 is a universal namespace. That is, names must be unique globally
- `https://s3-eu-west-1.amaonaws.com/somename`

- Not suitable to install an operating system on
- Sucessful uploads will generate a HTTP 200 status code

By default all newly created buckets are PRIVATE. You can setup access control to your bucket using:
- Bucket Policies
- Access Control Lists

S3 buckets can be configured to create access logs which log all requests made to the S3 bucket. This can be sent to another bucket and even another bucket in another account

**The key fundamentals of S3 are**
- Key (this is simply the name of the object)
- Value (this is simply the data and is made up of a sequence of bytes)
- Version ID (Important for versioning)
- Metadata (Data about data you are storing)
- Subresources
	- Access Control Lists
	- Torrent

- Read after Write consistency for PUTS of new objects
- Eventual Consistency for overwrite PUTS and DELETES (can take some time to propogate)

**Understand Different S3 Classes of Storage**
1. S3 Standard
2. S3 - IA
3. S3 One Zone - IA
4. S3 - Intelligent Tiering
5. S3 Glacier
6. S3 Glacier Deep Archive

![[firefox_2kZpFpUTNL.png]]

**Understand how to get the best value out of S3**
1. S3 Standard - Most expensive
2. S3 - IA - For infrequently accessed
3. S3 - intelligent Tiering - best bets if you arent using objects
4. S3 one zone - IA
5. S3 Glacier
6. S3 Glacier Deep Archive

**Encryption in Transit is achieved by**
- SSL/TLS

**Encryption At Rest (Server Side) is achieved by :**
- S3 Managed Keys - SSE-S3
- AWS Key Management Service, Managed Keys - SSe-KMS
- Server Side Encryption With Customer Provided Keys - SSE-C

**Client Side Encryption**

### Some Best Practices with AWS Organizations :
- Always enable multi-factor authentication on root account
- Always use a strong and complex password on root account
- Paying account should be used for billing purposes only. Do not deploy resources into the paying account
- Enable/Disable AWS services using Service Control Policies (SCP) either on OU or on individual accounts 

### 3 Different ways to share S3 buckets across accounts
- Using Bucket policies & IAM (applies across the entire bucket).  Programmatic access only
- Using Bucket ACLs & IAM (individual objects). Programmatic Access Only
- Cross-account IAM Roles. Programmatic AND  Console Access

**Cross Region Replication**
- Versioning must be enabled on both the source and the destination buckets
- Files in an existing bucket are not replicated automatically
- All subsequent updated files will be replicated automatically
- Delete markers are not replicated
- Deleting individual versions or delete markers will not be replicated
- Understand what Cross Region Replication is at a high level

### Lifecycle Policies
- Automates moving your objects between the different storage  tiers
- Can be used in conjunction with versioning
- can be applied to current versions and previous versions

### Transfer Acceleration 
Users around the world will upload their files to their edge locations and AWS will upload those files to our bucket

**CloudFront**
- Edge Location - This is the location where content will be cached. This is separate an AWS Region/AZ
- Origin - This is the origin of all the files that the CDN will distribute. This can be either an S3 bucket, an EC2 instance, an Elastic Load balancer, or Route53
- Distribution - This is the name given the CDN which consists of a collection of Edge Locations
- Web Distribution - Typically used for Websites
- RTMP - Used for Media Streaming

- Edge locations are not just READ only - you can write to them too. i.e. put and object on to them
- Objects are cached for the life of the TTL (Time To Live)
- You can clear cached objects, but you will be charged

### Snowball 
- Understand what Snowball is
- Snowball can
	- import to S3
	- export from S3 

### Storage Gateway 
- File Gateway 
	- For flat files, stored directly on S3
- Volume Gateway
	- Stored Volumes
		- Entire Dataset is stored on site and is asynchronously backed up to S3
	- Cached Volumes
		- Entire DAtaset is stored on S3 and the most frequently accessed data is cached on site
- Gateway Virtual Tape Library - can also get physical tape library

### Athena and Macie
- Athena Exam Tips
	- Remember what Athena is and what it allows you to do:
		- Athena is an interactive query service
		- It allows you to query data located in S3 using standard SQL 
		- Serverless
		- Commonly used to analyse log data stored in S3
- Macie Exam Tips 
	- Remember what Macie is and what it allows you to do:
		- Macie uses AI to analyze data in S3 and helps identify PII
		- Can also be used to analyse CloudTrail logs for suspicious API activity
		- Includes Dashboards, Reports and Alerting
		- Great for PCI-DSS compliance and preventing ID theft


Read the S3 FAQs before taking the exam

# Creating Static Website with S3 

![[firefox_VIhTog987Q.png]]

- configure a S3 bucket to for website hosting
- then upload website content to the bucket
- make sure the bucket is publicly accessed 


**Creating our bucket**
AWS Console --> S3 --> Create Bucket --> Name the bucket (must be globally unique) --> Choose the correct region --> uncheck block all public access --> Create bucket

**Uploading files to bucket**
Select the bucket --> upload --> add files -->  upload



**Make the files publicly visible**
select bucket --> permissions --> bucket policy --> define our policy --> for resource it should be define with the bucket ARN --> save changes 

Select bucket --> properties --> Static Website hosting --> Edit --> Enable --> enter the index.html, and error.html files names --> save changes

# AWS IAM Lab 
![[firefox_mJS7U4JdED.png]]

AWS Console --> IAM --> Groups --> Select Group --> Add Users --> Select all the users who should be in the group --> add user


Example of Inline Policies

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:Describe*",
                "ec2:StartInstances",
                "ec2:StopInstances"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": "elasticloadbalancing:Describe*",
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "cloudwatch:ListMetrics",
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:Describe*"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": "autoscaling:Describe*",
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

**View and edit permissions of a user**
IAM --> Users --> select user --> permissions --> expand the policy you want to edit, click on the dark triangle --> edit policy --> 
![[firefox_Rv4ggJU4IE.png]]

![[firefox_YIsvlHQX9G.png]]

- If we changed `Allow` to `Deny` --> this is known as an explicit deny. 
	- This is known as an explicit deny, and an explicit deny will always overwrite any allow
	- If there's a deny and a policy that applies to a user, then that deny will override any allow granted in any other policy 
	- By default all policies are implicitly denied until you allow it explicitly

# Quiz 

1. You are a solutions architect working for a large engineering company that are moving from a legacy infrastructure to AWS. You have configured the company's first AWS account and you have set up IAM. Your company is based in Andorra, but there will be a small subsidiary operating out of South Korea, so that office will need its own AWS environment. Which of the following statements is true?
	- You will need to configure Users and Policy Documents only once, as these are applied globally.
	- IAM is a Global service.  You can have regional conditions in policies, however by default users & policies are Global.
2. In what language are policy documents written?
	- JavaScript Object Notation - is a human readable and easily parsed structured data format used to pass blocks of data into & between systems. (JSON)
3. You are a developer at a fast-growing startup. Until now, you have used the root account to log in to the AWS console. However, as you have taken on more staff, you will need to stop sharing the root account to prevent accidental damage to your AWS infrastructure. What should you do so that everyone can access the AWS resources they need to do their jobs?
	- Create individual user accounts with minimum necessary rights and tell the staff to log in to the console using the credentials provided.
	- Create a customized sign-in link such as "yourcompany.signin.aws.amazon.com/console" for your new users to use to sign in with.
	- Read the AWS Security Best Practice white paper. Also note that the IAM account signin URL is different from the Root account signin URL
4. Which of the following is not a feature of IAM?
	- AWS makes use of Accounts & Passwords, or Keys and Secret keys, and MFA, to prove identity. You may have a 3rd party device that uses BioMetrics to initiate and exchange of the password or secret key with AWS, but that is not an AWS / IAM service. [https://aws.amazon.com/iam/features/](https://aws.amazon.com/iam/features/ "null")
	- NOT A FEATURE
		- IAM allows you to set up biometric authentication, so that no passwords are required.
5. You are a security administrator working for a hotel chain. You have a new member of staff who has started as a systems administrator, and she will need full access to the AWS console. You have created the user account and generated the access key id and the secret access key. You have moved this user into the group where the other administrators are, and you have provided the new user with their secret access key and their access key id. However, when she tries to log in to the AWS console, she cannot. Why might that be?
	- You cannot log in to the AWS console using the Access Key ID / Secret Access Key pair. Instead, you must generate a password for the user, and supply the user with this password and your organization's unique AWS console login URL.
6. What is an additional way to secure the AWS accounts of both the root account and new users alike?
	- MFA provides an additional requirement for the person signing on to prove that they are who they claim to be. Username & password are things you 'know' the MFA is something that you 'have'. e.g. you have the only device that can generate the token. [https://aws.amazon.com/iam/features/mfa/](https://aws.amazon.com/iam/features/mfa/ "null")
7. When you create a new user, that user **\_\_\_\_**.
	- Will be able to interact with AWS using their access key ID and secret access key using the API, CLI, or the AWS SDKs assuming programmatic access was enabled.
	- To access the console you use an account and password combination. To access AWS programmatically you use a Key and Secret Key combination
8. You have a client who is considering a move to AWS. In establishing a new account, what is the first thing the company should do?
	- Set up an account using their company email address.
		- This email address is a key part of linking the AWS account to your company. Using a private email address may make it harder to establish ownership if your need help from AWS. [https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/ "null") [https://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/](https://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/ "null") Explanation
9. Which statement best describes IAM?
	- IAM allows you to manage users, groups, roles, and their corresponding level of access to the AWS Platform.
		- Key concepts; Users, Groups, Roles, and the level of access. And "..to the AWS Platform
10. Every user you create in the IAM systems starts with **\_\_\_\_**.
	- No Permissions
		- AWS systems are designed to be secure 1st. The system administrator needs to add permissions to allow accounts to take actions.
11. Your company has launched a new app. To access the app files, the development team needs access to a bucket that is located within your team's AWS account. The development team is using a different account and requires programmatic and console level access to your team's S3 bucket. How would you share this bucket with the development team's account?
	- Setting up a cross account IAM Role
		- Setting up a cross account IAM role is currently the only method that will allow IAM users to access cross account S3 buckets both programmatically and via the AWS console.
12. What is the default level of access a newly created IAM User is granted?
	- No access to any AWS services.
		- By default new IAM Users have no permissions to AWS services. They must be explicitly granted.
13. Power User Access allows **\_\_\_\_**.
	- Access to all AWS services except the management of groups and users within IAM.
14. What level of access does the "root" account have?
	- Administrator Access
		- The _root_ account in an AWS account represents the Owner of the account and can do anything including changing billing details and even close the account. The details for this account should be locked away and only used when absolutely necessary. [https://docs.aws.amazon.com/IAM/latest/UserGuide/id\_root-user.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html "null")
15. Which of the following is not a component of IAM?
	- Organizational Units
		- 'OUs' are a feature of AWS Organizations [https://aws.amazon.com/iam/faqs/](https://aws.amazon.com/iam/faqs/ "null") [https://aws.amazon.com/organizations/features/](https://aws.amazon.com/organizations/features/ "null")
16. You have created a new AWS account for your company, and you have also configured multi-factor authentication on the root account. You are about to create your new users. What strategy should you consider in order to ensure that there is good security on this account.
	- Enact a strong password policy: user passwords must be changed every 45 days, with each password containing a combination of capital letters, lower case letters, numbers, and special symbols.
		- A password policy to set a minimum standard is good practice and is generally a top requirement for any industry compliance endorsement. [https://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/](https://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/ "null")
17. A new employee has just started work, and it is your job to give her administrator access to the AWS console. You have given her a user name, an access key ID, a secret access key, and you have generated a password for her. She is now able to log in to the AWS console, but she is unable to interact with any AWS services. What should you do next?
	- Grant her Administrator access by adding her to an Administrators' group.
		- By default new user accounts come with no permission to interact with services. These must be explicitly assigned by adding a Policy or adding them to a Group.
18. A ****\_\_**** is an object in AWS stored as a JSON document that provides a formal statement of one or more permissions.
	- Policy
		- A policy is an object in AWS that, when associated with an identity or resource, defines their permissions. Most policies are stored in AWS as JSON documents.

