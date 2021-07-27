# SQS

Amazon SQS is a web service that gives you access to a message queue that can be used to store messages while waiting for a computer to process them

Its a distributed queue system that enables web service applications to quickly and reliably queue messages that one component in the application generates to be consumed by another component

A queue is a temporary repository for messages that are awaiting processing


**Meme Website**

User uploads a photo to a S3 bucket --> this will trigger a Lambda function; which will take that S3 image and write some text over the image. --> This text is stored in SQS, and an EC2 instance will pull that message queue from the SQS and store that meme onto the S3 bucket. --> If the EC2 goes down the message is still stored in SQS and will be available in the next EC2. 
![[firefox_ylXyfEDRsu.png]]

**Travel Website**

User wants a deal on flight --> They visit their EC2 webserver for reserving a date --> The EC2 will parse that information into an SQS queue, and some application servers will get that message and look for deals. --> which is then collected by the EC2 instance and sent back to the user. 

![[firefox_HYJPJmTO94.png]]

#### What is SQS

Using Amazon SQS, you can decouple the components of an application so they run independently, easing message management between components. 

Any component of a distributed application can store messages in a fail-safe queue

Messages can contain up to 256 KB of text in any format. Any component can later retrieve the messages programmatically using Amazon SQS API

The queue acts as a buffer between the component producing and saving data, and the component receiving the data for processing.

This means the queue resolves issues that arise if the producer is producing work faster than the consumer can process it, or if the producer or consumer are only intermittently connected to the network

#### Queue Types
- Standard queues (default)
- FIFO queues (First in First out)

**Standard Queues**

Amazon SQS offers standard as the default queue type. A standard queue lets you have a nearly-unlimited number of transactions per second. Standard queues guarantee that a message is delivered at least once. 

Ocassionally (because of the highly-distributed architecture that allows high throughput), more than one copy of a message might be delivered out of order

However, standard queues provide best-effort ordering which ensures that messages are generally delivered in the same order as they are sent

**FIFO Queues**

The FIFO queue complements the standard queue.

The most important features of this queue type are FIFO (first-in-first-out) delivery and exactly-once processing: the order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it; duplicates are not introduced into the queue. 

FIFO queues also support message groups that allow multiple ordered message groups within a single queue. 

FIFO queues are limited to 300 transactions per second (TPS), but have all the capabilities of standard queues. 

### Exam Tips 
- SQS is pull-based, not pushed-based
	- EC2 instances have to pull the messages out of the SQS queue
- Messages are 256 KB in size
- Messges can be kept in the queue from 1 minute to 14 days; the default retention period is 4 days
- Visibility timeout is the amount of time that the message is invisible in the SQS queue after a reader picks up that message. Provided the job is processed before the visibility timeout expires, the message will then be deleted from the queue. If the job is not processed within that time, the message will become visible again and another reader will process it. This could result in the same message being delivered twice. 
- Visibility timeout maximum is 12 hours
- SQS guarantees that your messages will be processed at least once
- Amazon SQS long polling is a way to retrieve messages from your Amazon SQS queues. While the regular short polling returns immediately (even if the message queue being polled is empty), long polling doesn't return a response until a message arrives in the messages queue, or the long poll times out. 
- Any time you see a scenario based question about "decoupling" your infrastructure - think SQS

# Simple Workflow Service (SWF)

Amazon Simple Workflow Service (Amazon SWF) is a web service that makes it easy to coordinate work across distributed application components. SWF enables applications for a range of use cases, including media processing, web application back-ends, business process workflows, and analytics pipelines, to be designed as a coordination of tasks 

Tasks represent invocation of various processing steps in an application which can be performed by executable code, web service calls, human actions , and scripts. 

### Exam Tips 
SQS vs SWF 

- SQS has a retention period of up to 14 days; with SWF, workflow executions can last up to 1 year. 
- Amazon SWF presents a task-oriented API, whereas AMazon SQS offers a message-oriented API
- Amazon SWF ensures that a task is assigned only once and is never duplicated. With Amazon SQS, you need to handle duplicated messages and may also need to ensure that a message is processed only once. 
- Amazon SWF keeps track of all the tasks and events in an application. With Amazon SQS, you need to implement your own application-level tracking, especially if your application uses multiple queues. 

