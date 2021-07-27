# Using AWS Tags and Resource Groups in AWS

![[firefox_rUEJbUTYU9.png]]

AWS allows AWS customers to assign meta-data to their AWS resources in the form of tags. Each tag is basically a simple label; consisting of a custome defined key and an optional value, that makes it easier to manage search for and filter resources, by purpose owner environment and other criteria. 

Tags can be used for many purposes but without the use of tags, it can be difficult to manage your resources. 

AWS Console --> AWS Config --> 1 - click setup --> defaults are fine, confirm 
![[firefox_7cRrF1GR44.png]]


AWS Console --> EC2 --> running instances --> Need to create an AMI from one of these EC2 instances

![[firefox_cqFZU3PZvW.png]]

select an instance --> image and template --> create image --> give it a name "Base" is fine --> create image 

![[firefox_dSablpfA4V.png]]

It should now be available under the AMI in EC2 section

select the AMI "Base" --> launch --> t2.micro is fine --> Default is fine --> default is fine --> for tags, \[key => name, value => Test Web Server] --> Next --> select existing key group --> Next --> Launch --> Proceed without a key pair, don't need it because we won't be using SSH into the EC2 instance --> launch 
![[firefox_mioQTr79m2.png]]
AWS Console --> AWS Resource Groups & Tag editor --> Tag Editor --> select resource types from drop down --> AWS::EC2::Instance, and AWS::S3::Bucket for this lab --> search resources --> we can filter the seach results 

filter "mod1" --> select all --> clear the filter --> search for "moduleone" --> select the filters --> clear the filter --> Now we have all resources of module 1 selected
![[firefox_Dmh0ODc3ts.png]]

Manage tags of selected resources --> add tag --> \[key => Module, value => Starship Monitor] --> Review --> apply changes

![[firefox_HCgzkwFOYG.png]]

successfully applied those tag to all those resources for us

**Now we do the same thing but for module 2**

filter the search results --> "mod. 2" --> select the filters --> filter for "moduletwo" --> select the results --> clear the filters --> manage --> add tag \[key => Module, value => Warp Drive] --> review --> apply 

![[firefox_EZIEtk4hBu.png]]


**Now we can create a resource group with those tags**
Create Resource Group --> Tag based --> All supported resource types is fine --> under tags = Module, and for value = Starship Monitor --> Add --> preview group resources --> Provide the group a name --> Create Group 

![[firefox_kM91gfr5kA.png]]

Now we have a group where all that applies

**Now do it again for Warp Drive**

![[firefox_u5whUbiAIU.png]]

Any new resources you create with these tags will automatically appear in this resource group.

![[firefox_acWRriRhtL.png]]

For the next few steps we need the AMI id of the EC2 instance we created from AMI. We can get this id from the AMI section or by going to EC2 instances and selected the instance that was created using the AMI, and scrolling down to AMI id. 
![[firefox_fuIKugXtFS.png]]

copy that AMI ID

AWS Console -->  Config Console --> Create Rule --> AWS Managed Rule --> search for "ami" = select "approved-amis-by-id" --> Next --> Under trigger select Tags, \[key => Module, value => Starship Monitor] --> under parameters, \[key => anything you want, value => the AMI ID we copied] --> This will create a rule, that will find resources with the module tag thats set to starship monitor; and try to make sure that the ami id of that resource is set to this value. if not it will let us know. --> next --> add rule

![[firefox_lM7x4giOwa.png]]

![[firefox_jYxMipGwwp.png]]



# Creating and Working with an EC2 instance in AWS

EC2 is a core service within AWS. Its a service most get started with in AWS. It's an IaaS product of AWS, which allows you to create instances (virtual machines) running with AWS. Ulike other providers, EC2 benefits from a super class of supporting benefits; such as EBS, which provides high-performance storage, and VPC, which provides soft-defined networking. 

**Networking Configuration**
AWS Console --> VPC --> Your VPCs --> Actions --> Create DEfault VPC --> Create

