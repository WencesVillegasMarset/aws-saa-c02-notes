# AWS-SAA-C02 Study Notes


## EC2

Dimensions: vCPUs, Memory, Storage size and type and Network Performance

c4 Compute optimized—For workloads requiring significant processing
r3 Memory optimized—For memory-intensive workloads
i2 Storage optimized—For workloads requiring high amounts of fast SSD storage
g2 GPU-based instances—Intended for graphics and general-purpose GPU compute
workloads

AMIs: Snapshots of EC2 instances, they have os config, patches and apps.

Addressing Instances:

* Public Domain Name System (DNS) Name
* Public IP, it lives until the instance stops running
* Elastic IP, independent from instance state.


Security Groups: They are stateful firewalls.

Type of Security Group | Capabilities
EC2-Classic Security Groups | Control outgoing instance traffic
VPC Security Groups | Control outgoing and incoming instance traffic

Security group policies overlap, they don't cancel each other out or are mutually exclusinve.

Port, Protocol, Source/Destination

Bootstrapping instances: UserData field, you can add commands to execute at creation of instance.

VM Import/Export

You can get Instance Metadata from the AWS API.

Modifications after launch:

* Instance Type, you need to stop the isntance in order to do it.
* Security Grooups: Only if they are running within a VPC


Pricing:

* On-Demand: Standard type, the most flexible and expensive.
* Reserved instances: Specific instance type and AZ to save up to 75%, more long term commitment. 
  * All upfront
  * Partial upfront
  * Monthly basis
* Spot instances: not time critical and tolerant to interruptions, set a bidding price.

The more upfront, the bigger the discount.

Reservation changes:
* Switch Availability Zones within the same region.
* Change between EC2-VPC and EC2-Classic.
* Change the instance type within the same instance family (Linux instances only)

Tenancy: Shared (default), Dedicated Instances, Dedicated Host.


**Placement groups**: A placement group is a logical grouping of instances within a single Availability Zone. Placement groups enable applications to participate in a low-latency, 10 Gbps network.

TODO: PUT PLACEMENT GROUP CLAS 

Instance Store: Instance stores are included in the cost of an Amazon EC2 instance, so they are a very costeffective solution for appropriate workloads. The key aspect of instance stores is that they are temporary. 
Data in the instance store is lost when:
* The underlying disk drive fails.
* The instance stops (the data will persist if an instance reboots).
* The instance terminates.

## EBS
* Each Amazon EBS volume is automatically replicated within its Availability Zone to protect you from component failure.
* Multiple Amazon EBS volumes can be attached to a single Amazon EC2 instance, although a volume can only be attached to a single instance at a time.
* EBS volumes support live configuration changes while in production. You can modify volume type, volume size, and IOPS capacity without service interruptions

Types of EBS Volumes:

* Magnetic Volumes
  * Workloads where data is accessed infrequently.
  * Sequential reads
  * NOT BOOTABLE!
  * Situations where low-cost storage is a requirement
  * **Throughput-Optimized HDD**: provides low-cost magnetic storage that defines performance in terms of throughput rather than IOPS. These volumes are ideal for large sequential workloads.
    * Frequently used data also.
  * **Cold HDD**:  provides low-cost magnetic storage that defines performance in terms of throughput rather than IOPS. These volumes are ideal for large, sequential, cold-data workloads. Cheapest of them all. 
    * Cold data.
* **General-Purpose SSD**
  * System boot volumes, Small- to medium-sized databases, Development and test environments
  * Can handle bursts of IOPS, IOPS credits and more...
* **Provisioned IOPS SSD**
  * Critical business applications that require sustained IOPS performance
  * Large database workloads
  * Max: 32,000 IOPS and 64,000 on Nitro instances.

Amazon EBS-Optimized Instances: Select them when using volumes other than magnetic, to improve EBS I/O, this adds a charge.

Protecting Data:
* Backup/Recovery (Snapshots): Through the AWS Management Console, Through the CLI, Through the API, By setting up a schedule of regular snapshots.
  * To use a snapshot you create a Volume from it. Use the same principle to increase volume size. 
  * Snapshots are publicly shareable. 
  * You can create multi-volume snapshots, which are point-in-time snapshots for all EBS volumes attached to an EC2 instance.


**Amazon Data Lifecycle Manager (Amazon DLM)**: 