SWF Actors
- Workflow Starters - An application that can initiate (start) a workflow. Could be your e-commerce website following the placement of an order, or a mobile app searching for bus times 
- Deciders -- Control the flow of activity tasks in a workflow execution. If something has finished (or failed) in a workflow, a Decider decides what to do next
- Activity Workers -- Carry out the activity Tasks

# Simple Notification Service (SNS)

Amazon Simple Notification Service (Amazon SNS) is a web service that makes it easy to set up, operate, and send notifications from the cloud. 

It provides developers with a highly scalable, flexible, and cost-effective capability to publis messages from an application and immediately deliver them to subscribers or other applications. 

**Push Notifications**

Push notifications to Apple, Google, Fire OS, and Windows devices, as well as Android devices in China with Baidu Cloud Push

**SQS Integration**

Besides pushing cloud notifications directly to mobile devices, Amazon SNS can also deliver notifications by SMS text message or email to Amazon Simple Queue Service (SQS) queues, or to any HTTP endpoint. 

**What is A Topic?**

SNS allows you to group multiple recipients using topics. A topic is an "Access point" for allowing recipients to dynamically subscribe for identical copies of the same notification

One topic can support deliveries to multiple endpoint types -- for example, you can group together iOS, Android and SMS recipients. When you publish once to a topic, SNS delivers appropriately formatted copies of your message to each subscriber

**SNS Availability**

To prevent messages from being lost, all messages published to Amazon SNS are stored redundantly across multiple availability zones. 

![[firefox_JBxzJ5Pz4K.png]]

### Exam Tips 

SNS Benefits
- Instantaneous, push-based delivery (no polling)
- Simple APIs and easy integration with applications
- Flexible message delivery over multiple transport protocols
- Inexpensive, pay-as-you-go model with no up-front costs
- Web-based AWS management Console offers the simplicity of a point-and-click interface

SNS vs SQS 
- Both Messaging Services in AWS
- SNS --> PUSH
- SQS --> Polls (Pulls)

# Elastic Transcoder 

- Media Transcoder in the cloud
- Convert media files from their original source format in to different formats that will play on smartphones, tablets, PCs, etc
- Provides transcoding presets for popular ouput formats, which means that you don't need to guess about which settings work best on particular devices
- Pay based on the minutes that you transcode and the resolution at which you transcode


**Example**
File is uploaded to S3 --> which triggers a lambda function --> lambda function will take the metadata and send it to Elastic Transcoder --> Elastic Transcode will transcode the video so it looks good in all devices, and stores the transcoded file to a different S3 bucket. 

![[firefox_4Cqxs5Tj8f.png]]

### Exam Tips 

Just remember that it is a media transcoder in the cloud. It converts media files from their original source format in to different formats that will play on smartphones, tablets, PCs, etc.

# API Gateway 

Amazon API Gateway is a fully managed service that makes it easy for developers to publish, maintain, monitor and secure APIs at any scale. 

With a few clicks in the AWS Management Console, you can create an API that acts as a "front door" for applications to access data, business logic, or functionality from your back-end services, such as applications running on Amazon Elastic Compute Cloud (Amazon EC2), code running on AWS Lambda, or any web application 

**How it works**

User makes a call to our API Gateway --> which can be passed to any web application on AWS (EC2, DynamoDB, AWS Lambda, etc)

**What can API Gateway Do?**

- Expose HTTPS endpoints to define a RESTful API
- Serverless-ly connect to services like Lamda & DynamoDB
- Send each API endpoint to a different target
- Run efficiently with low cost
- Scale effortlessly
- Track and control usage by API key 
- Throttle requests to prevent attacks Connect to CloudWatch to log all requests for monitoring
- Maintain multiple versions of your API

**How to Configure**