**Creating the EC2**
AWS Console --> EC2 --> launch instance --> Amazon linux 2 is fine --> t2.micro is fine --> default configuration is fine, but for Network, make sure its the id of the vpc we created earlier, and subnet is enabled, under user data we can define our script
```bash
#!/bin/bash
yum update -y
yum install -y httpd
chkconfig httpd on
service httpd start
```
--> Next --> default storage is fine --> tags are optional --> \[key => Name, value => Webserver] --> Next --> for security group, create new group, name it LabSG, add another rule, HTTP, default is all ip, we can just set it to my ip --> review and launch --> create new keypair, call it Lab, and download the keypair --> launch instance

![[firefox_gDjV8wDofx.png]]

Head to EC2 --> wait for the instance to be completely setup --> copy the public ipv4 address dns --> enter it in a browser to check apache is running

Now SSH into the instance --> select the instance --> connect --> connect --> elevate user privilege `sudo su` --> 

# Using EC2 Roles and Instance Profiles in AWS 
Applications that run on an EC2 instance must include AWS credentials in their AWS API requests. 

Developers can store AWS credentials directly within the EC2 instance and allow applications in that instance to use those credentials. 

![[firefox_E8JYAeh92n.png]]

But then developers have to manage their credentials and ensure they securely pass the credentials to the instance and update the instance when it's time to rotate credentials. 

That's a lot of additional work.

An instance profile is an entity or a container that's used for connecting an IAM role to an EC2 instance. Instance profiles provide temporary credentials which are rotated automatically. 

AWS Console --> S3 --> use the bucket, that contains "bucketlookupfiles" in the name --> select the file --> action --> download --> this file contains the name and s3 access keys for this lab 

![[firefox_DqMG1C91FG.png]]

EC2 --> running instances --> bastion host --> copy the public ipv4 address 

![[firefox_W9XCtluOGe.png]]

**Now SSH into this EC2 instance**

![[firefox_dHQspsx4VA.png]]

`ssh cloud_user@3.90.246.95` --> `yes` --> `enter the password` --> configure the aws ec2 environment --> `aws configure` --> 

![[firefox_2TwO9JjDbn.png]]

next we need to create our trust policy, this is a policy thats going to allow the EC2 service to assume this role that we're about to create. 

So lets create a file: `nano trust_policy_ec2.json` --> now include this inside the json file we created
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Service": "ec2.amazonaws.com"
			},
			"Action": "sts.AssumeRole"
		}
	]
}
```

Now we need to actually create our role: 
`aws iam create-role --rolename DEV_ROLE --assume-role-role-policy-document file://trust_policy_ec2.json`

now we want to grant this role read access to one of our s3 buckets
we need to create an IAM policy that defines those permissions.

`nano dev_s3_read_access.json`

```json 
{
    "Version": "2012-10-17",
    "Statement": [
        {
          "Sid": "AllowUserToSeeBucketListInTheConsole",
          "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
          "Effect": "Allow",
          "Resource": ["arn:aws:s3:::*"]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": [
                "arn:aws:s3:::<DEV_S3_BUCKET_NAME>/*",
                "arn:aws:s3:::<DEV_S3_BUCKET_NAME>"
            ]
        }
    ]
}
```



---

# Using EC2 Roles and Instance Profiles

## Introduction

AWS Identity and Access Management (IAM) roles for Amazon Elastic Compute Cloud (EC2) provide the ability to grant instances temporary credentials. These temporary credentials can then be used by hosted applications to access permissions configured within the role. IAM roles eliminate the need for managing credentials, help mitigate long-term security risks, and simplify permissions management. Prerequisites for this lab include understanding how to log in to and use the AWS Management Console, EC2 basics (including how to launch an instance), IAM basics (including users, policies, and roles), and how to use the AWS CLI.

**Note:** When connecting to the bastion host and the web server, do so independently of each other. The bastion host is used for interacting with AWS services via the CLI.

## Solution

Log in to the AWS console using the `cloud_user` credentials provided. Once inside the AWS account, make sure you are using `us-east-1` (N. Virginia) as the selected region.

**Hint:** _When copying and pasting code into Vim from the lab guide, first enter `:set paste` (and then `i` to enter insert mode) to avoid adding unnecessary spaces and hashes._