* Automate the creation, retention, and deletion of snapshots taken to back up your Amazon EBS volumes.
* DLM also supports EBS-backed AMIs, they include an EBS snapshot for each volume attached to the source instance.
* Tags are used to select what to backup or not.
* You can create up to 100 lifecycle policies per Region.
* You can add up to 45 tags per resource.

Links:
* https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshot-lifecycle.html
* https://tutorialsdojo.com/amazon-ebs/%20?src=udemy
* http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumes.html

**Encryption**: Amazon EBS offers native encryption on all volume types. When you launch an encrypted Amazon EBS volume, Amazon uses the AWS Key Management Service (KMS) to handle key management. A new master key will be created unless you select a master key that you created separately in the service. Your data and associated keys are encrypted using the industry-standard AES-256 algorithm.

## S3

* Server-side encryption
  * is about data encryption at rest
  * Amazon S3-Managed encryption keys (SSE-S3) using 256-bit Advanced Encryption Standard (AES-256) block cipher
    * It encrypts the key itself with a master key that it rotates regularly.
    * Request header for SSE-S3: `x-amz-server-side-encryption`
  * Customer provided encryption keys (SSE-C):
    * Headers: `x-amz-server-side​-encryption​-customer-algorithm` , `x-amz-server-side​-encryption​-customer-key` , `x-amz-server-side​-encryption​-customer-key-MD5`
* Deletion:
  * Enabling S3 Object Lock prevents your existing and future records from being deleted or overwritten.


Links:

* https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html

## AWS CloudWatch

Monitoring service for AWS cloud resources and the applications you run on AWS

* To monitor custom metrics, you must install the CloudWatch agent on the EC2 instance.
  * Some custom metrics: SwapUtilization


## VPC

The Amazon VPC service was released after the Amazon EC2 service; because of this, there
are two different networking platforms available within AWS: EC2-Classic and EC2-VPC.
Amazon EC2 originally launched with a single, flat network shared with other AWS
customers called EC2-Classic. As such, AWS accounts created prior to the arrival of the
Amazon VPC service can launch instances into the EC2-Classic network and EC2-VPC. AWS
accounts created after December 2013 only support launching instances using EC2-VPC. AWS
accounts that support EC2-VPC will have a default VPC created in each region with a default
subnet created in each Availability Zone. The assigned CIDR block of the VPC will be
172.31.0.0/16.

Subnets:

AWS reserves the first four IP addresses and the last IP address of every subnet for internal networking purposes.

Subnets can be classified as public (traffic directed to IGW), private, or VPN-only (directed to VPG). The internal IP address range of the subnet is always private.

Default Amazon VPCs contain one public subnet in every Availability Zone within the region,
with a netmask of /20.


Route Tables:
A route table is a logical construct within an Amazon VPC that contains a set of rules (called
routes) that are applied to the subnet and used to determine where network traffic is
directed. A route table’s routes are what permit Amazon EC2 instances within different
subnets within an Amazon VPC to communicate with each other

You should remember the following points about route tables:
* Your VPC has an implicit router.
* Your VPC automatically comes with a main route table that you can modify.
* You can create additional custom route tables for your VPC.
* Each subnet must be associated with a route table, which controls the routing for the
subnet. If you don’t explicitly associate a subnet with a particular route table, the subnet
uses the main route table.
* You can replace the main route table with a custom table that you’ve created so that each
new subnet is automatically associated with it.
* Each route in a table specifies a destination CIDR and a target; for example, traffic
destined for 172.16.0.0/12 is targeted for the VPG. AWS uses the most specific route that
matches the traffic to determine how to route the traffic.

Internet Gateways:
An IGW provides a target in your Amazon VPC route tables for Internet-routable
traffic, and it performs network address translation for instances that have been assigned
public IP addresses.

You must do the following to create a public subnet with Internet access:
* Attach an IGW to your Amazon VPC.
* Create a subnet route table rule to send all non-local traffic (0.0.0.0/0) to the IGW.
* Configure your network ACLs and security group rules to allow relevant traffic to flow to and from your instance.

Subnets and IP Addreses:

* By default, nondefault subnets have the IPv4 public addressing attribute set to false, and **default subnets have this attribute set to true**. An **exception is a nondefault subnet** created by the Amazon EC2 launch instance wizard — the wizard sets the attribute to true. You can modify this attribute using the Amazon VPC console.

* You need to add IPv4 subnet first before you can create an IPv6 subnet

DHCP:

Elastic IPs:
You must first allocate an EIP for use within a VPC and then assign it to an instance.
EIPs are specific to a region (that is, an EIP in one region cannot be assigned to an
instance within an Amazon VPC in a different region).
There is a one-to-one relationship between network interfaces and EIPs.
You can move EIPs from one instance to another, either in the same Amazon VPC or a
different Amazon VPC within the same region.
EIPs remain associated with your AWS account until you explicitly release them.
There are charges for EIPs allocated to your account, even when they are not associated with a resource

Elastic Network Interface:

**VPC Peering**

This is not transitive, meaning that if a VPC1 and VPC2 are peered and VPC1 has a NAT Gateway, VPC2 cant access the internet via it's peer connection and then gateway.

**How to create a VPN connection to a corporate network?**

To create a VPN connection, you must create a customer gateway resource in AWS, which provides information to AWS about your customer gateway device. Next, you have to set up an Internet-routable IP address (static) of the customer gateway's external interface. 
You can enable access to your network from your VPC by attaching a virtual private gateway to the VPC, creating a custom route table, updating your security group rules, and creating an AWS managed VPN connection.

https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_VPN.html
https://docs.aws.amazon.com/vpc/latest/userguide/SetUpVPNConnections.html

**Bastion**:

* A bastion host is a special purpose computer on a network specifically designed and configured to withstand attacks.
* It should be in a public subnet with either a public or Elastic IP address.
* It is used to gain access to a subnet from outside the VPC (i.e corporate network).

Links:
* Bastion: https://docs.aws.amazon.com/quickstart/latest/linux-bastion/architecture.html

## RDS

* Parameter Groups can be used to apply the same set of settings to a a group of instances.
* Multi-AZ deployments: Creating Multi-AZ DB Instance automatically creates a primary DB Instance and synchronously replicates the data to a standby instance.
  * When the primary instance fails the CNAME is swithed from the primary to the standby instance which is promoted.
  * Failover conditions:
    * Loss of availability in primary Availability Zone
    * Loss of network connectivity to primary
    * Compute unit failure on primary
    * Storage failure on primary


Links:
* https://aws.amazon.com/rds/features/multi-az/

## Route53

* Routing Policies: 
  * Geolocation: route traffic based on the geographic location of your users, meaning the location that DNS queries originate from
    * Uses: restrict distribution of content, balancing load across endpoints, etc.
  * Geoproximity: route traffic to your resources based on the geographic location of your users and your resources. 
    * You can also optionally choose to route more traffic or less to a given resource by specifying a value, known as a bias. 
    * A bias expands or shrinks the size of the geographic region from which traffic is routed to a resource.
  * Latency: serve user requests from the AWS Region that provides the lowest latency. 
    * It does not, however, guarantee that users in the same geographic region will be served from the same location
  * Weighted: associate multiple resources with a single domain name or subdomain name and choose how much traffic is routed to each resource.
* Failovers:
  * Set failovers to  static S3 websites bucket or CloudFront distributions is possible.

*Routing traffic to a website that is hosted in an Amazon S3 Bucket*:
1. An S3 bucket that is configured to host a static website. The bucket must have the same name as your domain or subdomain. For example, if you want to use the subdomain portal.tutorialsdojo.com, the name of the bucket must be portal.tutorialsdojo.com
2.  registered domain name. You can use Route 53 as your domain registrar, or you can use a different registrar
3.  Route 53 as the DNS service for the domain. If you register your domain name by using Route 53, we automatically configure Route 53 as the DNS service for the domain.

*Routing traffic to an ELB load balancer*

* use Amazon Route 53 to create an alias record that points to your load balancer.
* Alias with a type "AAAA" record set and Alias with a type "A" record set, AAAA for IPV6.






## Auto Scaling

* Scheduled
* Predictive: . Predictive scaling uses machine learning to analyze each resource’s historical workload and regularly forecasts the future load for the next two days.
* Dynamic: Dynamic scaling creates target tracking scaling policies for the resources in your scaling plan

## Identity and Access Management 

Principals:

An entity that is allowed to interact with Resources. Root user, IAM Users and Roles/Temporary Security Tokens

IAM Policies: They are attached to principals. They contain permissions, each permission is composed of (Effect - allow/deny, Service, Resource - arn), Action, Condition)

## EFS: Elastic File System

* Multiple EC2 instances can acces EFS at the same time.

## SQS

* Types of queues:
  * Standard Queue offers nearly unlimited throughput. At-least once delivery, Best-Effort Ordering. 
  * FIFO Queue with throughput limited to 300 TPS and up to 3000 messages per second. Messages sent only once,