- Define an API (container)
- Define Resources and nested Resources (URL paths)
- For each Resource:
	- Select supported HTTP methods (GET, POST, etc)
	- Set security 
	- Choose target (such as EC2, Lambda, DynamoDB, etc)
	- Set request and response transformations as well

**How to deploy**

- Deploy API to a stage:
	- Uses API Gateway domain, by default
	- Can use custom domain
	- Now supports AWS Certificate Manager: Free SSL/TLS certs

**API Gateway Caching**

You can enable API caching in Amazon API Gateway to cache your endpoint's response. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of the requests to your API. When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time-to-live (TTL) period, in seconds. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint

**Same Origin Policy**

In computing, the same-origin policy is an important concept in the web application security model. Under the policy, a web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin. 

	- One page can talk to another page, provided they have the same domain name

This is done to prevent Cross-Site Scripting (XSS) attacks. 
- Enforced by web browsers
- Ignored by tools like PostMan and curl

#### CORS Explained

CORS is one way the server at the other end (not the client code in the browser) can relax the same-origin policy. 

Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources (e.g. fonts) on a web page to be requested from another domain outside the domain from which the first resource was served

*Example*
- Browser makes an HTTP OPTIONS call for a URL (OPTIONS is an HTTP method like GET, PUT, and POST)
- Server returns a response that says: "These other domains are approved to GET this URL"
- Error -- "Origin policy cannot be read at the remote resource?" You need to enable CORS on API Gateway 


### Exam Tips 
- Remember what API Gateway is at a high level
- API Gateway has caching capabilities to increase performance
- API Gateway is low cost and scales automatically
- You can throttle API Gateway to prevent attacks
- You can log results to CloudWatch
- If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled CORS on API Gateway
- CORS is enforced by the client

# Kinesis 

**Streaming Data**

Streaming Data is data that is generated continuously by thousands of data sources, which typically send in the data records simultanously, and in small sizes (order of Kilobytes).

- Purchases from online stores (think Amazon)
- Stock Prices
- Game data (as the gamer plays)
- Social Network data
- Geospatial data (think uber)
- iOT sesnor data

**Kinesis**

Amazon Kinesis is a platform on AWS to send your streaming data to. Kinesis makes it easy to load and analyze streaming data, and also providing the ability for you to build your own custom applications for your business needs. 

3 Different Types of Kinesis
1. Kinesis Streams
2. Kinesis Firehose
3. Kinesis Analytics

**Kinesis Streams**

Producers stream data to Kinesis, and it stores that data for 24hours by default but can go upto 7 days. The data is stored as shard, the data stored in shard is consumed by EC2 instaces, 'consumers'. Then the EC2 instance can then store it in different applications. 

![[firefox_4UXzb86JuM.png]]

Kinesis Streams consist of Shards:
- 5 transactions per second for reads, up to a maximum total data read rate of 2MB per second and up to 1000 records per second for writes, up to a maximum total data write rate of 1MB per second (including partition keys)
- The data capacity of your stream is a function of the number of shards that you specify for the stream. The total capacity of the stream is the sum of the capacities of its shards


**Kinesis Firehose**

Producers sent data to Kinesis Firehose, there is no persistent storage; and you must do something with the data when it comes in, and it is outputed to S3 or Elasticsearch Cluster. 

![[firefox_wwVQ3H8GKL.png]]

![[firefox_QJXkbZLVLI.png]]

**Kinesis Analytics**

Works with both kinesis firehose and streams. It can analyze the data on the fly, using either service, and store the data on either redshit, S3, or Elastic Cluster

![[firefox_6FQS5zT3yq.png]]

### Exam Tips 
- Know the difference between Kinesis Streams, and Kinesis Firehose. 
- Understand what Kinesis Analytics is

# Web Identity Federation and Cognito

Web Identity Federation lets you give your users access to AWS resources after they have successfully authenticated with a web-based identity provided like Amazon, Facebook, or Google. Following successful authentication, the user receives an authentication code from the Web ID provider, which they can trade for temporary AWS security credetnials 