### Create a Trust Policy and Role Using the AWS CLI

#### Obtain the `labreferences.txt` File
**labreferences.txt Content**
```text

Dev s3 bucket name = cfst-3035-3a6ce7d3db5a82f441bf6562bd9-s3bucketdev-a5f65mhzz4gj

Prod s3 bucket name = cfst-3035-3a6ce7d3db5a82f441bf6562bd-s3bucketprod-yt4vt8gh21u

Engineering s3 bucket name = cfst-3035-3a6ce7d3db5a82f441b-s3bucketengineering-1uy19bb2ofy6s

Secret s3 bucket name = cfst-3035-3a6ce7d3db5a82f441bf6562-s3bucketsecret-ppx8jxd6m5nm
```
1.  Navigate to S3.
2.  From the list of buckets, open the one that contains the text _s3bucketlookupfiles_ in the middle of its name.
3.  Select the `labreferences.txt` file.
4.  Click **Actions** > **Download**.
5.  Open the `labreferences.txt` file, as we will need to reference it throughout the lab.

#### Log in to Bastion Host and Set the AWS CLI Region and Output Type
1.  Navigate to **EC2** > **Instances**.
2.  Copy the public IP of the _Bastion Host_ instance.
3.  Open a terminal, and log in to the bastion host via SSH:
```bash
ssh cloud_user@<BASTION_HOST_PUBLIC_IP>
```

For more information on how to connect to a Linux instance using SSH, please refer to the [AWS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html). For more information on how to connect to a Linux instance using Putty, please refer to [Connect to your Linux instance from Windows using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html).
    

1.  Enter the password provided for it on the lab page.
2.  Run the following command:
```bash
[cloud_user@bastion]$ aws configure
```
3. Press **Enter** twice to leave the _AWS Access Key ID_ and _AWS Secret Access Key_ blank.
4. Enter `us-east-1` as the default region name.
5. Enter `json` as the default output format.

#### Create IAM Trust Policy for an EC2 Role

1.  Create a file called `trust_policy_ec2.json`:
    
```bash
[cloud_user@bastion]$ vim trust_policy_ec2.json
```
    
2.  To avoid adding unnecessary spaces or hashes, type `:set paste` and then `i` to enter `insert` mode.
    
3.  Paste in the following content:
    
```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {"Service": "ec2.amazonaws.com"},
          "Action": "sts:AssumeRole"
        }
      ]
    }
```
    
4.  Save and quit the file by pressing **Escape** followed by `:wq!`.

#### Create the `DEV` IAM Role

1.  Run the following AWS CLI command:
    
    ```bash
    [cloud_user@bastion]$ aws iam create-role --role-name DEV_ROLE --assume-role-policy-document file://trust_policy_ec2.json
    ```
    

#### Create an IAM Policy Defining Read-Only Access Permissions to an S3 Bucket

1.  Create a file called `dev_s3_read_access.json`:
    
    ```bash
    [cloud_user@bastion]$ vim dev_s3_read_access.json
    ```
    
2.  To avoid adding unnecessary spaces or hashes, type `:set paste` and then `i` to enter `insert` mode.
    