* Redundant within 3 AZs.
* Messages are retained for up to 14 days.
* Scales automatically up and down.
* Messages can contain up to 250KB of text data.
* Important parameters:
  * Message Retention Period: Retention if it's not deleted. Between 1 min and 14 days.
  * Max message size: 1KB to 250KB.
  * Delivery Delay: This is visibility period, time between sending and the message being visible to consumers. From 0 to 15 mins. For standard a change is not Retroactive for FIFO it is.
  * Recieve message wait time: Short (wait-time == 0s) or long polling (waiting from 1 to 20 secs), the latter reduces costs since it eliminates empty messages. 
  * Content based deduplication: ONLY FIFO, you enable hashing of the body with SHA256 to generate a content-based deduplication ID.
* Dead letter queues are supported.
* Server-side encryption is supported with keys managed my KMS.
* You cannot set a priority to individual items in the SQS queue



## DynamoDB

* DDB Streams enables publishing modification events on a table that can be captured by lambdas polling the stream.
* In-Memory Acceleration with DynamoDB Accelerator (DAX): primarily used for read performance improvement of your DynamoDB table from milliseconds response time to microseconds.
* 


## AWS Lambda

* Deployment configuration types:
  * Canary: Traffic is shifted in two increments. Replace certain percentage, wait some time, then replace the rest.
  * Linear: Increments between intervals until it's complete.
  * All-at-once: All traffic is shifted from the original Lambda function to the updated Lambda function version all at once.
* Languages supported: Java, Nodejs, Python, C#, GO, PowerShell, Ruby
* Memory range: 128mb to 300mb. 
* TMP disk capacity: 512mb
* File descriptors and processes: both 1024
* Function timeout: 900s
* Concurrent executions: 1000 default, can be increased via support ticket.
* Deployment package size: 50zip, 250mb unzipped
* Deployment packages size per region: 75gb.
* Usage patterns: Real-time File Processing, Stream Processing, ETL, Replacing CRON, Processing AWS Events i.e CloudTrail

## API Gateway

* Types: REST API, WebSocket API, HTTP API (Proxy to http backends or Lambda).
* It provides
  * Resiliency and scale
  * Request caching.
  * Security features: IAM, cognito etc
  * Metering: define plans that meter and restrict third-party developer access to your APIs. Visualize API Key usage and generate Keys as well.
  * Monitoring dashboard.
  * Deployment stages.
  * Open API Spec support.
  * SDK generation for iOS, Android and JS.
  
## Kinesis

Build real-time streaming analytics and real-time apps.

Sub-products: Kinesis Data Streams, Kinesis Data Firehose, Kinesis Data Analytics, Kinesis Video Streams.

Data Streams:
* Process and analyze streaming data, using Kinesis Client Library.
* Create a stream and publish with Kinesis Producer Library and then consume with Kinesis Client Library.
* You can have multiple consumers. E.g one processing for analytics and one dumpling the data to s3.
* The streaming data is replicated to 3 AZs within a region.
* You can retain data for up to 7 days as max increase, othewise 24h.
* Components:
  * Data Stream: Logical grouping of shards.
  * Shard: Append only log, ingest up to 1000 records per second, up to 5 read transactions per second.
  * Partition Key, Sequence Number.
* Pricing: 
  * Charged by shard by hourly rate.
  * Per million payloads billing.
* KCL: Kinesis Client Library
  * Watch out for lease table write capacity (DynamoDB)!

Firehose:
* You can load streaming data into data stores.
* You pay only for the data you transmit.
* The streaming data is replicated to 3 AZs within a region.

Data Analytics:
* Use standard SQL to process and analyze streams.
* Simply point Kinesis Data Analytics at an incoming data stream, write your SQL queries, and specify where you want to load the results. The queries run continously.

Use cases: Real-time Time Series analytics, Feed real-time dashboards, Real-time alarms and notifications. 

Links: 

* https://tutorialsdojo.com/amazon-kinesis/?src=udemy
* KCL: https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html#shared-throughput-kcl-consumers-overview

## AWS Elastic BeanStalk

You can deploy Web Server based apps and Worker based apps (a background process listening to SQS).

* Components of a Beanstalk app:
  * Environment: Infrastructure supporting the app, you can define multiple envoronments: DEV, STG and PRD for example.
  * Application Varsion: Your app code, stored in S3.
  * Saved Configuration: It defines how an environment and its resources should behave.