Amazon Cognito provides Web Identity Federation with the following features:
- Sign-up and sign-in to your apps
- Access for guest users 
- Acts as an identity broker between your application and web ID providers, so you don't need to write any additional code
- Synchronizes user data for multiple devices
- Recommeneded for all mobile applications AWS services

The recommended approach for web identity federation using social media accounts like facebook. 

If a user wants to access AWS services like S3, Redshift, etc. They can authenticate using facebook, Facebook will provide them with a token, they can use that token to sign into AWS Cognito. Cognito will respond back after recieving a token, and grant them access to AWS resources. 

![[firefox_fPvfAfmOtL.png]]

**Amazon Cognito Use Cases**

Cognito brokers between the app and Facebook or Google to provide temporary credentials which map to an IAM role allowing access to the required resources.

No need for the application to embed or store AWS credentials locally on the device and it gives users a seamless experience across all mobile devices.

**Cognito User Pools**

User Pools are user directories used to manage sign-up and sign-in functionality for mobile and web applications. Users can sign-in directly to the User Pool, or using Facebook, Amazon, or Google. Cognito acts as an Identity Broker between the identity provider and AWS. Successful authentication generates a JSON Web Token (JWTs).

**Cognito Identity Pools**

Identity Pools enable provide temporary AWS credentials to access AWS services like S3 or DynamoDB

![[firefox_8CsHVNIfCl.png]]

**Cognito Synchronization**

Cognito tracks the association between user identity and the various different devices they sign-in from. In order to provide a seamless user experience for your application, Cognito uses Push Synchronization to push updates and synchronize user data across multiple devices. Cognito uses SNS to send a notification to all the devices associated with a given user identity whenever data stored in the cloud changes. 

![[firefox_BlnFGq7uNc.png]]

### Exam Tips 