3.  Enter the following content, replacing `<DEV_S3_BUCKET_NAME>` with the bucket name provided in the `labreferences.txt` file:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
          "Sid": "AllowUserToSeeBucketListInTheConsole",
          "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
          "Effect": "Allow",
          "Resource": ["arn:aws:s3:::*"]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": [
			//the "/*" means it applies to the objects within the bucket
			// no "/*" means it applies to the bucket itself
                "arn:aws:s3:::<DEV_S3_BUCKET_NAME>/*", //must be bucket names
                "arn:aws:s3:::<DEV_S3_BUCKET_NAME>" //bucket names
            ]
        }
    ]
}
```

1.  Save and quit the file by pressing **Escape** followed by `:wq!`.
    
2.  Create the managed policy called `DevS3ReadAccess`:
    
    ```bash
    [cloud_user@bastion]$ aws iam create-policy --policy-name DevS3ReadAccess --policy-document file://dev_s3_read_access.json
    ```
    ![[firefox_LKklRhVknW.png]]
3.  Copy the policy ARN in the output, and paste it into the `labreferences.txt` file — we'll need it in a minute.
    

### Create Instance Profile and Attach Role to an EC2 Instance
#### Attach Managed Policy to Role

1.  Attach the managed policy to the role, replacing `<DevS3ReadAccess_POLICY_ARN>` with the ARN you just copied:
    
    ```bash
    [cloud_user@bastion]$ aws iam attach-role-policy --role-name DEV_ROLE --policy-arn "<DevS3ReadAccess_POLICY_ARN>"
    ```
    
2.  Verify the managed policy was attached:
    
    ```bash
    [cloud_user@bastion]$ aws iam list-attached-role-policies --role-name DEV_ROLE
    ```
    

#### Create the Instance Profile and Add the `DEV_ROLE` via the AWS CLI

1.  Create instance profile named `DEV_PROFILE`:
    
    ```bash
    [cloud_user@bastion]$ aws iam create-instance-profile --instance-profile-name DEV_PROFILE
    ```
    
2.  Add role to the `DEV_PROFILE` called `DEV_ROLE`:
    
    ```bash
    [cloud_user@bastion]$ aws iam add-role-to-instance-profile --instance-profile-name DEV_PROFILE --role-name DEV_ROLE
    ```
    
3.  Verify the configuration:
    
    ```bash
    [cloud_user@bastion]$ aws iam get-instance-profile --instance-profile-name DEV_PROFILE
    ```
	
	#### Attach the `DEV_PROFILE` Role to an Instance

1.  In the AWS console, navigate to **EC2** > **Instances**.
    
2.  Copy the instance ID of the instance named _Web Server_ instance and paste it into the `labreferences.txt` file — we'll need it in a second.
    
3.  In the terminal, attach the `DEV_PROFILE` to an EC2 instance, replacing `<LAB_WEB_SERVER_INSTANCE_ID>` with the _Web Server_ instance ID you just copied:

![[firefox_h2eLWoF7j1.png]]
    
```bash
    [cloud_user@bastion]$ aws ec2 associate-iam-instance-profile --instance-id <LAB_WEB_SERVER_INSTANCE_ID> --iam-instance-profile Name="DEV_PROFILE"
```
    
4.  Verify the configuration (be sure to replace `<LAB_WEB_SERVER_INSTANCE_ID>` with the _Web Server_ instance ID again):
    
    ```bash
    [cloud_user@bastion]$ aws ec2 describe-instances --instance-ids <LAB_WEB_SERVER_INSTANCE_ID>
    ```
    
    This command's output should show this instance is using `DEV_PROFILE` as an `IamInstanceProfile`. Verify this by locating the `IamInstanceProfile` section in the output, and look below to make sure the `"Arn"` ends in `/DEV_PROFILE`.
	
	### Test S3 Permissions via the AWS CLI

1.  In the AWS console, copy the public IP of the _Web Server_ instance.
    
2.  Open a new terminal.
    
3.  Log in to the web server instance via SSH:
    
    ```bash
    ssh cloud_user@<WEB_SERVER_PUBLIC_IP>
    ```
    
4.  Use the same password for the bastion host provided on the lab page.
    
5.  Verify the instance is assuming the `DEV_ROLE` role:
    
    ```bash
    [cloud_user@webserver]$ aws sts get-caller-identity
    ```
    `sts` => secure token service
    We should see `DEV_ROLE` in the `Arn`.
    ![[firefox_SI8i23kPUx.png]]
6.  List the buckets in the account:
    
    ```bash
    [cloud_user@webserver]$ aws s3 ls
    ```
    
    Copy the entire name (starting with `cfst`) of the bucket with `s3bucketdev` in its name.
    
7.  Attempt to view the files in the `s3bucketdev-` bucket, replacing `<s3bucketdev-123>` with the bucket name you just copied:
    
    ```bash
    [cloud_user@webserver]$ aws s3 ls s3://<s3bucketdev-123>
    ```
    ![[firefox_gHLU1JeJnX.png]]
    We should see a list of files.
	
### Create an IAM Policy and Role Using the AWS Management Console

#### Create Policy

1.  In the AWS console, navigate to **IAM** > **Policies**.
2.  Click **Create policy**.
3.  Click the **JSON** tab.
4.  Paste the following text as the policy, replacing `<PROD_S3_BUCKET_NAME>` with the bucket name provided in the `labreferences.txt` file:
    
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
              "Sid": "AllowUserToSeeBucketListInTheConsole",
              "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
              "Effect": "Allow",
              "Resource": ["arn:aws:s3:::*"]
            },
            {
                "Effect": "Allow",
                "Action": [
                    "s3:Get*",
                    "s3:List*"
                ],
                "Resource": [
                    "arn:aws:s3:::<PROD_S3_BUCKET_NAME>/*",
                    "arn:aws:s3:::<PROD_S3_BUCKET_NAME>"
                ]
            }
        ]
    }
    ```
    ![[firefox_7e9EibEHb7.png]]