* You can deploy single-instance or multi instance. AWS provisions the necessary load-balancers and other stuff.
  * Database runs on RDS and is optional.
* You can add AWS Elastic Beanstalk configuration files to your web application’s source code to configure your environment and customize the AWS resources that it contains.
* Developement stacks supported:
  * Apache Tomcat for Java applications
  * HTTP Server for PHP applications
  * HTTP Server for Python applications
  * Nginx or Apache HTTP Server for Node.js applications
  * Passenger or Puma for Ruby applications
  * Microsoft IIS 7.5, 8.0, and 8.5 for .NET applications
  * Java SE
  * Docker
  * Go

## Amazon Cogniro
User identity and data synchronization service that makes it really easy for you to manage user data for your apps across multiple mobile or connected devices

* You can create identities for users from login providers like Google, FB, Amazon and enterprise like Active Directory SAML.
* It supports unauthenticated identities, that save data and associate it with the profile when the proper id is created.
* Define roles and map users to different roles so your app can access only the resources that are authorized for each user.

## AWS OpsWorks

* Provides managed instances of Chef or Puppet (automation platforms).


Links:
* https://aws.amazon.com/opsworks/
* https://tutorialsdojo.com/aws-opsworks/




## EMR

* Load the data into S3.
* Define how many cluster nodes you need, instance types and applications to install in the cluster.
* The output is placed in S3
* Node types:
  * Master node: This node takes care of coordinating the distribution of the job across core and task nodes.
  * Core node: This node takes care of running the task that the master node assigns. This node also stores the data in the Hadoop Distributed File System (HDFS) on your cluster.
  * Task node: This node runs only the task and does not store any data. The task nodes are optional and provide pure compute to your cluster.

With Amazon EMR you can run MapReduce and a variety of powerful applications and frameworks, such as Hive, Pig, HBase, Impala, Cascading, and Spark.

## Auto Scaling

You can scale EC2 instances and: EC2 spot instances, EC2 Container Service (ECS), Map Reducer (EMR) clusters, AppStream 2.0 instances, Amazon Aurora Replicas and DynamoDB.

With EC2:

 Add the EC2 instances to an Auto Scaling group, define the minimum and maximum number of servers, and then define the scaling policy. Auto Scaling takes care of adding and deleting the servers and integrating them with ELB based on the usage.

 *Scaling Plan* 

 By using a scaling plan, you can configure and manage the scaling for the AWS resources you are going to use along with Auto Scaling.

 To identify scalable resources you can use CloudFormation Scan, search by tags or add AutoScaling groups.

 Scaling Strategies:

 * Optimize for Availability: Makes sure the services are available
 * Balance Availability and Cost: CPU/Resource utilization is kept at 50%.
 * Optimize for Cost: CPU/Resource utilization is kept at 70%.
 * Custom Scaling Strategy: Decide your own value

All these can be predictive or dynamic.
* Predictive Scaling: Demand is forecasted.
* Dynamic Scaling: target tracking scaling policies are created for the resources in your scaling plan, so you select a target value and auto scaling tries to keep it that way.

### EC2 Auto Scaling

* Define a launch configuration which can be saved and reused: AMI (Amazon machine image) details, instance type, key pair, security group, IAM (Identity and Access Management) instance profile, user data, storage attached, and more.
* Set a minimum and maximum number of instances running at any time.
* Select a Scale format:
  * Maintaining the instance level: define the minimum or the specified number of servers that will be running all the time.
  * Manual Scaling: via api or cli.
  * Scaling as per the demand: You can scale according to various CloudWatch metrics such as an increase in CPU, disk reads, disk writes, network in, network out, ... 
  * Scaling as per schedule.
* You can configure SNS notifications
* Auto Scaling groups are limited in number but increasable via support tickets.
* The groups cannot span regions.
* **Recommended**: It is recommended that you use the same instance type in an Auto Scaling group since you are going to have effective load distribution when the instances are of the same type.

Simple Scaling: You need to define a par of scale up and scale down policies. You define a cooldown period for when to effectively kill an instance.
Simple Scaling with Steps: You can specify changes in capacity in absolute step terms, like 2 instances more or percentages.
Target-Tracking Scaling: Auto Scaling will automatically scale up or scale down to mantain the established target value.

Termination Policy: The termination policy determines which EC2 instance you are going to shut down first
Modes:
* longest-running server 
* not patched servers
* servers close to billing an hour
* oldest launch configuration

## EKS