- Federation allows users to authenticate with a Web Identity Provider (Google, Facebook, Amazon)
- The user authenticates first with the Web ID provider and receives an authentication token, which is exchanged for temporary AWS credentials allowing them to assume an IAM role
- Cognito is an Identity Broker which handles interaction between your applications and the Web ID provider (you don't need to write your own code to do this)

Cognito 
- User pool is user based. It handles things like user registration, authenticatino, and account recovery
- Identity pools authorise access to your AWS resources

# Event Processing Patterns 

One or more AWS services will automatically perform work in response to events triggered by other AWS services. This architectural pattern can make services more reusable, interoperable, and scalable.

- Event-Driven Architecture
- Dead-Letter Queue (DLQ)
- Fanout Pattern
- S3 Event Notification

**Event-Driven Architecture**

![[firefox_s0uuYb5aTu.png]]

publish subscribe, or pub/sub messaging provides instant event notifications. The pub/sub model allows messages to be broadcast to different parts of a system asynchronously. 

**Dead-Letter Queue (DLQ)**
- SNS
	- Messages published to a topic that fail to deliver are sent to an SQS queue; held for further analysis for reprocessing
- SQS
	- Messages sent to SQS that exceed the queue's maxReceiveCount are sent to a DLQ (another SQS queue)
- Lambda
	- Results from failed asynchronous invocations; will retry twice and send either an SQS queue or SNS topic

![[firefox_6yyR9yMyoA.png]]

**Fanout Pattern**
![[firefox_EE76HzpEWl.png]]

![[firefox_bc0cXtpSf2.png]]

**S3 Event Notifications**

![[firefox_yZms6ETIPw.png]]

- Object Created Event
	- `s3:ObjectCreated:Put`
	- `s3:ObjectCreated:*`
- Object Removed Event
	- Supports deletes of versioned and unversioned objects
- Object Restored Event
	- Restoration of objects in Glacier
- RRS Object Lost Event
	- Detection of a reduced-redundadncy storage object is lost
- Replication Event
	- Replication failed
	- Replication exceeds 15 minutes
	- Object no longer tracked by replication metrics

### Exam Tips 
- Understand the pub/sub pattern -- facilitated by SNS
- DLQ -- SNS, SQS, Lambda
- Fanout pattern -- SNS
- S3 event notifications -- which events trigger; which services consume


# Summary 

SQS 
- SQS is a way to de-couple your infrastructure
- SQS is pull-based, not pushed based
- Messages are 256 kb in size
- Messages can be kept in the queue from 1 minute to 14 days; the default retention period is 4 days
- Standard SQS and FIFO SQS
- Standard order is ont guaranteed and messages can be delivered more than once
- FIFO order is strictly maintained and messaged are delivered only once


SQS 
- Visibility timeout is the amount of time that the message is invisible in the SQS queue after a reader picks up that message. Provided the job is processed before the visibility timeout expires, the message will then be deleted from the queue. If the job is not processed within that time, the message will become visible again and another reader will process it. This could result in the same message being delivered twice. 
- Visibility timeout maximum is 12 hours
- SQS guarantees that your messages will be processed at least once
- Amazon SQS long polling is a way to retrieve messages from your Amazon SQS queues. While the regular short polling returns immediately (even if the message queue being polled is empty), long polling doesn't return a response until a message arrives in the messages queue, or the long poll times out. 

SWF vs SQS 
- SQS has a retention period of up to 14 days; with SWF, workflow executions can last up to 1 year. 
- Amazon SWF presents a task-oriented API, whereas AMazon SQS offers a message-oriented API
- Amazon SWF ensures that a task is assigned only once and is never duplicated. With Amazon SQS, you need to handle duplicated messages and may also need to ensure that a message is processed only once. 
- Amazon SWF keeps track of all the tasks and events in an application. With Amazon SQS, you need to implement your own application-level tracking, especially if your application uses multiple queues. 

SWF Actors
- Workflow Starters - An application that can initiate (start) a workflow. Could be your e-commerce website following the placement of an order, or a mobile app searching for bus times 
- Deciders -- Control the flow of activity tasks in a workflow execution. If something has finished (or failed) in a workflow, a Decider decides what to do next
- Activity Workers -- Carry out the activity Tasks

SNS Benefits
- Instantaneous, push-based delivery (no polling)
- Simple APIs and easy integration with applications
- Flexible message delivery over multiple transport protocols
- Inexpensive, pay-as-you-go model with no up-front costs
- Web-based AWS management Console offers the simplicity of a point-and-click interface

SNS vs SQS 
- Both Messaging Services in AWS
- SNS --> PUSH
- SQS --> Polls (Pulls)

Elastic Transcoder is a media transcoder in the cloud. It converts media files from their original source format in to different formats that will play on smartphones, tablets, PCs, etc

API Gateway 
- Remember what API Gateway is at a high level
- API Gateway has caching capabilities to increase performance
- API Gateway is low cost and scales automatically
- You can throttle API Gateway to prevent attacks
- You can log results to CloudWatch
- If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled CORS on API Gateway
- CORS is enforced by the client

Kinesis 
- Know the difference between Kinesis Streams and Kinesis Firehose.
- Understand what Kinesis Analysis 

Cognito 
- Federation allows users to authenticate with a Web Identity Provider (Google, Facebook, Amazon)
- The user authenticates first with the Web ID provider and receives an authentication token, which is exchanged for temporary AWS credentials allowing them to assume an IAM role
- Cognito is an Identity Broker which handles interaction between your applications and the Web ID provider (you don't need to write your own code to do this)

Cognito 
- User pool is user based. It handles things like user registration, authenticatino, and account recovery
- Identity pools authorise access to your AWS resources

# Scaling EC2 Using SQS - Lab 

![[firefox_u6pT0cEd18.png]]

![[firefox_uI20ONsDmm.png]]

![[firefox_6VDOHTgYWD.png]]

![[firefox_NqNLX3EnJY.png]]

![[firefox_Dlx6jlKiac.png]]

![[firefox_jlsrkK5WbJ.png]]

![[firefox_7pQF6uXTxl.png]]

![[firefox_alOGLb0qgL.png]]

![[firefox_Em8YcelzR7.png]]

![[firefox_4xMNRTTp5h.png]]

![[firefox_W2UnV157dh.png]]


# Scaling EC2 Using SQS

## Introduction

In this scenario, you're a solutions architect at an e-commerce firm. The company runs flash sales from time to time, and when there's a spike in orders, the fulfillment backend can struggle to meet demand. One way to solve the problem is to overprovision EC2 instances in the fulfillment system to provide headroom to process all the orders. However, this can be very costly, since you'll have unused capacity when the traffic subsides. What if there's a better way? Well, there is, and this is the problem you'll solve here. In this lab, you will learn to create Auto Scaling rules for EC2 based on the number of messages in an SQS queue.

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Create CloudWatch Alarms

1.  Navigate to **EC2** > **Instances**.
    
2.  Select the _Bastion Host_ instance.
    
3.  Copy its public IP address.
    
4.  Open a terminal session, and log in to the instance:
    
    ```bash
    ssh cloud_user@<BASTION_HOST_PUBLIC_IP>
    ```
    
    The password is provided on the lab page.
    
5.  List the existing files:
    
    ```bash
    ls
    ```
    
    We'll see there's a file named `send_messages.py`.
    
6.  Run that script (this will send messages continuously into our SQS queue, simulating a large volume of orders):
    
    ```bash
    ./send_messages.py
    ```
    
7.  Back in the AWS console, select the _AutoScaling Group_ instance.
    
8.  Copy its public IP address.
    
9.  In a new terminal session, log in to that instance:
    
```bash
ssh cloud_user@<AUTOSCALING_GROUP_PUBLIC_IP>
```
    
    It uses the same password as the one for the _Bastion Host_ instance.
    
10.  List the existing files:
``` bash
    ls
```
    
We'll see there's a file named `receive_messages.py`, which is already running in the background (so we don't need to execute it).
    
11.  See what's in its log file:
```bash
tail -f receive_messages.log
```
    
We'll see this instance is retrieving messages from the queue.
    
12.  Leave the terminals open and running.
    

#### Create Scale-Out Alarm

1.  Back in the AWS console, navigate to **CloudWatch** > **Alarms**.
    
2.  Click **Create alarm**.
    
3.  Click **Select metric**.
    
4.  Select **SQS** > **Queue Metrics**.
    
5.  Click the checkbox of the metric named `ApproximateNumberOfMessagesVisible`.
    
6.  Click **Select metric**.
    
7.  On the _Specify metric and conditions_ page, change the _Period_ to **1 minute**.
    
8.  In the _Conditions_ section, set the following values:
    
    -   _Threshold type_: **Static**
    -   _Whenever ApproximateNumberOfMessagesVisible is..._: **Greater**
    -   _than..._: **500**
9.  Click **Next**.
    
10.  On the _Configure actions_ page, click into the _Send a notification to..._ box and select the listed **AutoScalingTopic**.
    
11.  Click **Next**.
    
12.  For _Alarm name_, enter **Scale Out**.
    
13.  Click **Next**.
    
14.  Click **Create alarm**.
    

#### Create Scale-In Alarm

1.  Click **Create alarm**.
    
2.  Click **Select metric**.
    
3.  Select **SQS** > **Queue Metrics**.
    
4.  Click the checkbox of the metric named `ApproximateNumberOfMessagesVisible`.
    
5.  Click **Select metric**.
    
6.  On the _Specify metric and conditions_ page, change the _Period_ to **1 minute**.
    
7.  In the _Conditions_ section, set the following values:
    
    -   _Threshold type_: **Static**
    -   _Whenever ApproximateNumberOfMessagesVisible is..._: **Lower**
    -   _than..._: **500**
8.  Click **Next**.
    
9.  On the _Configure actions_ page, click into the _Send a notification to..._ box and select the listed **AutoScalingTopic**.
    
10.  Click **Next**.
    
11.  For _Alarm name_, enter **Scale In**.
    
12.  Click **Next**.
    
13.  Click **Create alarm**.
    

### Create Simple Scaling Policies

#### Scale-Out Policy

1.  Note that after a minute or so, the _Scale Out_ alarm enters an _In alarm_ state.
    
2.  Click **Metrics** in the left-hand menu.
    
3.  Click **SQS** > **Queue Metrics**
    
4.  Click the checkbox of the metric named `ApproximateNumberOfMessagesVisible`. Initially, there will just be a small dot on the graph.
    
5.  Click **custom** at the top of the graph.
    
6.  Set _Minutes_ to **30**.
    
7.  Click the _Line_ dropdown, and select **Stacked area**.
    
8.  Click the dropdown next to the refresh icon.
    
9.  Select **Auto refresh**.
    
10.  Set the _Refresh interval_ to **10 Seconds**.
    
11.  Navigate to **EC2** > **Auto Scaling Groups**.
    
12.  Click the listed Auto Scaling group.
    
13.  Click the **Automatic scaling** tab.
    
14.  Click **Add policy**.
    
15.  Set the following values:
- Policy type_: **Simple scaling**
-  _Scaling policy name_: **Scale Out**
-  \*CloudWatch alarm**:** Scale Out\*\*
-  _Take the action_: **Add 1 capacity units**
-  _And then wait_: **60** seconds before allowing another scaling activity
16.  Click **Create**.
    

#### Scale-In Policy

1.  Click **Add policy**.
    
2.  Set the following values:
    
    -   _Policy type_: **Simple scaling**
    -   _Scaling policy name_: **Scale In**
    -   \*CloudWatch alarm**:** Scale In\*\*
    -   _Take the action_: **Remove 1 capacity units**
    -   _And then wait_: **60** seconds before allowing another scaling activity
3.  Click **Create**.
    
4.  Click the **Activity** tab. After a few minutes, we should see a new instance is launched as an effect of the scale-out policy.
    
5.  Click **Instances** in the left-hand menu. We'll see a new _AutoScaling Group_ instance now exists.
    

### Observe the Auto Scaling Group's Behavior in CloudWatch

1.  Navigate to **CloudWatch** > **Metrics**.
    
2.  Click **SQS** > **Queue Metrics**
    
3.  Click the checkbox of the metric named `ApproximateNumberOfMessagesVisible`. This time, the graph will display more data.
    
4.  In a new browser tab, navigate to **EC2** > **Auto Scaling Groups**.
    
5.  Click the listed Auto Scaling group.
    
6.  Select the **Activity** tab. After a minute or so, we should see it launches an additional EC2 instance.
    
7.  In the terminal running the `send_messages.py` script, press **Ctrl**+**C** to cancel the process (simulating the volume of sales slowing down).
    
8.  Back in the AWS console, view the CloudWatch metrics graph. After a few minutes, we should see the graph starts to flatten out.
    
9.  Wait a few minutes more, and we should see the graph takes a downward turn as the messages are drained from our SQS queue.
    
10.  In the EC2 browser tab, we should see our instances are starting to be terminated, since our _Scale In_ alarm has been triggered (meaning the number of messages in our queue is below the threshold we set).
    
11.  After a few more minutes, refresh the _Activity history_ to see another instance is now being terminated.


# Quiz

1. What happens when you create a topic on Amazon SNS?
- An Amazon Resource Name is created.
	- When a topic is created, Amazon SNS will assign a unique ARN (Amazon Resource Name) to the topic, which will include the service name (SNS), region, AWS ID of the user and the topic name. The ARN will be returned as part of the API call to create the topic. Whenever a publisher or subscriber needs to perform any action on the topic, they should reference the unique topic ARN. Reference: [Amazon SNS FAQs](https://aws.amazon.com/sns/faqs/ "null").
2. Does the Standard SQS message queue preserve message order and guarantee messages are only delivered once?
- No
	- The Standard SQS message queue does not preserve message order nor guarantee messages are delivered only once - these are features of a FIFO message queue. Since Standard SQS message queues are designed to be massively scalable using a highly distributed architecture, receiving messages in the exact order they are sent is not guaranteed. Standard queues provide at-least-once delivery, and in some circumstances, duplicates can occur.
3. What are the key components of Kinesis Data Firehose?
- Delivery streams, records of data and destinations.
	- Key components of Kinesis Data Firehose are: delivery streams, records of data and destinations. Producers, shards and consumers are components of Kinesis Data Streams.
4. With Amazon API gateway, how can you increase performance?
- Enable caching and set throttling limits.
	- If a cache is configured, then Amazon API Gateway will return a cached response for duplicate requests for a customizable time, but only if under configured throttling limits. This balance between the backend and client ensures optimal performance of the APIs for the applications that it supports.
5. You have discovered duplicate messages being processed in your SQS queue. How do you resolve this?
- Increase the visibility timeout of your queue, so that messages do not become visible once obtained by a consumer.
	- Duplicate messages occur when a consumer does not complete its message processing and the visibility timeout of the message expires, making it visible for another consumer to obtain. Increasing the visibility timeout to enable the consumer processing to complete, will prevent duplicate messages.
6. True or False? At a high level an Amazon API gateway is a front door to your AWS environment.
- True
	- Amazon API Gateway is a fully managed service that makes it easy for developers to publish, maintain, monitor, and secure APIs at any scale. With a few clicks in the AWS Management Console, you can create an API that acts as a “front door” for applications to access data, business logic, or functionality from your back-end services.
7. Amazon SWF restricts me to the use of specific programming languages.
- False
	- While there are a limited range of SDKs available for SWF, AWS provides an HTTP based API which allows you to interact using any language as long as you phrase the interactions in HTTP requests.
8. In SWF, what does a "domain" refer to?
- A collection of related workflows
	- Domains provide a way of scoping Amazon SWF resources within your AWS account. All the components of a workflow, such as the workflow type and activity types, must be specified to be in a domain. It is possible to have more than one workflow in a domain; however, workflows in different domains can't interact with each other. Reference: [Amazon SWF Domains](https://docs.aws.amazon.com/amazonswf/latest/developerguide/swf-dev-domains.html "null").
9. Amazon SQS offers standard as the default queue type. Standard queues ensure that messages are delivered in the same order as they're sent.
- False
	- If you have an existing application that uses standard queues and you want to take advantage of the ordering or exactly-once processing features of FIFO queues, you need to configure the queue and your application correctly. You can't convert an existing standard queue into a FIFO queue. To make the move, you must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue. Reference: [Amazon SQS FIFO (First-In-First-Out) queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html "null").
10. What is the difference between SNS and SQS?
- SNS is a push notification service, whereas SQS is message system that requires worker nodes to poll a queue.
	- SNS is a push notification service, whereas SQS is message system that requires worker nodes to poll a queue.
11. What application service allows you to decouple your infrastructure using message based queues?
- SQS
	- In IT the term 'message' can be used in the common sense, or to describe a piece of data of Task in an asynchronous queueing system such as MQseries, RabbitMQ or SQS.
12. Amazon's SQS service guarantees a message will be delivered at least once.
- True
	- Standard queues provide at-least-once delivery, which means that each message is delivered at least once. Reference: [Amazon SQS FAQs](https://aws.amazon.com/sqs/faqs/ "null").
13. Amazon SWF ensures that a task is assigned only once and is never duplicated.
- True
	- One time only completion is a key feature of SWF. At one time this was a key distinction from SQS, however with SQS FIFO queues, this is no longer a distinguishing feature. Reference: [Amazon SWF FAQs](https://aws.amazon.com/swf/faqs/ "null").
14. What is Amazon Kinesis Data Streams?
- What is Amazon Kinesis Data Streams?
	- Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. The data collected is available in milliseconds to enable real-time analytics use cases such as real-time dashboards, real-time anomaly detection, dynamic pricing, and more.
15. Amazon SWF is designed to help users **\_\_\_\_**.
- Coordinate synchronous and asynchronous tasks
	- Similar to SQS SWF manages queues of work, however unlike SQS it can have out-of-band parallel and sequential task to be completed by humans and non AWS services
16. Amazon Kinesis Data Firehose is used for ...
- Loading data into data stores and analytics tools
- Capturing, transforming and loading streaming data into Amazon S3
	- Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today.