5.  Click **Next: Tags**.
6.  Click **Next: Review**.
7.  Enter **ProdS3ReadAccess** as the policy name.
8.  Click **Create policy**.

#### Create Role

1.  Click **Roles** in the left-hand menu.
    
2.  Click **Create role**.
    
3.  Under _Choose a use case_, select **EC2**.
    
4.  Click **Next: Permissions**.
    
5.  In the _Filter policies_ search box, enter **ProdS3ReadAccess**.
    
6.  Click the checkbox to select **ProdS3ReadAccess**.
    
7.  Click **Next: Tags**.
    
8.  Click **Next: Review**.
    
9.  Give it a _Role name_ of **PROD\_ROLE**.
    
10.  Click **Create role**.
    

### Attach IAM Role to an EC2 Instance Using the AWS Management Console

1.  Navigate to **EC2** > **Instances**.
    
2.  Select the _Web Server_ instance.
    
3.  Click **Actions** > **Security** > **Modify IAM role**.
    
4.  In the _IAM role_ dropdown, select **PROD\_ROLE**.
    
5.  Click **Save**.
    

#### Test the Configuration

1.  Open the existing terminal connected to the _Web Server_ instance. (You may need to reconnect if you've been disconnected.)
    
2.  Determine the identity currently being used:
    
    ```bash
    [cloud_user@webserver]$ aws sts get-caller-identity
    ```
    
    This time, we should see `PROD_ROLE` in the `Arn`.
    
3.  List the buckets:
    
    ```bash
    [cloud_user@webserver]$ aws s3 ls
    ```
    
4.  Copy the entire name (starting with `cfst`) of the bucket with `s3bucketprod` in its name.
    
5.  Attempt to view the files in the `s3bucketprod-` bucket, replacing `<s3bucketprod-123>` with the bucket name you just copied:
    
    ```bash
    [cloud_user@webserver]$ aws s3 ls s3://<s3bucketprod-123>
    ```
    
    It should list the files.
	
	![[firefox_9Ibtpsq3DC.png]]
    
6.  In the `aws s3 ls` command output, copy the entire name (starting with `cfst`) of the bucket with `s3bucketsecret` in its name.
    
7.  Attempt to view the files in the `<s3bucketsecret-123>` bucket, replacing `<s3bucketsecret-123>` with the bucket name you just copied:


    
    ```bash
    [cloud_user@webserver]$ aws s3 ls s3://<s3bucketsecret-123>
    ```
    
    This time, our access will be denied — which means our configuration is properly set up.
	
# EC2 Quiz

1. To help you manage your Amazon EC2 instances, you can assign your own metadata in the form of **\_\_\_\_**.
	- Tags
		- Tagging is a key part of managing an environment. Even in a lab, it is easy to lose track of the purpose of resources, and tricky determine why it was created and if it is still needed. This can rapidly translate into lost time and lost money.
2. Will an Amazon EBS root volume persist independently from the life of the terminated EC2 instance to which it was previously attached? In other words, if I terminated an EC2 instance, would that EBS root volume persist?
	- No - Unless 'Delete on Termination' is unchecked for the root volume
		- You can control whether an EBS root volume is deleted when its associated instance is terminated. The default delete-on-termination behaviour depends on whether the volume is a root volume, or an additional volume. By default, the DeleteOnTermination attribute for root volumes is set to 'true.' However, this attribute may be changed at launch by using either the AWS Console or the command line. For an instance that is already running, the DeleteOnTermination attribute must be changed using the CLI.
3. Which service would you use to run a general Windows File Server with minimal overhead?
	- FSx for Windows
		- Amazon FSx for Windows File Server provides a fully managed native Microsoft Windows file system so you can easily move your Windows-based applications that require shared file storage to AWS. EBS Multi Attach allows you to attach a volume to up to 16 instances, but would have issues across multiple availability zones, and could not use NTFS natively. EFS uses the NFS protocol, and is explicitly not supported on Windows. S3 is object-based storage, and would not be suitable as the backend for a file server
4. Can I delete a snapshot of the root device of an EBS volume used by a registered AMI?
	- No 
		- If the original snapshot was deleted, then the AMI would not be able to use it as the basis to create new instances. For this reason, AWS protects you from accidentally deleting the EBS Snapshot, since it could be critical to your systems. To delete an EBS Snapshot attached to a registered AMI, first remove the AMI, then the snapshot can be deleted.
5. When can you attach/replace an IAM role on an EC2 instance?
	- To attach an IAM role to an instance that has no role, the instance can be in the stopped or running state. To replace the IAM role on an instance that already has an attached IAM role, the instance must be in the running state.
		- IAM Roles can be attached to instances in the stopped or running state, or replaced for instances in the running state. Prior to early 2017, you would only be able to attach an IAM role at launch, and if you wanted to attach a role, you would have to terminate and re-launch the instance. Reference: [Attaching an IAM role to an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role "null").
6. Which of the following provide the least expensive EBS options? 
	- Throughput Optimized (st1)
	- Cold (sc1)
		- Of all the EBS types, both current and of the previous generation, HDD based volumes will always be less expensive than SSD types. Therefore, of the options available in the question, the Cold (sc1) and Throughout Optimized (st1) types are HDD based and will be the least expensive options.
7. If an Amazon EBS volume is attached as an additional disk (not the root volume), can I detach it without stopping the instance?
	- Yes, although it may take some time.
		- Since the additional disk does not contain the operating system, you can detach it in the EC2 Console while the instance is running. However, any data on that drive would become inaccessible, and possibly cause problems for the EC2 instance
8. EBS Snapshots are backed up to S3 in what manner?
	- Incrementally
		- EBS snapshots use incremental backups and are stored in S3. Restores can be done from any of the snapshots. The original full snapshot can be safely deleted without impacting the ability to use the other related incremental backups.
9. The use of a cluster placement group is ideal **\_\_\_**
	- Your fleet of EC2 instances requires high network throughput and low latency within a single availability zone.
		- Cluster Placement Groups are primarily about keeping you compute resources within one network hop of each other on high speed rack switches. This is only helpful when you have compute loads with network loads that are either very high or very sensitive to latency.
10. What are the valid underlying hypervisors for EC2?
	- Xen
	- Nitro
		- AWS originally used a modified version of the Xen Hypervisor to host EC2. In 2017, AWS began rolling out their own Hypervisor called Nitro.
11. Which AWS CLI command should I use to create a snapshot of an EBS volume?
	- aws ec2 create-snapshot
		- When looking at the AWS CLI, remember the verbs, like 'create', which are used as part of commands. This helps you build the necessary command in your head, without referring to the documentation. For example, we might create a new image along with this snapshot. From this, we could understand that the command would likely be 'aws ec2 create-image'.

![[firefox_L9yLJkZ7SW.png]]
12. Which EC2 feature uses SR-IOV?
	- Enhanced networking
		- Enhanced networking uses single root I/O virtualization (SR-IOV) to provide high-performance networking capabilities on supported instance types. SR-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces. Enhanced networking provides higher bandwidth, higher packet per second (PPS) performance, and consistently lower inter-instance latencies. Reference: [Enhanced networking on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html "null").

13. You need to know both the private IP address and public IP address of your EC2 instance. You should **\_\_\_\_**.
	- Retrieve the instance Metadata from `http://169.254.169.254/latest/meta-data/local-ipv4` and `http://169.254.169.254/latest/meta-data/public-ipv4`.
		- Instance Metadata and User Data can be retrieved from within the instance via a special URL. Similar information can be extracted by using the API via the CLI or an SDK. The ipconfig and ifconfig tools don't have the ability to see the Public IP Address directly, since it's attached dynamically inside the AWS Software Defined Network which has to be queried by the API
14. Can Spread Placement Groups be deployed across multiple Availability Zones?
	- Yes.
		- Spread Placement Groups can be deployed across availability zones since they spread the instances further apart. Cluster Placement Groups can only exist in one Availability Zone since they are focused on keeping instances together, which you cannot do across Availability Zones
15. When creating a new security group, all inbound traffic is allowed by default.
	- False
		- There are slight differences between a normal 'new' Security Group and a 'default' security group in the default VPC. For a 'new' security group nothing is allowed in by default.
16. Spread Placement Groups can be deployed across multiple Availability Zones
	- Spread Placement Groups can be deployed across multiple Availability Zones
		- Spread Placement Groups can be deployed across availability zones since they spread the instances further apart. Cluster Placement Groups can only exist in one Availability Zone since they are focused on keeping instances together, which you cannot do across Availability Zones
17. What type of storage are Amazon's EBS volumes based on?
	- Block-based
		- EBS uses Block-based storage, where the data is stored on a virtual disk managed by the Operating System. EFS uses File-based storage, where the underlying filesystem is managed by AWS. S3 uses Object-based storage, where files are kept in a flat structure
18. Standard Reserved Instances can be moved between regions
	- Standard Reserved Instances can be moved between regions
		- Standard Reserved Instances cannot be moved between regions. You can choose if a Reserved Instance applies to either a specific Availability Zone, or an Entire Region, but you cannot change the region
19. In order to enable encryption at rest using EC2 and Elastic Block Store, you must **\_\_\_\_**.
	- Configure encryption when creating the EBS volume
		- The use of encryption at rest is default requirement for many industry compliance certifications. Using AWS managed keys to provide EBS encryption at rest is a relatively painless and reliable way to protect assets and demonstrate your professionalism in any commercial situation
20. Specifically, where in the AWS Global Infrastructure are EC2 instances provisioned?
	- In Availability Zones
		- When you're setting up an EC2 instance, you select which subnet you'd like to place your EC2 instance in. Each subnet is tied to a specific availability zone. You cannot move an instance between Availability Zones, without setting up a copied version of the instance. Whilst they exist in Regions, they are not portable across the whole region, nor across the whole globe
21. When updating the policy used by an IAM Role attached to an EC2 instance, what needs to happen for the changes to take effect?
	- Nothing - It will take effect almost immediately
		- Changes to IAM Policies take effect almost immediately (with maybe a few seconds delay). No substantial waiting time is required, nor changes to the system. This is because the IAM Policy exists in the AWS API, rather than on the instance itself. As a way to remember it in a scenario, if you think about a compromised system, you would need to revoke the access immediately, without waiting for changes to take effect. Reference: [AWS IAM FAQs](https://aws.amazon.com/iam/faqs/ "null").
22. To retrieve instance metadata or user data you will need to use the following IP Address:
	- http://169.254.169.254
		- This IP Address is specific to AWS, where you can use it on any instance to acquire information about that instance. It is a specific type of address called a 'link-local address', and is only accessible from that particular instance. You can also disable the metadata service to prevent it's misuse
23. Is it possible to perform API actions on an existing Amazon EBS Snapshot?
	- Yes, through the AWS APIs, CLI, and AWS Console.
		- You can use AWS APIs, CLI or the AWS Console to copy snapshots, share snapshots, and create volumes from snapshots.