* Managed Kubernetes, this is no vendor specific distro unlike ECS.

Links:
* https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html


## Amazon Simple Workflow Service 

Build applications that coordinate work across distributed components.

Link: 
* http://docs.aws.amazon.com/amazonswf/latest/developerguide/swf-welcome.html
* https://tutorialsdojo.com/amazon-simple-workflow-swf-vs-aws-step-functions-vs-amazon-sqs/?src=udemy

## AWS IoT Core

AWS IoT Core allows you to connect multiple devices to the cloud and to other devices without requiring you to deploy or manage any servers. You can also filter, transform, and act upon device data on the fly based on the rules you define.

Links:
* https://aws.amazon.com/iot-core/

## AWS WorkDocs

Share content, provide rich feedback, and collaboratively edit documents


## AWS Snowball

As a rule of thumb, if it takes more than one week to upload your data to AWS using the spare capacity of your existing Internet connection, then you should consider using Snowball.

* https://tutorialsdojo.com/aws-cheat-sheet-s3-transfer-acceleration-vs-direct-connect-vs-vpn-vs-snowball-vs-snowmobile/


## AWS WAF

To detect and mitigate DDoS attacks, you can use AWS WAF in addition to AWS Shield. AWS WAF is a web application firewall that helps detect and mitigate web application layer DDoS attacks by inspecting traffic inline

Simples Behaviours:

* Allow all requests except the ones that you specify
* Block all requests except the ones that you specify
* Count the requests that match the properties that you specify

Links: 
* https://tutorialsdojo.com/aws-waf/?src=udemy

## Amazon GuardDuty

threat detection service

## AWS Secrets Manager

Use AWS Secrets Manager to store and encrypt the database credentials, API keys, and other secrets. Enable automatic rotation for all of the credentials.

## AWS DataSync

Move large amounts of data online between on-premises storage and Amazon S3, Amazon Elastic File System (Amazon EFS), or Amazon FSx for Windows File Server.

DataSync eliminates or automatically handles many of these tasks, including scripting copy jobs, scheduling, and monitoring transfers, validating data, and optimizing network utilization. The DataSync software agent connects to your Network File System (NFS), Server Message Block (SMB) storage, and your self-managed object storage, so you don’t have to modify your applications.

* use DataSync to migrate active data sets or archives to AWS
* replicate data to AWS for business continuity
* deploy the DataSync agent, connect it to your file system, select your AWS storage resources, and start moving data between them

Pricing: You pay only for the data you move


## Elastic Load Balancing

**Network Load Balancer**: 
* functions at the fourth layer of the Open Systems Interconnection (OSI) model, transport layer
* You CAN assign IPs to these NLB, not like Application ones which work on the 7th layer.
* If extreme performance and static IP is needed for your application then it is recommend that you use Network Load Balancer

**Application Load Balancer**:

* If you need flexible application management and TLS termination then it is recommended to use Application Load Balancer
* After the load balancer receives a request, it evaluates the listener rules in priority order to determine which rule to apply, and then selects a target from the target group for the rule action


**Classic Load Balance**

* If your application is built within the EC2 Classic network then you should use Classic Load Balancer.
* 

## AWS GLobal Accelerator

* Global Accelerator is a good fit for non-HTTP use cases
  * such as gaming (UDP)
  * IoT (MQTT)
  * Voice over IP
  * as well as for HTTP use cases that specifically require static IP addresses or deterministic, fast regional failover


## AWS CloudFront

* CloudFront improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery


## AWS Config

enables you to assess, audit, and evaluate the configurations of your AWS resources

* monitors and records your AWS resource configurations
* automate the evaluation of recorded configurations against desired configurations
* determine your overall compliance against the configurations specified in your internal guidelines
* review changes in configurations and relationships between AWS resources

Different from CloudTrail which cannot enforce rules to comply with your organization's policies.

## Amazon ECS

* Batch processing:
  * Batch jobs are often short-lived and embarrassingly parallel. You can package your batch processing application into a Docker image so that you can deploy it anywhere, such as in an Amazon ECS task.
  * use Amazon ECS Run Task action to run one or more tasks once. The Run Task action starts the ECS task on an instance that meets the task’s requirements including CPU, memory, and ports.


## Amazon API Gateway

create, publish, maintain, monitor, and secure APIs at any scale

* Enables you to build RESTful APIs and WebSocket APIs that are optimized for serverless workloads
* You pay only for the API calls you receive and the amount of data transferred out.

