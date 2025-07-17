
# AWS Certified Solutions Architect Associate  
## By Stephane Maarek  

Shared knowledge with Cloud Practitioner, Developer and SysOps courses.

AWS (Amazon Web Service) is a **Cloud Provider** providing with servers and services that you can use **on demand** and **scale easily**.  

# Section 3 AWS Global Infrastructure
History : since 2002

Use cases:
> Enterprise IT, Backup & Storage, Big Data Analysis  
> Website hosting, mobile & social apps  
> Gaming services & etc  

AWS Global Infrastructure
> AWS Regions  
> AWS Availability Zones  
> AWS Data Centers  
> AWS Edge Locations / Points of Presence  
> on [website](https://infrastructure.aws)

---

Regions
> Names **us-east-2** etc  
> A region is a **cluster of data centers**  
> Most AWS services are **region-scoped**  
> Choosing a Region  
> > **Compliance** with data governance and legal requirements: data never leaves without your explicit permission  
> > **Proximity** to customers: reduced latency  
> > **Available** services within a Region: some services and features are not available in some Regions  
> > **Pricing**: pricing varies region to region and is transparent in the service pricing page  

Availability Zones(AZ)
> Each region has 3-6 AZ, e.g. ap-southeast-2a(bc)  
> Each AZ is one or more discrete data centers with redundant power, networking and connectivity  
> They're seperate from each other, isolated from disaster  
> Connected with high bandwidth, ultra-low latency networking  


Points of Presence(Edge Locations)
> 400+ PoP (400+ EL & 10+ Regional Caches) in 90+ cities across 40+ countries  
> Content is delivered to end users with lower latency  

---

AWS Console  
> AWS Global Services  
> > Identity and Access Management (IAM)  
> > Route 53 (DNS service)  
> > CloudFront (CDN)  
> > WAF (Web Application Firewall)   
> 
> Most AWS services are Region-scoped  
> > Amazon EC2  
> > Elastic Beanstalk  
> > Lambda  
> > Rekognition  
>
> Check Region [table](https://https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)


# Section 4 IAM & AWS CLI  

### IAM Users and Groups

- IAM = Identity and Access Management, **Global** service.  
- **Root** account created by default, shouldn't be used or shared.  
- **Users** are people within org, and can be grouped  
- **Groups** only contain users, not other subgroups  
- Users don't have to belong to a group, user can belong to multiple groups.  
- Root account would **only show acocunt ID** if you click top right user name; while IAM user would also show the IAM user name

### IAM Permissions
- **Users or Groups** can be assigned JSON documents called policies.  
- These policies define **permissions** of the users.  
- In AWS you apply the **least privilege principle**: don't give more permissions than a user needs.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:Describe*",
            "Resources": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:ListMetrics",
                "cloudwatch:Describe"
            ],
            Resources: "*"
        }
    ]
}
```

### IAM Policies Inheritance  
- Explicit "DENY" overrides all "ALLOW"
- Special cases for resorce-based policies, otherwise implicit "DENY" overwrite "ALLOW"
- [Further reading](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

### IAM Policies Structure
- Version: policy language version, always include "2012-10-17"
- Id: an identifier for the policy(optional)
- Statement: one or more individual statements (required) which consists of  
    - Sid: an identifier for the statement (optional)
    - Effect: "Allow" / "Deny"
    - Principal: account/user/role to which this policy applied to
    - Action: list of actions this policy allows or denies  
    - Resource: list of resources to which the action applied to
    - Condition: conditions for when this policy is in effect (optional)  
 
### IAM Password Policy  
- Strong password = higher security for accounts
- In AWS, you can set up a password policy
  - Set a min password length
  - Require specific character types
  - Allow all IAM users to change their own passwords
  - Require users to change their password after some time(pw expiration)
  - Prevent pw re-use  

### Multi Factor Authentication - MFA
- Users have access to your account and can possibly change configurations or delete resources in your AWS account
- You want to protect your **Root** Accounts and IAM users
- MFA = pw you know + security device you own
- Main benefit of MFA: if a password is stolen or hacked, the account is not compromised.  

### MFA devices options
- Virtual MFA device - *Google Authenticator, Authy* - allow multiple tokens on a single device  
- Universal 2nd Factor (U2F) Security Key - *YubiKey* - allow multiple root and IAM users using a single security key   
- Hardware Key Fob MFA Device - *Gemalto*  
- Hardware Key Fob MFA Device for AWS GovCloud (US) - *SurePassID*  

### AWS Access Keys
- To access AWS, you have three options:
  - AWS Management Console (protected by password + MFA)
  - AWS Command Line Interface (CLI): protected by access keys
  - AWS Software Developer Kit(SDK): for code, protected by access keys
- Access Keys are generated through AWS Console
- Users manage their own access keys
- **Access Keys are secret, just like a password. Don't share them**
- ``aws configure``
- ``aws iam list-users``

### AWS CLI
- A tool that enables you to interact with AWS services using commands in your command-line shell
- Always start with ``aws``, direct access to the public APIs of AWS services
- You can develop scripts to manage your resources
- open-source
- Alternative to using AWS Management Console

### AWS SDK
- Language-specific APIs(set of libraries)
- Enables you to access and manage AWS services programmatically
- Embedded within your application
- Supports SDKs, mobile SDKs, IoT device SDK
- AWS CLI is built on AWS SDK for python  

### CloudShell
- top right of management console
- as if CLI with your console credentials
- ``--region`` to specify region
- upload and download features
- available only in some regions

### IAM Roles for Services
- Some AWS service will need to perform actions on your behalf
- To do so, we will assign **permissions** to AWS services with **IAM Roles**, used by **services** but not physical people
- E.g. EC2 Instance want to perform some action on AWS, we create IAM Role to permit its access to AWS  
- Comma roles:
  - EC2 Instance Roles
  - Lambda Function Roles
  - Roles for CloudFormation  

### IAM Security Tools
- IAM Credential Report (account-level)
  - a report that lists all your account's users and the status of their various credentials
  - IAM - Credential Report
- IAM Access Advisor (user-level)
  - Access advisor shows the service permissions granted to a user and when those services were last accessed
  - You can use this information to revise your policies (least privilege)
  - IAM - Access Management - Users - someUser - Access Advisor

### IAM Guidelines & Best Practice
- Don't use the root account except for AWS account setup
- One physical user = one AWS user
- Assign users to **groups** and assign permissions to groups
- Create a **strong password policy**
- Use and enforce the use of **MFA**
- Create and use **Roles** for giving permissions to AWS services
- Use Access Keys for Progammatic Access (CLI/SDK)
- Audit permissions of your account using IAM Credential Report & IAM Access Advisor
- **Never share IAM user and Access Key**

### IAM SUMMARY
- Users: mapped to a physical user, has a password for AWS Console
- Groups: contains users only  
- Policies: JSON document that outlines permissions for users or groups  
- Roles: for EC2 instances or AWS services  
- Security: MFA + Password Policy
- AWS CLI: manage AWS services using command-line  
- AWS SDK: manage AWS services using a programming language  
- Access Key: access AWS using CLI or SDK  
- Audit:  IAM Credential Reports and IAM Access Advisor

# Section 5 EC2 Fundamentals

### AWS Budget Setup

- Initially, only root is able to see billing details
- root - account - iam user and role access to billing information
- Bills, Free Tier, Budgets  

---

### Amazon EC2

- EC2 is one of the most popular of AWS' offering
- EC2 = Elastic Compute Cloud = Infrastructure as a Service
- It mainly consists the capability of:
  - Renting virtual machines (EC2)
  - Storing data on virtual drives (EBS) 
  - Distributing load across machines (ELB)
  - Scaling the services using an auto-scaling group (ASG)
- Knowing EC2 is fundamental to understand how the Cloud works

---

### EC2 sizing and configuration options

- OS: Linux, Win, Mac OS
- CPU: compute power and cores
- RAM
- Storage:
  - Network-attached (EBS, EFS)
  - hardware (EC2 Instance Store)
- Network card: speed of the card, public address
- Firewall rules: security group
- Bootstrap script(configure at first launch): EC2 User Data  

---

### EC2 User Data

- It is possible to bootstrap our instances using an **EC2 User Data** script
- **bootstrapping** means launching commands when a machine starts
- That script is **only run once** at the instance **first start**
- EC2 User Data is used to automate boot tasks such as:
  - Installing updates
  - Installing software
  - Downloading common files from the Internet
  - Anything you can think of
- The EC2 User Data Script runs with the root user - sudo privilege

---

### EC2 instance types: examples

t2.micro(part of free tier, 750 hours per month);  
t2.xlarge;  
c5d.4xlarge;  
r5.16xlarge;  
m5.8xlarge;  
different vCPU, Mem, Storage, Network perf, EBS Bandwidth

---

### EC2 instance launch hands-on

- Public IP may change after restarting, but private usually won't

---

### EC2 instance Types

- You can use different types of EC2 instances that are optimized for different use case. [weblink](https://aws.amazon.com/ec2/instance-types/) 
- [weblink](https://ec2instances.info)
- AWS has the following naming convention:
$$m5.2xlarge$$
- m: instance class
- 5: generation(AWS improve then over time)
- 2xlarge: size with the instance class

---

### EC2 instance types - General Purpose  

- Great for a diversity of workloads such as web servers or code repositories
- Balance between Compute, Memory, Networking
- Name: T,M

---

### Compute Optimized

- Great for compute-intensive tasks that require high performance processors:
  - Batch processing workloads
  - Media transcoding
  - High perf web servers
  - High perf computing
  - Scientific modeling and ML
  - Dedicated gaming server
- Name: C

---

### Memory Optimized

- Fast perf for workload that process large data sets in memory
- Use cases:
  - High perf, relational/non-relational databases
  - Distributed web scale cache stores
  - In-memory database optimized for Business Intelligence
  - Applications performing real-time processing of big unstructured data
- Name: R, X1, High Memory(U-1), Z1

---

### Storage Optimized

- Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage
- Use cases:
  - High frequency online transaction processing (OLTP) systems
  - Relational and NoSQL databases
  - Cache for in-memory databases (i.e. Redis)
  - Data warehousing applications
  - Distributed file system
- Name: I, D, H1

---

### Security Groups

- SG are the foundamental of network security in AWS
- They control how traffic is allowed into or out of EC2 instances
- SG only contains **Allow** rules
- SG rules can referece by IP or by security groups
- inboud and outbound traffic

---

### Security Groups Deeper Dive

- SG are acting as a firewall on EC2 instances
- They regulate
  - Access to Ports
  - Authorized IP(v4 or v6) ranges
  - Control of inbound network (from other to instance)
  - Control of outbound network (from instance to other) default allowing

---

### Security Groups Good to Know

- Can be attached to multiple instances
- Locked down to a region/VPC combination
- Does live "outside" the EC2 - if traffic is blocked the EC2 instance won't see it
- It's good to maintain one seperate security group for SSH access
- If your application is not accessible (**time out**), then it's a security group issue.
- If your application gives a "connection refused" error, then it's an application error or it's not launched
- All **inbound** traffic is **blocked** by default
- All **outbound** traffic is **authorized** by default
- SG can reference other SG

---

### Classic Ports

- 22 SSH, Secure Shell, log into a Linux instance
- 21 FTP, File Transfer Protocol, upload files into a file share
- 22 SFTP, Secure File Transfer Protocol, upload files using SSH
- 80 HTTP, access unsecured websites
- 443 HTTPS, access secured websites
- 3389 RDP, Remote Destop Protocol, log into a Windows instance  

---

### SSH Summary Table

System | SSH | Putty | EC2 Instance Connect
-- | -- | -- | --
Mac | yes | no | yes
Linux | yes | no | yes
Windows < 10 | no | yes | yes
Windows >= 10 | yes | yes | yes

``ssh -i *.pem ec2-user@ip-aadress``

---

### EC2 Instance Connect

Browser-based ec2 connection  
No ssh key option, but still provide temporary ssh key behind the scene, not visible to the user.  

---

### EC2 Instance Roles

- Rule of thumb - **NEVER** enter IAM Access Key ID or Secret Access Key into an ec2 instance
- instead use IAM roles
- instances - action - security - modify IAM role

---

### EC2 Instances Purchasing Options

- On-Demand Instances - short workload, predictable pricing, pay by second
- Reserved (1 & 3 years)
  - Reserved Instances - long workloads
  - Convertible Reserved Instances - long workloads with flexible instances
- Saving Plans (1 & 3 years) - commitment to an amount of usage, long workload
- Spot Instances - short workload, cheap, can lose instances (less reliable)
- Dedicated Hosts - book an entire physical server, control instance placement
- Dedicated Instances - no other customers will share your hardware
- Capacity Reservation - reserve capacity in a specific AZ for any duration

---

### EC2 On Demand

- Pay for what you use:
  - Linux or Windows - billing per second, after the first minute
  - All other OS - billing per hour
- Has the highest cost but no upfront payment
- No long-term commitment

- Recommended for **short-term and un-interrupted** workloads, where you can't predict how the application will behave

---

### EC2 Reserved Instances

- Up to 72% discount compared to On-demand
- You reserve a specific instance attributes **(Instance Type, Region, Tenancy, OS**)
- Reservation Period - **1 year** (discount) or **3 years** (more discount)
- Payment Options - No Upfront(+), Partial Upfront(++), All Upfront(+++)
- Reserved Instance's Scope - **Regional** or **Zonal**(reserve capacity in an AZ)
- Recommended for steady-state usage applications (i.e. database)
- You can buy and sell in the Reserved Instance Marketplace
- Prefer Saving Plans than RI

---

- **Convertible Reserved Instance**
  - Can change the EC2 instance type, instance family, OS, scope and tenancy
  - Up to 66% discount
  
---

### EC2 Savings Plans

- Get a discount based on long-term usage (up to 72% -same an RIs)
- Commit to a certain type of usage ($10/hour for 1 or 3 years)
- Usage beyond EC2 Savings Plans is billed at On-Demand price

- Locked to a specific instance family and AWS region (e.g. M5 in us-east-1)
- Flexible across:
  - Instance Size (e.g. m5.xlarge, m5.2xlarge)
  - OS
  - Tenancy (Host, Dedicated and Default)

---

### EC2 Spot Instances

- Can get a discount of up to 90% compared to On-demand
- Instances that you can "lose" at any point of time if your max price is less than current spot price
- The **MOST cost-efficient** instance in AWS

- Useful for workloads that are **resilient to failure**
	- Batch jobs
	- Data analysis
	- Image processing
	- Any distributed workloads
	- Workloads with a flexible start and end time

- **Not suitable for critical jobs or databases**

---

### EC2 Dedicated Hosts

- A physical server with EC2 instance capacity fully dedicated to your use
- Allows you address **compliance requirements** and **use your existing server-bound software licenses** (per-socket, per-core, per-VM software licenses)
- Purchasing Options
	- On-demand - pay per second for active Dedicated Host
	- Reserved - 1 or 3 years (No/Partial/All Upfront)
- The **most expensive** option

- Useful for software that have **complicated licensing model** (BYOL - Bring Your Own License)
- Or for companies that have **strong regulatory or compliance needs** 

- Compared to DI, per host billing; visibility of sockets, cores, host ID; affinity between a host and instance; targeted instance placement; add capacity using an allocation request 
- Visibility to lower level hardware

---

### EC2 Dedicated Instances

- Instances run on hardware that's dedicated to you
- May share hardware with other instances in same account
- No control over instance placement(can move hardware after Stop/Start)
- Per instance billing (compared to Dedicated Hosts)
- **Both DH, DI** enables the use of dedicated physical servers; automatic instance placement

---

### EC2 Capacity Reservations

- Reserve **On-Demand** instances capacity in a specific AZ for any duration
- You always have access to EC2 capacity when you need it
- **NO time commitment** (create/cancel anytime), **no billing discounts**
- Combine with Regional Reserved Instances and Saving Plans to benefit from billing discounts
- You're charged at On-Demand rate whether you run instances or not

- Suitable for **short-term, uninterrupted workloads that needs to be in a specific AZ**

---

### Right Options

- **On demand**: coming and staying in resort whenever we like, we pay the price
- **Reserved**: like planning ahead and if we plan to stay for a long time, we may get a good discount
- **Saving Plans**: pay a certain amount per hour for certain period and stay in any room type
- **Spot instances**: the hotel allows people to bid for the empty rooms and the highest bidder keeps the rooms. You can get kicked out at any time.
- **Dedicated Host**: we book an entire building of the resort
- **Capacity Reservation**: you book a room for a period with full price even you don't stay in it. Guarantee you can move in whenever you come in that period

--- 

### EC2 Spot Instance Requests

- Can get a discount of up to 90% compared to On-demand
- Define **max spot price** and get the instance while **current spot price < max**
	- The hourly spot price varies based on offer and capacity
	- If the current spot > max you can choose to **stop** or **terminate** your instance with a 2-minutes grace period
- Other Strategy: **Spot Block**
	- "Block" spot instance during a specified time frame (1 to 6 hours) without interruptions 
	- In rare situations, the instance may be reclaimed
- Used for batch jobs, data analysis, or workloads that are resilient to failures

---

### How to terminate Spot Instance

A Spot request: 
- Max price
- Desired number of instances
- Launch Specification
- Request type: **one-time | persistent**
- Valid from, Valid Until

A one-time request will go away after requirements are fulfilled and instances are launched.

A persistent request would be smart enough, when your spot instances get stopped or interrupted, to restart your smart instance.

Therefore, you have to cancel persistent spot request before you terminate or stop you running instances.  

**Cancelling a spot request does not terminate instances**.

State for spot request
open; failed; active; disabled; closed; cancelled
You can only go to state **cancelled** from **open, active or disabled state**.

---

### Spot Fleets

- Spot Fleets = set of Spots Instances + (optional) On-Demand Instances
- The Spot Fleet will try to meet the target capacity with price constraints
	- Define possible launch pools: instance type, OS, AZ
	- Can have multiple launch pools, so that the fleet can choose
	- Spot Fleet stops launching instances when reaching capacity or max cost
- Strategies to allocate Spot Instances
	- LowestPrice: from the pool of the lowest price (cost optimization, short workload)
	- Diversified: distributed across all pools (great for availability, long workloads)
	- capacityOptimized: pool with the optimal capacity for the number of instances
	- priceCapacityOptimized (recommended): pools with highest capacity available, then select the pool with the lowest price (best choice for most workloads)
- **Spot Fleets allow us to automatically request Spot Instance with the lowest price**

# Section 6 EC2 Solutions Architect Associate Level

### Private vs Public IP(IPv4)

- Networking has two sorts of IPs. IPv4 and IPv6:
	- IPv4: [0-255].[0-255].[0-255].[0-255], 32 bits
	- IPv6: [0-ffff] (8 times, concatenated by :), 128 bits
	
- In this course, only IPv4
- IPv4 is still the most common format used online
- IPv6 is newer and solves problems for the Internet of Things

- IPv4 allows for 3.7 billion different addresses in the public space

---

### Private vs Public IP Example

- Internet Gateway has public IP
- Each computer has a unique private IP in its private network
- This private IP can be the same if two are in different private network
- CIDR & subnet mask

---

### Private vs Public IP Fundamental Differences


- Public IP
	- Public IP means the machine can be identified on the internet (WWW)
	- Must be unique across the whole web (no two machines can have the same public IP).
	- Can be geo-located easily

- Private IP
	- Private IP means the machine can only be identified on a private network only
	- The IP must be unique across the private network
	- BUT two different private networks can have the same IPs
	- Machines connect to WWW using a NAT + internet gateway (a proxy)
	- Only a specified range of IPs can be used as private IP

---

### Private vs Public IP In AWS EC2 - Hands on

- By default, your EC2 machine comes with
	- A private IP for the internal AWS Network
	- A public IP, for the WWW

- When we are doing SSH into our EC2 machines, 
	- We can't use a private IP, because we are not in the same network
	- We can only use the public IP(unless using VPN)

- If your machine stopped and started, **the public IP can change**. 

---

### Elastic IPs

- When you stop and then start an EC2 instance, it can change its public IP.
- If you need to have a fixed public IP for your instance, you need an Elastic IP
- An Elastic IP is a public IPv4 IP you own as long as you don't delete it
- You can attach it to one instance at a time

- With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account

- You can only have 5 Elastic IP in your account (ask AWS to increase)

- Overall, try to **avoid** using Elastic IP
	- They often reflect poor architectural decisions
	- Instead, use a random public IP and register a DNS name to it
	- Or, as we'll see later, use a Load Balancer and don't use a public IP

- Under EC2-Network and Security - Elastic IPs

---

### Placement Groups

- Sometimes you want control over the EC2 Instance placement strategy
- That strategy can be defined using placement groups
- When you create a placement group, you specify one of the following strategies for the group
	- Cluster - clusters instances into a low-latency group in a single AZ
	- Spread - spreads instances across underlying hardware (max 7 instances per AZ per group) - critical applications
	- Partition - spreads instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 instances per group (Hadoop, Cassandra, Kafka)
- Network and Security - Placement Groups; when launching EC2 instances, find PG setting in ``advanced details``

---

### Placement Groups - Clusters

- Pros: Great network (10 Gbps bandwidth between instances with Enhanced Networking enabled - recommended)
- Cons: If the AZ (probably the rack) fails, all instances fails at the same time
- Use case
	- Big Data job that needs complete fast
	- HPC
	- Application that needs extremely low latency and high network throughput

---

### Placement Groups - Spread

- Completely opposite to clusters, minimized the failure risk.
- Each EC2 is assigned to different hardware (even within the same AZ)
- Pros: 
	- Can span across AZ
	- Reduced risk is simultaneous failure
	- EC2 instances are on different physical hardware
- Cons:
	- **Limited to 7 instances** per AZ per placement groups
- Use case:
	- Application that needs to maximize high availability
	- Critical Applications where each instance must be isolated from failure from each other

---

### Placement Groups - Partition

- Up to 7 partitions per AZ
- Can span across multiple AZs in the same region
- Up to 100s of EC2 instances
- The instances in a partition do not share racks with the instances in the other partitions
- A partition failure can affect many EC2 but won't affect other partitions
- EC2 instances get access to the partition information as metadata
- Use cases: HDFS, HBase, Cassandra, Apache Kafka

--- 

### Elastic Network Interfaces (ENI)

- Logical component in a VPC (virtual private cloud) that represents a **virtual network card**
- The ENI can have the following attributes
	- Primary private IPv4, one or more secondary IPv4
	- One Elastic IP (IPv4) per private IPv4
	- One Public IPv4
	- One or more security groups
	- A MAX address
- You can create ENI independently and attach them on the fly (move them) on EC2 instances for failover
- Bound to a specific availability zone (AZ)
- Won't cost money
- [Further Reading](https://aws.amazon.com/blogs/aws/new-elastic-network-interfaces-in-the-virtual-private-cloud/)
--- 

### EC2 Hibernate

- We know we can stop, terminate instances
	- Stop - the data on disk (EBS) is kept intact in the next start
	- Terminate - any EBS volumes (root) also set-up to be destroyed is lost
- On start, the following happens:
	- First start: the OS boots and the EC2 User Data script is run
	- Following starts: the OS boots up
	- Then your application starts, caches get warmed up, and that can take time!

- Introducing **EC2 Hibernate**:
	- The in-memory (RAM) state is preserved
	- The instance boot is much faster! (the OS is not stopped/restarted)
	- Under the hood: the RAM state is written to a file in the root EBS volume
	- The root EBS volume must be **large enough and encrypted**
	
- Use cases:
	- Long-running processing
	- Saving The RAM state
	- Services that take time to initialize

- Good to know that
- Supported Instance Families - C3, C4, C5, I3, M3, M4, R3, R4, T2, T4 ...
- Instance Ram Size - must be less than 150 GB
- Instance Size - not supported for bare metal instances
- AMI - Amazon Linux 2, Linux AMI, Ubuntu, RHEL, CentOS & Windows...
- Root Volume - must be EBS, encrypted, not instance store and large
- Available for **On-Demand, Reserved and Spot** Instance

- An instance can **NOT** be hibernated for more than 60 days
- ``uptime`` shows how long has the instance been turned on
--- 

# Section 7 EC2 Instance Storage TODO

### EBS Volume

- An EBS (Elastic Block Store) Volume is a network drive you can attach to your instance while they run
- It allows your instances to persist data, even after their termination
- They can only be mounted to one instance at a time (at the CCP level, we'll talk about multi-attach), multiple volumes can be attached to the same instance
- They are **bound to a specific availability zone (AZ)**
- Can be attached on-demand, i.e. can be un-attached, which make them powerful
- To make use of new volume in the system (mount), check [this](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-using-volumes.html)

- Analogy: think of them as a "network USB stick"
- Free tier: 30 GB of free EBS storage of type General Purpose (SSD) or Magnetic per month

- It's a network drive (i.e. not a physical drive)
	- It uses the network to communicate the instance, which means there might be a bit of latency
	- It can be detached from an EC2 instance and attached to another one quickly

- It's locked to an Availability Zone (AZ)
	- An EBS volume in us-east-1a cannot be attached to instance in us-east-1b
	- To move a volume across, you first need to snapshot it

- Have a provisioned capacity (size in GBs, and IOPS, I/O per second)
	- You get billed for all the provisioned capacity
	- You can increased the capacity of the drive over time

- EBS - Delete on Termination attribute
- Controls the EBS behavior when an EC2 instance terminates
	- By default, the root EBS volume is deleted (attribute enabled)
	- By default, any other attached EBS volume is not deleted (attr disabled)
- This can be controlled by the AWS console/CLI
- Use case: preserve root volume when instance is terminated


--- 

###  EBS Snapshots

- Make a backup (snapshot) of your EBS volume at a point in time
- Not necessary to detach volume to do snapshot, but recommended
- Can copy snapshots across AZ or Region

EBS Snapshot Features:
- EBS Snapshot Archive
	- Move a snapshot to an "archive tier" that is 75% cheaper
	- Takes within 24 to 72 hours for restoring the archive
- Recycle Bin for EBS Snapshots
	- Setup rules to retain deleted snapshots so you can recover them after an accidental deletion
	- Specify retention (from 1 day to 1 year)
- Fast Snapshot Restore(FSR)
	- Force full initialization of snapshot to have no latency on the first use (costly)

EBS Snapshots Hands-on
- Copy snapshots into other regions
- Create volume from snapshot - target AZ can be different
- Recycle bin - retention rule lock/unlock

--- 

### AMI - Amazon Machine Image



# Section 8 Elastic Load Balancer TODO

## ALB and CLB (Static DNS name)
Application LB and Classic LB.


## NLB (Static DNS name static ip)

## GWLB

## Sticky Session

## Cross Zone Load Balancing

## SSL/TLS Certificates and SNI

## Connection Draining / Deregistration Delay

## ASG and Scaling Policies

### Why ASG?
- Scale-in and scale-out
- Set number limit, min-desired-max
- Automatically register new instance to lb
- recreate instances if necessary

### ASG Template
- AMI + Instance Type
- EC2 User Data
- EBS Volumes
- Security Groups
- SSH key pairs
- IAM Roles for EC2 instances
- Network + Subnet Info
- LB info
- ... and sizes and policies

### Policies based on CloudWatch Alarm
- Dynamic Scaling
	- Targe Tracking Scaling
	- Simple / Step Scaling -- alarm triggered, add 2 remove 1
- Scheduled Scaling
	- Anticipated scaling based on usage pattern
- Predictive Scaling -- based on forecast
- metrics
	- CPU Utilization
	- Request Count Per Target(instance)
	- Average Network I/O
	- Other custom metric
- Cool down period -- ASG won't launch or terminate instances during it -- Use ready-to-use ami to speedup setup and reduce cooldown period
 



# Section 9 RDS + Aurora + ElasticCache

## Relational Database Service

It's a **managed** DB service for DB use SQL as a query language. It allows you create database in the cloud that are managed by AWS. Supports Postgre, MySQL, MariaDB, Oracle, Microsoft SQL Server, IBM DB2, Aurora(AWS proprietary database)

WHY RDS? RDS is a managed service. 
- Automated provisioning, OS patching
- Continuous backup and restore to specific timestamp(Point in Time Restore)
- Monitoring Dashboard
- Read Replica for improved read performance
- Multi AZ for Disaster Recovery
- Maintenance windows for upgrade
- Vertical and horizontal scaling capability
- Storage backed by EBS
- **BUT** we cannot ssh into the underlying EC2 instances.

RDS - Storage Auto Scaling
- Helps you increase storage on your RDS DB instance dynamically. 
- When RDS detects you are running out of free database storage, it scales automatically. 
- It avoids manually scaling database storage.
- To use the feature, set **Maximum Storage Threshold** (maximum limit for DB storage)
- Automatically modify storage if:
	- Free storage is less than 10% of the allocated storage
	- Low storage lasts for at least 5 mins
	- 6 hours have passes since last modification
- Useful for application with **unpredictable workloads**
- Support all RDS database engines

## RDS Read Replica and Multi AZ

-  Read replica helps to scale your read 
-  Up to 15 read replicas
-  Within AZ, cross AZ or cross Region
-  Replication is **ASYNC**, so reads are **eventually consistent**
-  Replicas can be promoted to their own DB

Read Replica Use Case
	Reporting application run some analytics on Read Replica, leave the production database taking on normal load unaffected. Read Replica are used for **SELECT/read** only kind of statements

Read Replica Network Cost
- Normally, network cost when data goes from one AZ to another
- For RDS Read Replica **within the same region**, it's free.
- Cross region would be charged

RDS Multi AZ for Disaster Recovery
-  The Replication is **synchronous/SYNC**, changes to master DB would be replicated on standby instance synchronously
-  Share one DNS name with master -- in case master fail, there would be **automatic failover** to standby
-  Increase availability
-  Failover in case of loss of entire AZ, loss of network, instance or storage failure
-  No manual intervention in apps
-  Not used for scaling

Question: It there a possibility to set up your read replica as multi AZ for disaster recovery? Yes.

RDS - From Single-AZ to Multi-AZ
-  No need to stop main DB -- zero downtime operation
-  Just click on "modify" for the database
-  Internal process -- snapshot, new DD restored from the snapshot, sync establish

## RDS Custom

-  With RDS we don't have access to underlying OS or customization, but with RDS Custom, we do
-  Managed Oracle and Microsoft SQL Server Database with OS and database customization
-  RDS: Automatic setup, operation, and scaling of database in AWS
-  Custom: access to the underlying database and OS so you can
	- Configure settings
	- Install patches
	- Enable native features
	- Access the underlying EC2 instance using SSH or SSM Session Manager
-  It's recommended to **de-activate automation mode** to perform customization. Better to take a DB snapshot before.
- RDS vs RDS custom
	- RDS: entire database and the OS to be managed by AWS
	- RDS custom: full admin access to the underlying OS and the database 
## Aurora

Introduction

-  It is a propriety technology from AWS (not open source)
-  Postgre and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgre or MySQL database)
-  Aurora is "AWS cloud optimized" and claims high performance improvement over MySQL and Postgre on RDS
-  Aurora storage automatically grows in increments of 10 GB, up to 128 TB
-  Up to 15 replicas and replication process is faster than MySQL
-  Failover in Aurora is instantaneous. It's High Availability native.
-  Costs 20% more, more efficient

High Availability and Read Scaling

Storage of Aurora is replicated, self-healing and auto-expanding
-  6 copies of your data across 3 AZ
	- 4 copies out of 6 needed for writes
	- 3 copies out of 6 needed for reads
	- Self healing with peer-to-peer replication
	- Shared Storage Volume(logical) is striped across 100s of volumes
-  One Aurora Instance takes write (master), 
-  Automated failover for master in less than 30 seconds
-  Master + up to 15 Aurora Read Replicas serve reads
-  Support for Cross Region Replication

Aurora BD Cluster
Client connect writer endpoint to write to master, client connect reader endpoint (with connection load balancing) to read from auto-expanding replicas. LB happens at the connection level, not the sql statement level.  

Features
-  Automated fail-over
-  Backup and Recovery
-  Isolation and security
-  Inductry compliance
-  Push-button scaling
-  Automated Patching with Zero downtime
-  Advanced Monitoring
-  Routine Maintenance
-  Backtrack: restore data at any point of time without using backups

Aurora Replicas - Auto Scaling
Reader Endpoint would be extended to newly created instances

Aurora Replicas - Custom Endpoint
-  Define a subset of Aurora Instance
-  Example: Run analytical queries on specific replicas
-  The Reader Endpoint is generally not used after defining Custom Endpoints
-  Create more custom endpoints to cover all kinds of workloads

Aurora Serverless
- Automated db instantiation and auto-scaling based on actual usage
- Good for infrequent, intermittent, or unpredictable workloads
- No capacity planning needed
- Pay per second, can be more cost-effective

Global Aurora
-  Aurora Cross Region Read Replicas
	-  Useful for disaster recovery
	-  Simple to put in place
-  Aurora Global Database (recommended)
	-  One Primary region for (read/write)
	-  Up to 5 secondary (read-only) regions, replication lag is less than 1 seconds
	-  Up to 16 Read Replica per secondary region
	-  Helps for decreasing latency
	-  Promoting anther region (for disaster recovery) has an recovery time objective of < 1 minute
	-  **EXAM: Typical cross-region replication takes less than 1 second** 

Aurora Machine Learning
-  Enables you to add ML-based predictions to your applications via SQL
-  Simple, optimized, and secure integration between Aurora and AWS ML services
-  Supported services
	-  Amazon SageMaker (use with any ML model)
	-  Amazon Comprehend (for sentiment analysis)
- You don't  need to have ML experience
-  Use cases: fraud detection, ads targeting, sentiment analysis, product recommendations

## Backup and Monitoring

Automated backups
-  Daily full backup of the database (during backup window)
-  Transaction logs are backed-up by RDS every 5 mins
-  That leads to ability to restore to any point in time
-  1 to 35 days of retention, set 0 to disable automated backups

Manual DB snapshots
-  Manually triggered by the user
-  Retention of backup for as long as you want

In Aurora
-  Automated backups cannot be disabled (1 to 35 days)
-  Automated backups support point-in-time recovery in that timeframe
-  Manual snapshots similar to RDS manual DB snapshots

Trick: in a stopped RDS database, you will still pay for the storage. If you plan on stopping it for a long time, you should snapshot and restore instead.

Restore
-  Restoring a RDS/Aurora backup or a snapshot creates a new database
-  Restoring MySQL RDS database from S3
	-  Create a backup of your on-premises database, store it on S3, restore the backup file onto a new RDS instance running MySQL
-  Restoring MySQL Aurora cluster from S3
	- With the help of **Percona XtraBackup**

Aurora Cloning
-  Create a new Aurora DB cluster from an existing one
-  Faster than snapshot and restore
-  Use copy-on-wirte protocal
	-  Initially the new DB uses the same data volume as the original DB cluster
	-  When updates are made to either of the DB cluster, the additional storage is allocated and data is copied to be seperate
-  Very fast and cost effective
-  **Useful to create a "staging" database from a "production" database without impacting the production database**

## RDS Security and Proxy

Security
-  At-rest encryption:
	-  DB master and replicas encryption using AWS KMS - must be defined at launch time 
	-  If the master is not encrypted, the read replicas cannot be encrypted
	-  To encrypt an un-encrypted DB, go through a DB snapshot-restore as encrypted
-  In-fly encryption: TLS ready by default, use the AWS TLS root certificates (provided) on client side
-  IAM Authentication: IAM roles to connect to your database (instead of username/password)
-  Security Groups: Control network access to your RDS / Aurora DB
-  No SSH available except on RDS custom
-  Audit Logs can be enabled and sent to CloudWatch Logs for longer retention

RDS Proxy
-  Fully managed database proxy for RDS
-  Allow apps to pool and share DB connections established with the database
-  **Improving database efficiency by reducing the stress on database resources (CPU, RAM) and minimize open connections (and timeouts)**
-  Serverless, autoscaling, highly available (multi-AZ)
-  **Reduced RDS and Aurora failover time by up to 66%**
-  Support RDS (except Oracle and IBM) and Aurora 
-  No code changes required for most apps  
-  **Enforce IAM Authentication for DB, and securely store credentials in AWS Secrets Manager**
-  **RDS Proxy is never public accessible, must be accessed from VPC**
-  Lambda Functions is greatly helped by proxy.

## ElastiCache

Overview
-  The same way RDS is to get managed Relational Database
-  ElastiCache is to get managed Redis or Memcached
-  Caches are in-memory databases with really high performance, low latency
-  Helps reduce load off of databases for read intensive workloads
-  Helps make your application stateless
-  AWS take care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
-  **Using ElastiCache involves heavy application code changes**

Usage -- DB cache
-  Apps queries ElastiCache, cache hit, return; cache miss, get from RDS and store in ElastiCache
-  Helps relieve load in RDS
-  Cache must have an invalidation strategy to make sure only the most current data is used there.

Usage -- User Session Store
-  User logs into any of the application
-  The application writes the session data into ElasticCache
-  The user hits another instance of our application, 
-  then instance retrieves session data, don't have to log-in again

Redis VS Memcached
Redis
-  **Multi AZ** with Auto-Failover
-  **Read Replicas** to scale reads and have **high availability**
-  Data **Durability** using AOF persistence
-  **Backup and restore features**
-  Supports **Sets and Sorted Sets**

Memcached
-  Multi-node for **partitioning** of data (sharding)
-  **No high availability**
-  **Non persistent**
-  **No backup and restore**
-  Multi-threaded architecture

Security
-  ElastiCache supports IAM Authentication for Redis only
-  IAM policies on ElastiCache are only used for AWS API-level security
-  Redis-AUTH
	-  You can set a "password/token" when you create a Redis cluster
	-  This is an extra level of security for your cache (on top of security group)
	-  Support SSL in-flight encryption

Memcached
-  Support SASL-based authentication (advanced) 

Patterns for ElastiCache
-  Lazy Loading -- all read data is cached, can become stale
-  Write Through -- adds or update data in cache when written into DB
-  Session Store -- store temporary session data in cache (using TTL features)

Use Case
-  Gaming Leaderboards are computationally complex
-  **Redis Sorted Sets** guarantee both uniqueness and element ordering
-  Each time a new element is added, it's ranked in real time then added in correct order.

# Section 10 Route 53 and DNS

DNS: Translates the human friendly hostnames into the machine IP address. Backbone of the Internet. Uses hierarchical naming structure. 

**Domain Registrar**: Amazon Route 53, GoDaddy
**DNS records**: A, AAAA, CNAME, NS,
**Zone File**: contains DNS recors
**Name Server**: resolves DNS queries (Authoritative or Non-Authoritative)
**Root**: Implicit dot after all hostname, ...com.
**Top Level Domain**: .com, .us, .in, .gov, .org ....
**Secondary Level Domain**: amazon.com, google.com ...
**FQDN**: Fully qualified domain name api.www.example.com.

How DNS work
1. Ask local DNS server, return if cached, otherwise, do the following
2. Local DNS server ask root DNS server(ICANN), get NS IP record of TLD DNS server (.com NS)
3. Ask TLD DNS server (IANA), get NS IP record of SLD DNS server (example.com NS)
4. Ask SLD DNS server (Domain registrar), get A IP record of destination (example.com IP)
5. Cache and send back to web browser

## Route 53

- A **highly available, scalable, fully managed and Authoritative**(the customer, you, can update the DNS records) DNS. 
- Also a **Domain Registrar**
- Ability to check the health of resources
- The only AWS service which provides 100% availability SLA(service level agreement)'
- 53 is the traditional port used by DNS services

### DNS Records
- Define how you want to route traffic for a domain
- contains
	- Domain/subdomain Name
	- Record Type
	- Value
	- Routing Policy
	- TTL
- Route 53 supports DNS record types of A(ipv4), AAAA(ipv6), CNAME, NS ...
- CNAME -- hostname to another hostname, target must be A/AAAA record. Can't create a CNAME record for the top node of a DNS namespace (Zone Apex), i.e. cannot create for example.com but you can create for w3.example.com
- NS -- Name Servers for the Hosted Zone, control how traffic is routed for a domain

### Hosted Zones (every root domain has a hosted zone)
A container for records that define how to route traffic to a domain and its subdomain

Public and Private Hosted Zone. The latter can only be used in one or more VPCS (private domain names).

Registering a domain -- would have NS record and SOA record

dig/nslookup install
``sudo yum install -y bind-utils``

### TTL 

Time to live (cache on client side) (not to query DNS too often)  
Good practice -- when you want to change the record, decrease TTL and increase it back when all is settled.
Except for Alias record, TTL is mandatory for each DNS record!

### CNAME vs Alias records

AWS resources (LB, cloudfront...) expose an AWS hostname

- CNAME points a hostname to another hostname, only for (from) non-root domain name
- Alias points a hostname to an AWS resource, works for root and non-root domain (mydomain.com => someresources.aws.com) ; **Free of charge, native health check**. 


Alias 
- maps a host name to an AWS resource. 
- It's an extension to DNS functionality. 
- Automatically recognizes changes in the resources' IP address. 
- Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex)
- Always type A/AAAA for AWS resources
- Cannot set the TTL, set automatically
- Targets: ELB, CloudFront, API GW, Elastic Beanstalk, S3 Websites, VPC Interface Endpoints, Global Accelerator, Route 53 Record (Hosted Zone)
- You cannot set an Alias record for an EC2 DNS name! (Use A DNS record would be sufficient?)


## Routing Policies

Define how Route 53 responds to DNS queries. Not like load balancer routing traffics. DNS only respond to DNS queries, does not route traffic. 

***Simple (No HC)***\
- Route to a single resource.
- Can specify multiple values in the same record. 
- All of them are sent to the client, and client would choose a random one.
- When Alias enabled, specify only one AWS resources.
- Can't be associated with Health Check.

***Weighted (HC)***
- Control the percentage of request go to each specific record.
- relative weight, not necessarily add up to 100
- DNS records must have same name and type
- Can be associated with Health Checks
- Use case: Load balancing  between regions, testing new application versions
- Assign 0 -- stop sending traffic to a resource
- All 0 -- all records are returned with equal weights

***Latency (HC)***
- Redirect to the resource that has the least latency close to clients
- Super helpful when latency for users is a priority
- **Latency is based on traffic between users and AWS regions**
- Germany users may be directed to the US.
- Can be associated with Health Check, has a failover capability
- Has to **configure region** when setting up record.

***Health Checks***
- Health Checks are only for **public resources**.  
- Health check -> DNS failover
- Three kinds
	- health checks that monitor an endpoint (application, server, other AWS resources)
	- Heath checks monitor other health checks
	- Monitor CloudWatch Alarm (full control) -- throttles of DynamoDB, alarms on RDS, custom metrics, (helpful for private resources)
- Health Check are integrated with CloudWatch metrics

HC monitoring one endpoint
- Health Check are coming from all over the world (about 15 checkers)
- Setting of heal check
	- Healthy/Unhealth failure threshold - 3 fails by default
	- Interval 10 or 30 seconds
	- Supported protocol: TCP, HTTP, HTTPS
	- Over 18 % of health checkers report the endpoint is healthy -- healthy; vice versa.
	- Can choose checkers from which region s you want route 53 to use
	- string matching for the body of http response
- http response status 2xx 3xx  means pass
- 5120 bytes of response content can be used to set up pass/fail
- Configure your **router/firewall/security group** to allow incoming request from Route 53 Health Checkers ip-ranges.amazonaws.com/ip-ranges.json

calculated HC
- Combine multiple HC to a single HC
- Logic include and/or/not
- Monitor up to 256 child HC
- Specify how many child HCs need to pass to make the parent pass
- Usage: perform maintenance on website without causing all the health checks to fail

HC on private Hosted Zones
- Route 53 health checkers are outside VPC
- They cannot access private endpoint (private VPC or on-premises resource)
- Create a **CloudWatch Metric** and assign a **CloudWatch Alarm** (in VPC), then create a health check to checks the alarm itself.

***Failover(Active-Passive, HC)***
Primary and Secondary, sec don't have to associate with HC.  

***Geolocation(HC)***
- Different from latency based!
- This routing is based on user location
- Most precise location would be selected
- Should create a **Default** record in case of no match
- Use case: website localization, restrict content distribution, load balancing
- Can be associated with Health Checks

***Geoproximity(HC)***
- Based on location of users and resources
- Ability to shift more traffic to resources  based on the defined **bias**
- larger positive number -- more traffic to go to that resource; vice versa
- Resources can be AWS resources (region specified) and non-AWS resources (longitude and latitude specified)
- Must use **Route 53 Traffic Flow** to use this feature
- Usage: shift traffic from one region to another

***IP-based***
- Based on client IP address
- Provide a list of CIDRs (Classless Inter-Domain Routing) for your clients and the corresponding endpoints/locations (user-IP-to-endpoint mappings)
- Use case: optimize performance, reduce network costs
- Example: route end users from a particular ISP to a specific endpoint
- CIDR block template: ``203.0.113.0/24``

***Multi Value***
- Use when routing traffic to multiple resources
- Route 53 returns multiple values/resources
- Can be associated with HC(return only healthy records)
- Up to 8 healthy records are returned for each multi-value query
- **Not a substitute for having an ELB** (client-side) 
- Not like simple policy, who may return unhealthy records

***3rd party domain**
- You can buy domain name with any domain registrar
- Usually registrar provides you with a DNS service to mange your DNS records
- You can use another DNS service to manage DNS records
- Example: purchase domain from GoDaddy and manage DNS records using Route 53
- Look for Name Server options on registrar and change it to custom ones (like Route 53 ones)
- To do so, 
	- create a public hosted zone in Route 53
	- Update NS Records on 3rd party to use Route 53 Name Servers
- Domain Registrar is not bound to a DNS Service! Even though they always come in pairs.
# Section 11 Classic SAA Discussion TODO

## WhatsTheTime.com (stateless web app)

public ec2 t2 instance

elastic ip

More and more traffic -- m5 instance, vertical scaling, 

Down time when upgrading -- Scale horizontally -- add more m5 instances, more elastic IPs

No elastic IP -- Add Route 53 A Record and policies  

Adding and removing instances on the flight, user may not notice dns record change -- shorten TTL.

Now: 
 Using private instances, ELB + Health Checks, restricted security group rules; Route 53 points to LB by alias record

Not to configure manually -- Launching ASG, scale in and out on demand.

Now:
No down time, auto-scaling, load balanced.

AZ goes down -- multi AZ application -- ELB + Health Checks + MultiAZ, ASG span across multiple AZs. 

Cost Save -- reserve instance capacity, or even spot instances.  

Now -- LB, ASG, Multi AZ, HC, Reserved Instances type of application

## MyClothes.com (stateful web app)

 With shopping cart, hundred of users at the same time. We need to scale, maintain horizontal scalability and keep application as stateless as possible. User should not lose their shopping cart. Users should have details (addrs) in db.

Base:
Route 53 + ELB + ASG + Multi AZ. 

Redirected to different instances, users lose shopping cart -- Introduce stickness, session affinity (lose instance, lose cart)

Or using user cookies (HTTP requests heavy, security risk, <4KB)

Or using server session (send session id), Elastic Cache to store and retrieve session data, sub millisecond.   Alternative : Dynamo DB

To store details like name, address --  RDS for long-term storage.

User mostly read data, scaling reads -- RDS master for write and read replica.

Or lazy loading -- Elastic Cache as cache for RDS, need maintain cache on app side.

AZ goes down -- Multi AZ ELB, Multi AZ RDS, Multi AZ ElastiCache

Security -- EC2 security group (from LB)

## MyWordPress.com (stateful web app)

 Picture access, display and upload. User data, blog content stored in MySQL database.

Base
Route 53 + ELB Multi AZ +  ASG Multi AZ + RDS Multi AZ

Aurora scales better and easy to upwrite

To store images with EBS -- one instance case, EBS works fine. For multiple instances, EFS + ENI to have shared storage.  






 
## Instantiation Quickly

## Beanstalk


# Section 12 Amazon S3

"infinitely scaling" storage
Use case:
- Backup and storage
- Disaster recovery
- Archive
- Hybrid Cloud Storage
- Application hosting
- Media hosting
- Data lakes and big data analytics
- Software delivery
- Static website

## S3 - Buckets
- Can be viewed as top level directory. 
- Files (objects) are stored in directories (buckets).
- Globally unique name of bucket, across **all region, all accounts**.
- Buckets are defined only at the **region** level.
- Naming convention
	- No uppercase, no underscore
	- 3-63 characters long
	- Not an IP
	- Must start with lowercase letter or number
	- Must **NOT** start with the prefix ``xn--``
	- Must **NOT** end with the suffix ``-s3alias``
	- Letters, numbers and ``-``
## S3 - Objects
- Objects, files have a Key
- The key is the **FULL PATH**
- Composed of prefix and object name
	- ``s3://my-bucket/my_folder1/another_folder/my_file.txt``
	- prefix : ``my_folder1/another_folder/``
	- name: ``my_file.txt``
- There's no concept of "directories" within buckets (also UI will trick you as if there is)
- Just keys with very long names that contain slashes
- Object values are the content of the body
	- Max object size 5TB
	- If uploading more than 5 GB, must use "multi-part upload"
- Metadata (list of text key/value pairs -- system or user metadata)
- Tags (Unicode key-value pair up to 10) -- useful for security/lifecycle
- Version ID (if versioning is enabled)

## S3 - Hands On

Choose Region
Bucket Type : General Purpose rather than Directory 
Public Access setting -- block all public access (default)
Encryption and key -- default
open -- public URL but with S3 pre-signed token
directory -- work like a file system

## S3 - Security
### User-Based
- IAM policies - which API calls should be allowed for a specific user from IAM.
### Resource-Based
- Bucket Policies - bucket wide rules from the S3 console - allows cross account - most common
- Object Access Control List(ACL) - finer grain (can be disabled)
- Bucket Access Control List(ACL) - less common (can be disabled)

An IAM principal can access S3 object if:
- The user IAM permission ALLOW it OR the resource policy ALLOWS it
- AND there's no explicit DENY

### Encryption
Encrypt objects in Amazon S3 using encryption keys.

### S3 bucket policies
- JSON based policies
	-  { Version, Statement: \[ {Sid, Effect, Principal, Action, Resource}  \] }
	- Resources: Buckets and Objects
	- Effect: Allow / Deny
	- Actions: Set of API to Allow or Deny
	- Principal: The account or user to apply the policy to
- Use S3 bucket policy to:
	- Grant public access to the bucket
	- Force objects to be encrypted at upload
	- Grant access to another account (Cross Account)
- For public access - bucket policies
- For IAM user - IAM permissions
- For EC2 instance - IAM roles
- For IAM user of another AWS account - bucket policy, allow cross account
- Bucket settings for Block Public Access
	- These settings were created to prevent company data leaks
	- Unless these setting are disabled, the objects won't be public
	- If you know your bucket should never be public, leave these on
	- Can be set at account level
 
## S3 - Security Hands On
- Disable Block Public Access (be careful!)
- Permission - Bucket Policy - Edit - Policy example / Policy generator 
- Effect: Allow
- Principal: ``*`` means all public access
- Action: ``s3:GetObject`` means read
- ARN: ``<bucket-arn>/*``

## S3 - Static Website Hosting & Hands On
- S3 can host static websites and have them accessible on the Internet
- The URL will be in one of the formats (dash or dot) (depending on the region)
	- ``http://bucket-name.s3-website-aws-region.amazonaws.com``
	- ``http://bucket-name.s3-website.aws-region.amazonaws.com``
- If 403 Forbidden error, make sure the bucket policies allows public reads!

Hands On
- Properties - Static Website Hosting - Enable - Index document - ``index.html``
- Then find the URL in properties - static website hosting - bucket website endpoint
## S3 - Versioning & Hands On
Why versioning? Be able to update the website/data in a safe way.
- You can version your files at Amazon S3
- It is enabled at the **bucket level**
- Same key overwrite will change the version
- It is best practice to version your buckets
	- Protect against unintended delete (ability to restore a version), add a delete marker
	- Easy to roll back to previous version
- Notes 
	- Resources/Files that are not versioned prior to enabling versioning will have **null** version
	- Suspending versioning does not delete previous versions

Hands On
- Properties - Bucket Versioning - Edit - Enable
- Upload a file with the same key
- Objects - ``show versions`` button on - delete the object with a specific version ID - that would lead to **permanently delete**
- However, if we let ``show versions`` button off in objects view, deleting an object would only lead to adding a delete marker at the top of that object (object disappears when ``show versions`` button is off, object attached with delete marker when ``show versions`` button is on), and delete (permanently) the delete marker would restore the object

## S3 - Replication & Hands On
Two types : Same Region Replication and Cross Region Replication
- Must **enable versioning** in source and destination buckets
- Buckets can be in different AWS accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
-  Use cases
	- CRR - compliance, lower latency access, replication across accounts
	- SRR - log aggregation, live replication between production and test accounts
- After enable replication, only **NEW** objects are replicated
- Optionally, you can replicate existing objects using **S3 Batch Replication**
	- Replicate existing objects and objects that failed replication
- For **DELETE** operations
	- Delete markers can be replicated from source to target (optional setting)
	- Deletions with version ID are not replicated (to avoid malicious deletes)
- There is **no chaining** of replication 1->2, 2->3 does not mean objects in 1 would be replicated into 3. Thus it's better 1->2,1->3.

Hands On
- Remember to enable versioning
- Region selection is done by choosing from the top right of the console
- Management - Replication rules - create replication rule - rule scope : apply to all objects - destination: target bucket - IAM role : create a new role
- **Batch (replication) operations is separate from replication** feature itself
- version id would also be replicated
## S3 - Storage Classes & Hands On
### S3 Durability and Availability
Durability
- High Durability (11 9's) of objects across multiple AZ, 
- store 10,000,000 objects, on avg expect to incur a loss of a single object once every 10,000 years
- same for all storage classes

Availability
- measure how readily available a service is
- Varies depending on storage classes
- Example: S3 standard has 99.99% availability = not available 53 minutes a year

### Storage Classes
Three major types:
Standard
Infrequent Access
- For data less frequently accessed, but requires rapid access when needed
- Lowe cost than S3 Standard, cost on retrieval
Glacier
- Low-cost object storage meant for archiving/backup
- Pricing : storage + retrieval cost

- Standard - General Purpose
	- 99.99% availability
	- Used for frequently access data
	- Low latency and high throughput
	- Sustain 2 concurrent facility failures
	- Big Data analytics, mobile & gaming application, content distribution
- Standard-Infrequent Access (IA)
	- 99.9% availability
	- Disaster Recovery, backups
- One Zone-Infrequent Access
	- High durability in a single AZ, data lost when AZ is destroyed
	- 99.5% availability
	- Secondary backup copies of on-premise data, or data can be recreated.
- Glacier Instant Retrieval 
	- Millisecond retrieval, great for data accessed once a quarter
	- Minimum storage duration of 90 days
- Glacier Flexible Retrieval (formerly Amazon S3 Glacier)
	- Expedited (1 - 5 mins), Standard (3 - 5 hours), Bulk ( 5 - 12 hours, free)
	- Minimum storage duration of 90 days
- Glacier Deep Archive
	- Standard (12 hours), Bulk (48 hours)
	- Minimum storage duration of 180 days
- Intelligent Tiering
	- Small monthly monitoring and auto-tiering fee
	- Moves objects automatically between Access Tiers based on usage
	- There are no retrieval charges in S3 Intelligent Tiering
	- Frequent Access tier(auto): default tier
	- Infrequent Access tier(auto): 30 days not accessed
	- Archive Instant Access tier(auto): 90 days not accessed
	- Archive Access tier(optional): 90 - 700+ days not accessed
	- Deep Archive Access tier(optional): 180 - 700+ days not accessed

- Can move between classes manually or using S3 life cycle configurations

Hands On
- During uploading, Properties - Storage class to choose storage class for object
- Can also be changed after uploaded
- Bucket - Management - Lifecycle Rules, automatically change storage classes for objects

# Section 13 Advanced Amazon S3

## S3 Lifecycle Rules and S3 Analytics

- You can transition objects between storage classes
- Diagram: Standard -- Standard IA -- Intelligent Tier -- One-Zone IA -- Glacier Instant Retrieval -- Glacier Flexible Retrieval -- Glacier Deep Archive, (lifecycle rules support only from upper level to lower level, waterfall models)
- For infrequently accessed objects, move to Standard IA
- For archive objects that you don't need fast access to, move them to Glacier or Glacier Deep Archive
- Moving objects can be automated using a **Lifecycle Rules**
### Lifecycle Rules
- Transition Actions - configure objects to transition to another storage class
- Expiration Actions - configure objects to expire (delete) after some time
	- Access log files can be set to delete after a 365 days
	- Can be used to delete **old version** of files if versioning is enabled
	- Can be used to delete incomplete Multi-Part Upload
- Rules can be created for a certain prefix (i.e. ``s3://mybucket/mp3/*``)
- Rules can be created for certain object tags (i.e. Department: Finance)

### S3 Analytics
- Help you decide when to transition objects to the right storage class
- Recommendation for Standard and Standard IA
	- Does not work for One-zone IA or Glacier
- Report is updated daily
- 24 - 48 hours to start seeing data analysis
- Good first step to put together Lifecycle Rules or improve them

### Hands On
- Bucket - Management - Lifecycle Rules - create lifecycle rule
- Five types of actions
	- move current versions between storage classes
	- move noncurrent between storage classes
	- expire current versions of objects
	- permanently delete noncurrent versions of objects
	- delete expired object delete markers or incomplete multipart uploads

## Requester Pays
- In general, bucket owner pay for all S3 storage and data transfer costs associated with their bucket
- With **Requester Pays** bucket, the requester instead of the bucket owner pays the cost of the request and the data download from the bucket. Owner still pays for storage.
- Helpful when you want to share large datasets with other accounts
- The requester must be authenticated in AWS (cannot be anonymous)

## S3 Event Notifications & Hands On

- ``S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication...``
- Object name filtering possible (``*.jpg``)
- Use case: generate thumbnails of images uploaded to S3
- Can create as many "S3 events" as desired, and sent to SNS, SQS, Lambda ...
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer

- For event notification target to work, we need to configure IAM permission, like SNS/SQS/Lambda Resource (Access) Policy. We don't use IAM roles.
- Also we can send all event notification to **Amazon EventBridge**, which can then send to over 18 AWS services as destination based on rules.
With EventBridge, we have 
- **Advanced filtering** options with JSON rules (metadata, object size, name...)
- **Multiple Destination** -- ex Step Functions, Kinesis Streams / Firehose
- **EventBridge Capabilities** -- archive, replay events, reliable delivery

Hands On
- Bucket - Properties - Event notifications - Create event notification 
- Bucket - Properties - Amazon EventBridge
- Set prefix, suffix
- Set event types (object creation, removal, restore ..)
- Set Destination
- Create SQS queue, modify access policy to allow S3 write to SQS
- There will be a TestEvent in SQS after event notification applied.

## S3 Performance
### Baseline Performance
- S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per second per prefix in a bucket. (prefix is anything between bucket name and file name)
- No limit of prefixes in a bucket
- If you spread reads across all n prefixes evenly, you can achieve 5500n requests per seconds for GET and HEAD

### S3 Performance Optimization
- Multi-Part upload
	- Recommended for files > 100 MB
	- Must use for files > 5GB
	- Can help parallelize uploads (speed up transfers)
- S3 Transfer Acceleration
	- Increase transfer speed by first transferring file to a nearby AWS edge location which will then forward the data to the S3 bucket in the target region
	- Compatible with multi-part upload
	- User to edge, fast public internet; edge to target region S3, fast AWS private network.
	- Minimize the amount of public internet that we go through and maximize the amount of private AWS network that we go through.

### Read Files - S3 Byte-Range Fetches
- Parallelize GETs by requesting specific byte ranges of a file
- Better resilience in case of failures
- Use case:
	- Speed up downloads
	- Retrieve only partial data (i.e. header of a file)


## S3 Batch Operations
- Perform bulk operation on existing S3 objects with a single request
	- Modify object metadata and properties
	- Copy objects between S3 buckets
	- **Encrypt un-encrypted object**
	- Modify ACLs, tags
	- Restore objects from S3 Glacier
	- Invoke lambda function to perform custom action on each object
- A job consists of a list of objects, the action to perform and optional parameters
- S3 batch operations manages retries, tracks progress, sends completion notifications, generate reports ... 
- Use S3 inventory to get object list and use S3 Select to filter your objects

## S3 Storage Lens
- Understand, analyze, and optimize storage across entire AWS Organization
- Aggregate data for Organization, specific accounts, regions, buckets, or prefixes.
- Discover anomalies, identify cost efficiencies, and apply data protection best practices across entire AWS Organization (30 days usage and activity metrics) 
- Default or create your own dashboards
- Can be configured to export metrics daily to an S3 bucket (CSV, Parquet)
- Analyze and provide summary insights, data protection, cost efficiencies

### Default Dashboard
- Visualize summarized insights and trends for both free and advanced metrics
- Default dashboard shows Multi-Region and Multi-Account data
- Preconfigured by Amazon S3
- Cannot be deleted, can be disabled

### Metrics
- Summary Metrics
	- General Insights about S3 storage
	- Storage Bytes, Object Count ...
	- Use cases: identify the fastest growing (or not used) bucket and prefixes
- Cost-Optimization Metrics
	- Provide insights to manage and optimize your storage costs
	- Noncurrent Version Storage Bytes, Incomplete Multipart Upload Storage Bytes...
	- Use cases: identify buckets with incomplete multipart uploaded older than 7 days, identify which objects could be transitioned to lower cost storage class
- Data-Protection Metrics
	- Insights for data protection features
	- Versioning Enabled Bucket Count, MFA Delete Enabled Bucket Count, SSEKMS Enabled Bucket Count, Cross Region Replication Rule Count ...
	- Use cases: identify buckets that are not following data-protection best practices
- Access-Management Metrics
	- Insights for S3 Bucket Ownership
	- Object Ownership Bucket Owner Enforced Bucket Count ...
	- Use cases: identify which Object Ownership settings your buckets use
- Event Metrics
	- Insights for S3 Event Notification
	- Event Notification Enabled Bucket Count, 
	- identify which buckets have S3 Event Notifications configured
- Performance Metrics
	- Insights for S3 Transfer Acceleration
	- Transfer Acceleration Enabled Bucket Count
	- identify which buckets have S3 Transfer Acceleration enabled
- Activity Metrics
	- Insight about how your storage is requested
	- All Requests, Get/Put/List Requests, Bytes Downloaded ...
- Detailed Status Code Metrics
	- Insight for HTTP status codes
	- 200 Count, 403 Count, 404 Count...

Free vs Paid metrics
- Free Metrics
	- Automatically available for all customers
	- Contains around 28 usage metrics
	- Data is available for queries for 14 days
- Advanced Metrics and Recommendations
	- Additional paid metrics and features
	- Advanced Metrics -- Activity, Advanced Cost Optimization, Advanced Data Protection, Status Code
	- CloudWatch publishing -- Access metrics in CloudWatch without additional charges
	- Prefix Aggregation -- Collect metrics at the prefix level
	- Data is available for queries for 15 months

# Section 14 Amazon S3 Security

## S3 Encryption
### Object Encryption
- You can encrypt objects in S3 buckets using one of 4 methods
- Server-Side Encryption (SSE)
	- SSE with Amazon S3-Managed Keys (SSE-S3) enabled by default
	- SSE with KMS Keys stored in AWS KMS (SSE-KMS)
	- SSE with Customer-Provided Keys (SSE-C)
	- DSSE-KMS not covered in exam
- Client-Side Encryption
- It's important to understand which ones are for which situation in the exam

### SSE-S3
- Encryption using keys handled, managed and owned by AWS that you never have access to
- Object is encrypted server side
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption": "AES256"
- Enabled by default for new buckets and new objects
- Upload the file with correct HTTP(S) header.

### SSE-KMS
- Encryption using keys handled and managed by AWS KMS (Key Management Service)
- KMS advantages: user control audit key usage using CloudTrail
- Object is encrypted server side
- Must set header "x-amz-server-side-encryption": "aws:kms"
- Upload the file with correct HTTP(S) header.
- Limitation: may be impacted by the KMS limits
	- When you upload, call GenerateDataKey API
	- When download, call DecryptKMS API
	- Count towards the KMS quota per second (5500, 10000, 30000 rps based on region)
	- You can request a quota increase using Service Quota Console

### SSE-C
- Server-Side encryption using keys fully managed by the customer outside of AWS
- Amazon S3 does **NOT** store the encryption key you provice
- **HTTPS** must be used
- Encryption key must provided in HTTP headers, for every HTTP request made

### Client-Side Encryption
- Use client libraries such as **Amazon S3 Client-Side Encryption Library**
- Client must encrypt data themselves before sending to S3
- Client must decrypt data themselves when retrieving from S3
- Customer fully manages the keys and encryption cycle

### Encryption in Transit (SSL / TLS)
- Encryption in flight is also called SSL/TLS
- S3 Exposes two endpoints: HTTP(not encrypted) and HTTPS(encryption in flight)
- HTTPS is recommended for S3
- HTTPS is mandatory for SSE-C
- Most clients would use the HTTPS endpoint by default

### Force Encryption in Transit
- Use a bucket policy
- Use ``condition`` and ``aws:SecureTranport`` fields to configure
```JSON
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Deny",
			"Principal": "*",
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::mybucket/*",
			"Condition": {
				"Bool": {
					"aws:SecureTransport": "false"
				}
			}
		}
	]
}
```
Any HTTP request would be blocked and HTTPS may be allowed.

## Encryption Hands On
- Editing the server side encryption options would create a new version of existing object
- Choose AWS KMS key, there would be a default S3 key
- SSE-KMS, bucket key option available, to reduce API calls to KMS service
- SSE-C can only be chosen on CLI, not from the console
- You don't have to tell AWS if you're doing client side encryption

### Default Encryption
- SSE-S3 encryption is automatically applied to new objects stored in S3 bucket
- Optionally, you can "force encryption" using a bucket policy and refuse any API call to PUT an S3 object without your preferred encryption headers (SSE-KMS or SSE-C)
```JSON
{
	"Condition": {
		"StringNotEquals":{
			"s3:x-amz-server-side-encryption": "aws:kms"
		}
	}

}
```

```JSON
{
	"Condition": {
		"Null":{
			"s3:x-amz-server-side-encryption-customer-algorithm": "true"
		}
	}
}

```

Note: Bucket Policies are evaluated before "Default Encryption". Headers encryption is evaluated before applying default encryption.  

## CORS
- Cross Origin Resource Sharing
- Same Origin Policy to prevent CSRF and Data theft, but too strict. CORS relax it.
- Origin = scheme (protocol) + host (domain) + port
- CORS is a web-browser based mechanism to allow requests to other origins while visiting the main origin
- Same origin means same scheme, same host and same port
- For CORS, the requests won't be fulfilled unless the other origin allows for the requests, using CORS Headers (i.e. Access-Control-Allow-Origin)

**Workflow**
After receiving a response from Origin, the webpage requires some resources lying in other Cross-Origin. The browser would first send an OPTIONS request as **Preflight Request**
```
OPTIONS /
HOST: www.other.com
ORIGIN: https://www.example.com
```
Then based on logics on Cross-Origin, if that Origin is allowed, then a **Preflight Response** would be sent back containing related headers
```
Access-Control-Allow-Origin: https://www.example.com
Access-Control-Allow-Method: GET, PUT, DELETE
```
Then web browser check the response, knowing it's OK to CORS, and would send HTTP request with proper headers
```
GET /
Host: www.other.com
Origin: https://www.example.com
```

**ORS in S3**
- If a client makes a cross-origin request on S3 bucket, we need to enable the correct CORS headers
- To deal with it, you can allow for a specific origin or for \*, all origins
- CORS is a web-browser security enforced by the browser
## CORS Hands On
- In the same bucket, works well
- Create another bucket, configure bucket policy.
- In html of Origin, fetch the full path of resources in another bucket, this is going to trigger CORS request
- At first trial, web dev tools would show CORS request blocked error.
- In other bucket, Permission -- CORS setting --  A JSON file with "AllowedHeaders" "AllowedMethods", "AllowedOrigins"(without slash at the end), "ExposeHeaders", "MaxAgeSeconds" fields.

## MFA Delete & Hands On
- MFA, Multi-Factor Authentication - force users to generate a code on a device before doing important operations on S3
- MFA will be required to
	- Permanently delete an object version
	- Suspend Versioning on the bucket
- NOT the case
	- Enable Versioning
	- List deleted versions
- To use MFA delete **Versioning must be enabled** on the bucket.
- **Only the bucket owner (root account)** can enable/disable MFA delete.
- It's an extra protection against permanent deletion of object version

Hands On
- Remember to enable versioning
- Properties -- Bucket Versioning -- Edit, MFA Delete disabled 
- Not able to enable at AWS console, have to use CLI with root credentials (Access Key)
- After set up, deletion have to be done using CLI e.t.c. as well, not able to do it at AWS console. 

## Access Logs & Hands On
- For audit purpose, you may want to log all access to S3 buckets
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- That data can be analyzed using data analysis tools
- The target logging bucket must be in the same AWS region
- There's a specific log format
- Warning: 
	- **DO NOT** set your logging bucket to be the monitored bucket
	- It will create a logging loop and your bucket will grow exponentially

Hands On
- Properties -- Server Access Logging -- Edit -- Enable
- Set destination, prefix, format,
- Activities are logged into logging bucket, takes a little bit of time
- Permission -- bucket policy would be automatically updated

## Pre-signed URLs & Hands On
- Generate pre-signed URLs using the **S3 Console, AWS CLI or SDK**
- URL Expiration
	- S3 Console - 1 min up to 720 mins (12 hours)
	- AWS CLI - configure expiration with ``-expires-in`` parameter in seconds, default 3600 secs, max 604800 secs = 168 hours
- Users given a pre-signed URL inherit the permission of the user that generated the URL for GET/PUT
- Use case: grant temporary access to some specific files for download or upload
	- Allow only logged-in users to download a premium video from your S3 bucket
	- Allow an ever-changing list of users to download files by generating URLs dynamically
	- Allow temporarily  a user to upload a file to a precise location in your S3 bucket, while keeping bucket private

Hands On
- Chose a non-public bucket
- Open button provides a pre-signed URL itself, containing user signature.
- Console -- Object Action (top right) -- Share with a pre-signed URL
- With that URL within valid period, anyone can access it even if the bucket or object is private.

## Glacier Vault Lock & S3 Object Lock
### Glacier Vault Lock
- Adopt a WORM(Write Once Read Many) model
- Create a Vault Lock Policy
- Lock the policy for future edits(can no longer be changed or deleted)
- Helpful for compliance and data retention

### S3 Object Lock (versioning must be enabled)
- Adopt a WORM(Write Once Read Many) model
- Block an object version from deletion for a specified amount of time
- **Retention mode - Compliance**
	- Object versions cannot be overwritten or deleted by any user, including the root user
	- Objects retention modes cannot be changed, and retention periods cannot be shortened
- **Retention mode - Governance**
	- Most user cannot overwrite or delete an object version or alter its lock settings
	- Some users have special permissions to change the retention or delete the object
- **Retention Period**: protect the object for a fixed period, it can be extended.  
- **Legal Hold**
	- Protect the object indefinitely, independent from retention period
	- Can be freely placed and removed using the ``s3:PutObjectLegalHold`` IAM permission

## S3 Access Points
### Access Points
Push the S3 bucket policy to access point policy for easier access manager.
An access point policies include object list (or prefixes), methods (R/W).
With proper IAM permission, different user group can have different access patterns to the bucket.

- Policies attached to each access point, simple bucket policy, easy to scale access to S3 bucket
- Access Points simplify security management for S3 buckets
- Each Access Point has
	- Its own DNS name (Internet Origin or VPC Origin)
	- An access point policy (like bucket policy) - manage security at scale

### Access Points - VPC Origin
- We can define the access point to be accessible only from within the VPC
- To do so, we must create a VPC endpoint to access the Access Point (Gateway or Internet Endpoint) 
- VPC endpoint policy must allow access to the target bucket and Access Point
- Three level of security
## S3 Object Lambda
- Use AWS Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Point
- Use case:
	- Redacting personally identifiable information for analytics or non-production environments
	- Converting across data formats, such as XML to JSON
	- Resizing and watermarking images on the fly using caller-specific details, such as the user who requested the object



# Section 15 CloudFront & AWS Global Accelerator

## CloudFront Overview
### What is CloudFront?
- Content Delivery Network(CDN)
- Improves read performance, content is cached at the edge
- Improves users experience
- 216 Point of Presence globally (edge locations)
- DDoS protection (because worldwide), integration with Shield, AWS Web Application Firewall

### CloudFront - Origins
- S3 bucket
	- For distributing files and caching them at the edge
	- Enhanced security with CloudFront Origin Access Control (OAC)
	- OAC is replacing Origin Access Identity (OAI)
	- CloudFront can be used as an ingress (to upload files to S3)
- Custom Origin (HTTP)
	- Application Load Balancer
	- EC2 instance
	- S3 websites (must first enable the bucket as a static S3 website)
	- Any HTTP backend you want
- Workflow -- Edge locations forward request to Origin and cache results
- S3 is protected by both bucket policy and OAC

### CloudFront vs S3 Cross Region Replication
- CloudFront:
	- Global Edge network
	- Files are cached for a TTL (maybe a day)
	- Great for **static content that must be available everywhere**
- S3 CRR
	- Must be setup for each region you want replication to happen
	- Files are updated in near real-time
	- Read only
	- Great for **dynamic content that needs to be available at low-latency in few regions**.

## CloudFront Hands On
- create a bucket, default setting
- Even with pre-signed URL, the webpage cannot be rendered correctly -- the image URL does not contains user token therefore access denied
- CloudFront Console (Global Service) - Create a CloudFront Distribution - choose origin domain (S3) - Origin access set to OAC (Public only works when bucket allows public access) - Create Origin Access Control and choose it -  Update S3 bucket policies (prompt, or Distributions - Origins - Edit - copy policy) - disable WAF - default root object set to index.html
- Wait until distribution deployed, copy domain name, can render webpage correctly
- Second time would fetch from cache instead of bucket, faster.
- CloudFront - left bar - Security - Origin access - OAC assigned to some distributions

## ALB or EC2 as an Origin
- If EC2 is directly connected to edge location, then it **must** be public and allows public IP of edge locations (by security group)
- If there's an ALB between EC2 and edge locations, then EC2 can be private (still allow security group of load balancer), but ALB **must** be public and allow public IP of Edge locations (by security group)

## CloudFront Geo Restriction
- Restrict who can access your distribution
	- Allow list: as its name
	- Block list: as its name
- The "country" is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content
- Hands On: Distribution - choose the distribution - Security - CloudFront Geographic Restriction

## CloudFront Price Classes
- CF Edge locations are all around the world
- The cost of data out per edge location varies
- The more out, the cheaper per unit
- You can reduce the number of edge locations for **cost reduction**
- Three price classes:
	- Price Class All: all regions, best performance
	- Price Class 200: most regions, but excludes the most expensive regions (Africa, ME, Asia)
	- Price Class 100: least expensive regions (NA, EU)

## CloudFront Cache Invalidation
- In case you update the back-end origin, CF don't know about it and will only get the refreshed content after the TTL has expired
- However, you can enforce an entire or partial cache refresh (thus bypassing the TTL) by performing a **CloudFront Invalidation**, then updates would be reflected soon.
- You can invalidate all files or a special path/prefix

## Global Accelerator
- Why? Consider ALB in one region, users worldwide want to access it -- many hops through routers over public network -- latency and packet loss -- wish to go though AWS network to minimize latency
- Unicast and Anycast IP -- multiple machines share the same IP, client is routed to the **nearest** one. Global Accelerator uses Anycast IP.
- GA leverages the AWS internal network to route to your application
- **2 Anycast IP**(public) are created for your application
- By the anycast IP we send traffic directly to edge location
- The edge location send traffic to application

- Works with Elastic IP, EC2 instances, ALB, NLB, public or private
- Consistent Performance
	- Intelligent routing to lowest latency and fast regional failover
	- No issue with client cache (because IP doesn't change)
	- Internal AWS network
- Health Checks
	- Global Accelerator performs a health check of your applications
	- Helps make your application global (failover less than 1 minute for unhealthy)
	- Great for disaster recovery
- Security
	- Only 2 external IP need to be whitelisted
	- DDoS protection thanks to AWS Shield

AWS Global Accelerator vs CloudFront
- They both use the same global network and its edge locations around the world
- Both services integrate with AWS Shield for DDoS protection
- CloudFront
	- Improves performance for both cacheable content (such as images and videos)
	- Dynamic content (such as API acceleration and dynamic site delivery)
	- Content is served at the edge
- Global Accelerator
	- Improves performance for a wide range of applications over TCP or UDP
	- Proxying packets at the edge to applications running in one or more AWS Regions
	- Good fit for non-HTTP use cases, such as gaming(UDP), IoT(MQTT or Voice over IP
	- Good for HTTP use cases that require static IP address.
	- Good for HTTP use cases that requires deterministic, fast regional failover.
	
## Global Accelerator Hands On
- Automatically goes into nearest region
- Create EC2 instances at different regions
- name, listeners (port and protocol and affinity)
- endpoint groups and health checks (for EC2 and Elastic IP endpoints)
- Add endpoints, assign weights


# Section 16 AWS Storage Extras

## Snow Family &  Hands On
- Highly secure, portable devices to collect and process data at the **edge** and **migrate data into and out of** AWS. 
- Offline devices to perform data migrations: if it takes more than a week to transfer over the network, use Snowball devices!

| Properties       | Snowcone             | Snowball Edge   |
| ---------------- | -------------------- | --------------- |
| Storage Capacity | 8 TB HDD - 14 TB SSD | 80 TB - 210 TB  |
| Migration Size   | Up to terabytes      | Up to petabytes |
Why Snow Family?
- Limited connectivity
- Limited bandwidth
- High network cost
- Shared bandwidth (cannot maximize the line)
- Connection stability

Why edge computing?
- Process data while it's being created on an **edge location**
	- A truck on the road, a ship on the sea, a mining station underground...
- These locations may have limited internet and no access to computing power
- We setup a Snowball Edge / Snowcone device to do edge computing
	- Run EC2 instances or Lambda functions at the edge
- Use cases: preprocess data, machine learning, transcoding media

How does it work?
Ship the devices between user and Amazon.
- Request device from AWS console for delivery
- Install Snowball client / AWS OpsHub on your servers
- Connect the snowball to your servers and copy files using the client
- Ship back the device
- Data will be loaded into an S3 bucket
- Snowball is completely wiped.

## FSx & Hands On
- Launch 3rd party high-performance file systems on AWS
- Fully managed service
- Including Lustre, Windows (File Server), NetApp ONTAP, OpenZFS

### FSx for Windows (File Server)
- FSx for Windows is a fully managed **Windows** file system share drive
- Supports Server Message Block SMB protocol & Windows NTFS
- Microsoft Active Directory integration, ACLs, user quotas
- **Can be mounted on Linux EC2 instances**
- Supports **Microsoft's Distributed File System (DFS) Namespaces** (group files across multiple FS)

- Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
- Storage Options:
	- SSD - latency sensitive workloads  (DBs, media processing, data analytics, ...)
	- HDD - broad  spectrum of workloads (home directory, CMS ...)
- Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
- Can be configured to be **Multi-AZ (high availability)**
- Data is backed-up daily to S3

### FSx for Lustre
- Lustre is a type of parallel distributed file system, for large-scal computing
- The name is derived from "Linux" and "cluster"

- For **machine learning, HPC**
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- Storage Options:
	- SSD - low-latency, IOPS intensive workloads, small and random file operations
	- HDD - throughput workloads, large and sequential file operations
	- Scratch and Persistent, see below
- Seamless integration with S3
	- Can "read" S3 as a file system (through FSx)
	- Can write the output of the computations back to S3 (through FSx)
- Can be used from on-premises serves (VPC or Direct Connect)

### FSx File System Deployment Options
- Scratch File System
	- Temporary storage
	- Data is not replicated ( does not persist if file server fails)
	- High burst (6x faster, 200 MBps per TiB)
	- Usage: short-term processing, optimize costs
	- optional data repository at S3
- Persistent File System
	- Long-term storage
	- Data is replicated within same AZ
	- Replace failed files within minutes
	- Usage: long-term processing, sensitive data
	- optional data repository at S3

### FSx for NetApp ONTAP
- Managed NetApp ONTAP on AWS
- File system compatible with **NFS, SMB, iSCSI** protocol
- Move workloads running on ONTAP or NAS to AWS
- Works with
	- Linux
	- Windows
	- MacOS
	- VMware Cloud on AWS
	- Amazon Workspace & AppStream 2.0
	- Amazon EC2, ECS, and EKS
- Storage shrinks or grows automatically
- Snapshots, replications, low-cost, compression, and data de-duplication
- **Point-in-time instantaneous cloning (helpful for testing new workloads)**

### FSx for OpenZFS
- Managed OpenZFS file system on AWS
- File System compatible with NFS(v3, v4, v4.1, v4.2)
- Move workloads running on ZFS to AWS
- Works with:
	- Linus
	- Windows
	- MacOS
	- VMware Cloud on AWS
	- Amazon Workspace & AppStream 2.0
	- Amazon EC2, ECS, and EKS
- Up to 1 million IOPS with less than 0.5 ms latency
- Snapshots, compression and low-cost (no de-duplication)
- **Point-in-time instantaneous cloning (helpful for testing new workloads)**

## Storage Gateway & Hands On
- AWS is pushing for "hybrid cloud", part of your infrastructure is on the cloud, part on-premises
- This can be due to
	- Long cloud migration
	- Security requirements
	- Compliance requirements
	- IT strategy
- S3 is a proprietary storage technology (unlike EFS, NFS) so how do you expose the S3 data on-premises?
- AWS Storage Gateway!

- A bridge between on-premises data and cloud data
- Use cases:
	- Disaster recovery
	- Backup and restore
	- Tiered storage (cold and warm)
	- On-premises cache and low-latency file access
- Types of Storage Gateway
	- S3 File Gateway
	- FSx File Gateway
	- Volume Gateway
	- Tape Gateway
- In the above cases, the gateway has to be run on your corporate data center. 

### S3 File Gateway
- Configured S3 buckets are accessible using the NFS and SMB protocol
- Only works for non-Glacier data in the bucket
- **Most recently used data is cached in the file gateway**
- Support S3 Standard, S3 Standard IA, S3 One Zone IA, S3 Intelligent Tiering
- Transition to S3 Glacier using a Lifecycle policy
- Bucket access using IAM roles for each File Gateway
- SMB Protocol has integration with Active Directory(AD) for user authentication

### FSx File Gateway
- Native access to Amazon FSx for Windows File Server
- You already can access FSx, the Gateway provides **local cache for frequently accessed data**
- Windows native compatibility (SMB, NTFS, AD)
- Useful for group file shares and home directories

### Volume Gateway
- Block storage using iSCSI protocol backed by S3
- Backed by **EBS snapshots** which can help restore on-premises volumes!
- Two options!
- **Cached volumes**: low latency access to most recent data
- **Stored volumes**: entire dataset is on premises, scheduled backups to S3

### Tape Gateway
- Some companies have backup processes using physical tapes
- With Tape Gateway, companies use the same process but, in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-based process (and iSCSI interface)
- Works with leading backup software vendors

### Hardware appliance
- Using storage gateway means you need on-premises virtualization
- Otherwise, you can use a **Storage Gateway Hardware Appliance**
- You can buy it on amazon.com

- Works with File / Volume / Tape Gateway
- Has the required CPU, memory, network, SSD cache resources
- Helpful for daily NFS backups in small data centers (virtualization unavailable)

## AWS Transfer Family
- A fully managed service for file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
- Supported Protocols
	- AWS Transfer for FTP
	- AWS Transfer for FTPS
	- AWS Transfer for SFTP
- Managed infrastructure, Scalable, Reliable, Highly Available (multi AZ)
- Pay per provisioned endpoint per hour + data transfers in GB
- Store and manage users' credentials within the service
- Integrate with existing **authentication systems** (Microsoft AD, LDAP, Okta, Amazon Cognito, custom)
- Usage: sharing files, public dataset, CRM, ERP
- Diagram: Users -> (route 53, integrated with Auth) -> transfer family -- IAM role --> S3 and EFS

## DataSync
- Move large amount of data to and from
	- On-premises / other cloud to AWS(NFS, SMB, HDFS, S3 API...) - needs **agents**
	- AWS to AWS (different storage services) - no agent needed
- Can synchronize to:
	- Amazon S3 (any storage classes - including Glacier)
	- Amazon EFS
	- Amazon FSx (all kinds)
- Replication tasks can be **scheduled hourly, daily, weekly**
- **File permissions and metadata are preserved** (NFS POSIX, SMB)
- Unique option that will preserve the metadata of files when moving data
- One agent task can use 10 Gbps, can setup a bandwidth limit
- Snowcone devices come with DataSync agent pre-installed
## All AWS Storage Options Compared
- S3: Object Storage
- S3 Glacier: Object Archival
- EBS Volumes: Network storage for one EC2 instance at a time (multi attach io1 io2)
- Instance Storage: Physical storage for your EC2 instance (high IOPS)
- EFS: Multi-AZ Network File System for Linux Instances, POSIX filesystem
- FSx for Windows: NFS for Windows servers
- FSx for Lustre: HPC Linux file system
- FSx for NetApp ONTAP: High OS Compatibility
- FSx for OpenZFS: Managed ZFS file system
- Storage Gateway: S3 and FSx File Gateway, Volume Gateway (cached and stored), Tape Gateway
- Transfer Family: FTP, FTPS, SFTP interface on the top of S3 or EFS
- DataSync: Schedule data sync from on-premises to AWS or AWS to AWS
- Snow Family: move large amount of data to the cloud, physically
- Databases: for specific workloads, usually with indexing and querying

# Section 17 Decoupling Applications: SQS,SNS, Kinesis, Active MQ

- Multiple applications will inevitably need to communicate with one another
- Synchronous and Asynchronous (Event based) communications pattern
- Synchronous between apps can be problematic if there are sudden spikes of traffic
- In that case, it's better to **decouple** your applications, and have the decoupling layer scale for you
	- Using SQS queue model
	- Using SNS pub/sub model
	- Using Kinesis: real-time streaming model
- These services can scale independently from our application!

## SQS

Producer(s) -- Send message --> SQS Queue -- Poll message --> Consumer(s)

What is a queue? A buffer to decouple between producers and consumers

## SQS - Standard Queue
- Oldest offering (over 10 years)
- Fully managed service, used to **decouple applications**
- Attributes
	- Unlimited throughput, unlimited number of message in queue
	- Short-lived messages: Default retention of messages: 4 days, maximum of 14 days
	- Low latency (< 10 ms on publish and receive)
	- Limitation of 256 KB per message sent
- **Can have duplicate messages** (at least one delivery, occasionally)
- **Can have out of order messages** (best effort ordering)

### SQS - Producing Messages
- Produced to SQS using SDK, SendMessage API
- The message is **persisted** in SQS until a consumer (reads and) deletes it
- Message retention: default 4 days, up to 14 days
- Example: send an order to be processed
	- Order id
	- Customer id
	- Any attributes you want
- SQS standard: unlimited throughput

### SQS - Consuming Messages
- Consumers (running on EC2 instances, on-premises servers, AWS Lambda ...)
- Poll SQS for messages (receive up to 10 messages at a time)
- Process the messages (example: insert the message into an RDS database)
- Delete the message using the DeleteMessage API, guarantee that no other consumer can see the message, message processing is complete

### SQS -  Multiple EC2 Instances Consumers
 - Consumers receive and process messages in parallel
 - Some message may take long time to process, therefore it's likely to be delivered to another consumer. That's why
	 - At least once delivery
	 - Best-effort message ordering
 - Consumers delete messages after processing them
 - We can scale consumers horizontally to improve throughput of processing

### SQS with ASG
SQS Queue -- Poll for messages --> EC2 instances (ASG)
  \\ -- > CloudWatch Metric: Queue length -- Alarm for breach --> CloudWatch Alarm --> Scale ASG

Higher throughput

### SQS to **decouple** between application tiers

requests --> Front-end web app (ASG) --SendMessage--> SQS Queue --ReceiveMessages-> Backend processing application (ASG) --insert--> S3 bucket

Scale front-end and back-end independently
Also front and back end can choose different instance type according to requirements (video processing may use better GPU, for example)

SQS- Security
- **Encryption**:
	- In-flight encryption using HTTPS APi
	- At-rest encryption using KMS keys
	- Client-side encryption if the client wants to perform encryption/decryption itself

- **Access Control**: IAM policies to regulate access to the SQS API

- **SQS Access Policies** (similar to S3 bucket policies)
	- Useful for cross-account access to SQS queues
	- Useful for allowing other services (SNS, S3 ...) to write to an SQS queue

## SQS - Standard Queue Hands On
Configuration: Visibility Timeout, Delivery Delay, Receive Message Wait Time, Message Retention Period, Max Message Size (1KB to 256 KB).

Security: For Encryption, either SSE-SQS or SSE-KMS. If use KMS, change Data key reuse period to limit number of API calls to KMS Service. 

Access Policy: Basic: Only queue owner or specified AWS accounts, IAM users and roles

Redrive and Dead-letter queue, we'll see in the future.

Send and Poll Message panel.

## SQS - Message Visibility Timeout
- After a message is polled by a consumer, it becomes **invisible** to other consumers
- By default, the "message visibility timeout" is **30 seconds**
- That means the message has 30 seconds to be processed
- Can be set to 0 seconds to 12 hours
- After the message visibility timeout is over, the message is "visible" in SQS.

- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the **ChangeMessageVisibility** API to get more time
- If visibility timeout is high (hours), and consumer crashes, re-processing will take time
- If visibility timeout is too low (seconds), we may get duplicate
- Reasonable for your application, consumer need to be programmed to call ChangeMessageVisibility API accordingly.


## SQS - Long Polling
- When a consumer requests messages from the queue, it can optionally "wait" for messages to arrive if there are none in the queue
- This is called Long Polling
- **Long polling decreases the number of API calls made to SQS while increasing the efficiency and reducing the latency of applications**
- The wait time can be between 1 sec to 20 sec (20 sec preferrable)
- Long polling is preferable to Short Polling
- Long polling can be enabled at the queue level or at the API level using **WaitTimeSeconds**

## SQS - FIFO Queue
- FIFO = First In First Out (**ordering** of messages in the queue)
- **Limited throughput**: 300 msg/s without batching, 3000 msg/s with batching
- Exactly-once send capability (by removing duplicate)
- Message are processed in order by the consumer
- Name of FIFO queue must end with ``.fifo``
- Can do content-based deduplication (when enabled, the message deduplication id is optional)

- Sending message:  body, message group ID, and deduplication ID

## SQS and ASG
SQS Queue --Poll for messages--> EC2 instances (ASG)
  \\ -- > CloudWatch Metric: Queue length --Alarm for breach--> CloudWatch Alarm --> Scale ASG

 If the load is too big, some transaction may be lost (example: when inserting into DB )

SQS as a buffer to database write

requests --> Enqueue message (ASG) --SendMessage-->SQS Queue(infinitely scalable) --ReceiveMessage--> Dequeue message (ASG) --Insert--> DBs

Only works when the client does not need to have the confirmation that the write has been happened, but we trust once written into SQS, data would be eventually written into DBs.

Decoupling, sudden spike load, timeouts, scale fast -- SQS queue.

## SNS - Simple Notification Service
What if you want to send one message to many receivers?

Direct integration: Buying service sends message to Email notification, Fraud Service, Shipping Service and SQS Queue

Pub/Sub patter: Buying Service -> SNS Topic -> Services

### Amazon SNS
- The "event producer" only sends message to one SNS topic
- As many "event receivers" (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12,500,000 subscriptions per topic
- 100,000 topics limit (can be increased)
- SNS --publish--> Subscribers(**emails, SMS & mobile notifications, HTTP(S) endpoints, SQS, Lambda, Kinesis Data Firehose**)

### SNS integrates with a lot of AWS servcies
- Many AWS services can send data directly to SNS for notifications

### SNS - How to publish
- Topic Publish (using the SDK)
	- Create a topic
	- Create a subscription (or  many)
	- Publish to the topic

- Direct Publish (for mobile apps SDK)
	- Create a platform application
	- Create a platform endpoint
	- Publish to the platform endpoint
	- Works with Google GCM, Apple APNS, Amazon ADM...

### SNS - Security
**Encryption**:
	- In-flight encryption using HTTPS APi
	- At-rest encryption using KMS keys
	- Client-side encryption if the client wants to perform encryption/decryption itself

- **Access Control**: IAM policies to regulate access to the SNS API

- **SNS Access Policies** (similar to S3 bucket policies)
	- Useful for cross-account access to SNS topics
	- Useful for allowing other services (S3 ...) to write to an SNS topic

## SNS + SQS: Fan Out
You want to send a message to multiple SQS queues. If sent individually, it's hard to deal with app crashes, delivery failures, adding more SQS ... 

### Fan Out: 
- push once in SNS, receive in all SQS queues that are subscribers
- Fully decoupled, no data loss
- SQS allows for: data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue **access policy** allows for SNS to write
- Cross-Region Delivery: works with SQS Queues in other regions

### Application: S3 Events to multiple queues
- For the same combination of **event type** (e.g. object creation) and **prefix** (e.g. ``images/``) you can only have **one S3 Event Rule**. 
- If you want to send the same S3 event to many SQS queues, use fan-out

### Applications: SNS to Amazon S3 through Kinesis Data Firehose
SNS can send to Kinesis and therefore we can have the following solutions architecture:
Buying service -> SNS Topic -> KDF -> S3/Any supported KDF Destinations

### SNS - FIFO Topic
Like SQS
- **Ordering** by Message Group ID (all messages in the same group are ordered)
- **Deduplication** using a Deduplication ID or Content Based Deduplication
- **Can only have SQS Standard and FIFO queues as subscribers**
- Limited throughput, same as SQS FIFO (300, 3000msg/s)
- Name have to end with ``.fifo``

In case you need fan-out + ordering + deduplication, use SNS FIFO and SQS FIFO

### SNS - Message Filtering
- JSON policy used to filter messages sent to SNS topic's subscriptions
- If a subscription does not have a filter policy, it receives every message
- For example, filter on the state of the order and send filtered orders to a queue only for placed orders, similar actions may be applied to cancelled orders, or all orders.

## SNS Hands On
Similar as SQS Hands On. If we use email, need to confirm.

## Amazon Kinesis
- Makes it easy to **collect, process and analyze** streaming data in real-time
- Ingest real-time data such as Application logs, Metrics, Website clickstreams, IoT telemetry data... Four services
- **Kinesis Data Streams**: capture, process and store data streams
- **Kinesis Data Firehose**: load data streams into AWS data stores
- **Kinesis Data Analytics**: analyze data streams with SQL or Apache Flink
- **Kinesis Video Streams**: capture, process and store video streams

### Kinesis Data Stream
- Made of multiple shards, from 1 up to N, the number have to be provisioned.
- Shards are going to define stream capacity in terms of ingestion and consumption rates
- Producers - manyfold -- applications, Client, SDK, KPL, Kinesis Agent (All rely on SDK at low level) are going to put record into Data Stream
- A record is made up of Partition Key and Data Blob (up to 1 MB)
- Ingestion rate 1MB / sec or 1000 msg/sec (per shard)
- Consumers - manyfold -- apps, KCL, SDK, Lambda, Kinesis Data Firehose, Kinesis Data Analytics are going to get records from Data Stream
- A record is made up of Partition Key, Sequence Number and Data Blob
- Consumption rate 2MB/sec (shared) Per shard all consumers or 2 MB/sec per shard per consumer if in enhanced fan-out mode.

- Retention between 1 to 365 days
- Ability to replay, reprocess data
- Once data is inserted in Kinesis, it cannot be deleted (immutability)
- Data that shares the same partition goes to the same shard (ordering)
- Producers: AWS SDK, Kinesis Producer Library(KPL), Kinesis Agent
- Consumers: Write your own: Kinesis Consumer Library (KCL), AWS SDK or Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics

Capacity Modes
- Provisioned mode:
	- Choose number of shards provisioned and scale manually
	- Each shard 1MB/s or 1000 msg/s in
	- Each shard 2MB/s out (classic for all consumers or enhanced for every consumer)
	- You pay per shard provisioned per hour
- On-demand mode:
	- No need to provision or manage capacity
	- Default capacity provisioned (4 MB or 4000 msg per sec)\
	- Scales automatically based on observed throughput peak during the last 30 days
	- Pay per stream per hour and data in/out per GB

Security
- Data Stream is deployed in Region level
- Control access / Authorization using IAM policies
- Encryption in flight using HTTPS
- Encryption at rest using KMS
- You can implement encryption / decryption of data on client side (harder)
- VPC Endpoints available for Kinesis to access within VPC (with out going through the internet)
- Monitor API calls using CloudTrail

Hands On
- Shard estimator tool
- Monitoring and configuration to scale the stream
- different AWS CLI versions have different commands
- To consume, describe stream and get shard iterator.

### Kinesis Data Firehose
- Take record data from producers (applications, client, sdk, Kinesis Agent, Data Stream, CloudWatch, AWS IoT), optionally transform the data (Lambda), then batch write to destinations (AWS Destination S3, Amazon Redshift(warehouse in database, copy through S3,), Amazon OpenSearch, 3rd party Partner Destination, HTTP Endpoint custom destinations), further send all or failed data into S3 backup bucket.
- Fully managed service, no administration, automatic scaling, serverless
	- AWS: Redshift, S3, OpenSearch
	- 3rd party destination: Splunk, MongoDB, DataDog, NewRelic...
	- Cutom: send to any HTTP endpoint
- Pay for data going through firehose
- Near Real Time
	- Buffer interval: 0-900 secs
	- Buffer size: 1MB to 128 MB
- Support many data formats, conversions, transformation, compression, Parquet, ORC
- Support custom data transformation using AWS Lambda
- Can send failed or all data to a backup S3 bucket

Data Stream vs Firehose
Data Stream
- Data Stream is a streaming service for ingest at scale
- Write custom code (producer, consumer)
- Real-time (~200 ms)
- Manage scaling (shard splitting, merging)
- Data storage for 1 to 365 days
- Supports replay capability
Firehose
- Load streaming data into S3/Redshift/Openserach/3rd party/ custom HTTP endpoint
- Fully managed
- Near real-time
- Automatic scaling
- No data storage
- Does not support replay capability

Firehose Hands On
- Delivery Stream
- buffer hints, compression and encryption
- Permission - create IAM role automatically

### Kinesis Data Analysis
Will be discussed latere

### Data ordering in Kinesis vs SQS FIFO
Kinesis
- Imaging 100 truck sending GPS data regularly, we need the data in order.
- How should you send data into Kinesis
- Send using a "Partition Key" value of the "truck_id".
- The same key will always go to the same shard

SQS FIFO
- SQS standard, no ordering
- For SQS FIFO, if you don't use a Group ID, messages are consumed in the order they are sent, with **only one consumer**
- You want to scale the number of consumers, but you want messages to be grouped when they are related to each other
- Then you use a Group ID (Similar to Partition Key in Kinesis)

Kinesis vs SQS
- Let's assume 100 trucks, 5 shard, 1 SQS
- Data Steam
	- On average 20 trucks per shard
	- Trucks will have their data ordered within each shard
	- The maximum amount of consumers in parallel we can have is 5
	- Can receive up to 5MB/s of data
- SQS FIFO
	- You only have one SQS FIFO queue
	- You will have 100 Group ID
	- You can have up to 100 Consumers
	- You can have up to 300 messages per seconds (or 3000 if using batching)

### SQS vs SNS vs Kinesis
SQS
- Consumer pull data
- Data is deleted after being consumed
- Can have as many workers as we want
- No need to provision throughput
- Ordering guarantees only on FIFO queues
- Individual message delay capability

SNS
- Push data to many subscribers
- Up to 12,500,000 subscribers
- Data is not persisted 
- Pub/Sub
- Up to 100,000 topics
- No need to provision throughput
- Integrates with SQS for fan-out architecture pattern
- FIFO capability for SQS FIFO

Kinesis
- Standard: pull data 2 MB per shard
- Enhanced fan-out: 2 MB per shard per consumer
- Possibility to replay data
- Meant for real-time big data, analytics and ETL
- Ordering at the shard level
- Data expire after X days
- Provisioned mode or on-demand capacity mode

## Amazon MQ
- SQS SNS are cloud native services: propriety protocols from AWS
- Traditional applications running from on-premises may use open protocols such as MQTT, AMQP, STOMP, Openwire, WSS
- **When migrating to the cloud**, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- **Amazon MQ is a managed message broker service for** Rabbit MQ, Active MQ.
- Amazon MQ does not scale as much as SQS, SNS
- Amazon MQ runs on servers, can run in Multi-AZ with failover
- Amazon MQ has both queue feature (like SQS) and topic feature (like SNS)

High Availability
- Two MQ Brokers at Active and Standby AZ, with EFS as backend storage
- 

# Section 18 Containers on AWS: ECS, Fargate, ECR & EKS
## Docker
- Docker is a software development platform to deploy apps
- Apps are packaged in containers that can be run on any OS
- Apps run the same, regardless of where they're run
	- Any machine
	- No compatibility issues
	- Predictable behavior
	- Less work
	- Easier to maintain and deploy
	- Works with any language, any OS, any technology
- Use cases: microservices architecture, lift and shift apps from on-premises to the AWS cloud

Where are Docker images stored?
- Docker images are stored in Docker Repository
- Docker Hub
	- Public repository
	- Find base images for many technologies or OS
-  Amazon ECR (Amazon Elastic Container Registry)
	- Private repository
	- Public repository (Amazon ECR Public Gallery)

Docker vs VM?
- Docker is sort of a virtualization technology, but not exactly
- Resources are shared with the host => many containers on one server
- Hypervisor -- docker daemon, less secure but light weight

Workflow
- Dockerfile, build, push and pull the image, run

Docker Containers Management on AWS
- Amazon ECS -- Amazon's own container platform
- Amazon EKS -- Amazon's managed Kubernetes (open source)
- Amazon Fargate
	- Amazon's own Serverless container platform
	- Works with ECS and with EKS
- Amazon ECR -- store container image


## ECS (Overview and SA)

EC2 Launch Type
- ECS = Elastic Container Service
- Launch Docker containers on AWS = Launch **ECS Tasks** on ECS Clusters
- **EC2 Launch Type: you must provision and maintain the infrastructure (the EC2 instances)**
- Each EC2 Instance must run the **ECS Agent** to register in the ECS Cluster
- AWS takes care of starting or stopping containers through ECS Agents

Fargate Launch Type
- Launch Docker containers on AWS
- **You do not provision the infrastructure (no EC2 instances to manage)**
- **It's all Serverless! Exams love it!**
- You just create task definitions
- AWS just runs ECS Tasks for you based on the CPU/RAM you need
- To scale, just increase the number of tasks. Simple - no more EC2 instances

IAM Roles for ECS
- **EC2 Instance Profile (EC2 Launch Type Only)**
	- Used by the **ECS agent**
	- Makes API calls to ECS service
	- Send container logs to CloudWatch Logs
	- Pull Docker image from ECR
	- Reference sensitive data in Secretes Manager or SSM Parameter Store
- **ECS Task Role**
	- Allows each task to have a specific role
	- Use different roles for the different ECS Services you run
	- Task Role is defined in the **task definition**

Load Balancer Integrations
- ALB -- supported and works for most use cases
- NLB -- recommended only for high throughput, high performance use cases, or to pair it with AWS Private Link
- CLB -- supported but not recommended, no advanced features, no Fargate

Data Volumes (EFS)
- Mount EFS file systems onto ECS tasks
- Works for both **EC2 and Fargate** launch types
- Tasks running in any AZ will share the same data in the EFS file system
- **Fargate + EFS = Serverless**
- Use cases: persistent multi-AZ shared storage for your containers
- Note: Amazon S3 cannot be mounted as a file system

ECS Service Autoscaling
- Automatically increase or decrease the desired number of ECS tasks
- Amazon ECS Auto Scaling uses **AWS Application Auto Scaling**
	- ECS Service Average CPU Utilization
	- ECS Service Average Memory Utilization - Scale on RAM
	- ALB Request Count Per Target - metric coming from the ALB
- **Target Tracking** - scale based on target value for a specific CloudWatch metric
- **Step Scaling** - scale based on a specified CloudWatch Alarm
- **Scheduled Scaling** - scale based on a specified data/time (predictable changes)
- ECS Service Auto Scaling (task level) != EC2 Auto Scaling (instance level)
- Fargate Auto Scaling is much easier to setup, because **Serverless**

EC2 Launch Type - Auto Scaling EC2 instances
- Accommodate ECS Service Scaling by adding underlying EC2 instances
- **ASG scaling**
	- Scale your ASG based on CPU Utilization
	- Add EC2 instances over time
- **ECS Cluster Capacity Provider**
	- Used to automatically provision and scale the infrastructure for your ECS Tasks
	- Capacity Provider paired with an Auto Scaling Group
	- Add EC2 instances when you're missing capacity (CPU, RAM ...)

ECS tasks invoked by EventBridge
- User upload object into S3 bucket of a Region
- S3 send event to EventBridge, which has a rule to run an ECS Task on receiving that event
- The ECS Task (in Fargate, in ECS Cluster, in VPC) read from S3, process the object and write to DynamoDB

ECS tasks invoked by EventBridge Schedule
- EventBridge trigger every hour the rule to run ECS Task
- The task do some batch processing to S3 (every hour)

ECS - SQS Queue Example
- Messages into SQS Queue, Task polling for messages.
- ECS Service Auto Scaling

ECS - Intercept Stopped Tasks using EventBridge
- Some ECS Task Exited, trigger event and send to EventBridge
- Alert SNS topic and send email to admin
- Task Definition does not cost any money

## ECS Hands On

Cluster
- Infrastructure: AWS Fargate + EC2 Instances
- Under EC2 instances, automatically create ASG, choose OS, instance type, etc
- Network setting, VPC, subnet, security group.
- Go to ASG and there would be a new ASG
- Go to created cluster, infrastructure, there would be fargate, fargate spot and ASG provider
- Change desired cap of ASG to 1, an EC2 instance would be created and registered to the cluster

Service
- Create a task definition
	- name
	- Infrastructure requirements(fargate/ec2)
	- OS/architecture
	- task size (#cpu and memory size)
	- task role -- IAM role assigned to the task, to access AWS service
	- task execution role -- default or created automatically
	- container -- name, image, port mapping, resource allocation limit, env variables (from file or manually),
	- Storage - Ephemeral storage default
- Cluster - DemoCluster - Create Service
- In deployment config, Launch type - Fargate, Platform version - LATEST, Application Type - Service, Service type - replica / deamon
- In Networking, create a new security group, allow http 80 from all ips
- LB -- create an ALB, new listener, new target group
- Click the service, check ALB, container, and visit the DNS name of ALB
- Check tasks, logs and events
- Update service, increase desired tasks, and check LB works

Clean Up
- Set desired tasks to 0, wait for all task end
- Delete Service (CloudFormation handle the stack to delete it) before delete cluster

## ECR
- ECR = Elastic Container Resitry
- Store and manage Docker images on AWS
- **Private and Public** repository (AWS ECR Public Gallery)
- Fully integrated with ECS, backed by Amazon S3
- Assign IAM role to ECS Cluster, which allow instances to pull images from ECR Repo
- Access is controlled through IAM (permission errors then check policy)
- Supports image vulnerability scanning, versioning, image tags, image lifecycle...

## EKS
- EKS = Elastic Kubernetes Service
- A way to launch **managed Kubernetes clusters on AWS**
- Kubernetes is an open-source system for automatic deployment, scaling and management of containerized (usually Docker) application
- It's an alternative to ECS(not open-source), similar goal but different API
- EKS supports **EC2** if you want to deploy worker nodes or **Fargate** to Deploy serverless containers
- **Use case:** if your company is already using Kubernetes on-premises or in another cloud, and wants to migrate to AWS using Kubernetes
- **Kubernetes is cloud-agnostic** (can  be used in any cloud --  Azure, GCP...)

Diagram
- EKS node running EKS pods
- Multi-AZ ASG of nodes
- Public or private LB on different subnets

Node Types
- **Managed Node Groups**
	- Creates and manages Nodes (EC2 instances) for you
	- Nodes are part of an ASG managed by EKS
	- Supports On-Demand or Spot Instances
- **Self-Managed Nodes**
	- Nodes created by you and registered to the EKS cluster and managed by an ASG
	- You can use prebuilt AMI - Amazon EKS Optimized AMI
	- Supports On-Demand or Spot Instances
- **AWS Fargate**
	- No maintenance required; no nodes managed

Data Volumes
- Need to specify **StorageClass** manifest on your EKS cluster
- Leverages a **Container Storage Interface (CSI)** compliant driver
- Supports for
	- EBS
	- EFS (only storage type that works with Fargate)
	- FSx for Lustre
	- FSx for NetApp ONTAP

Hands On
- Create IAM role for cluster, node group
- Add-ons to use EBS volume
## App Runner
- Fully managed service that makes it easy to deploy web applications and APIs at scale
- No infrastructure experience required
- Start with your source code or container image
- Automatically builds and deploy the web app
- Automatic scaling, highly available, load balancer, encryption
- VPC access support
- Connect to database, cache, and message queue services
- Use cases: web apps, APIs, microservices, rapid production deployments

Hands On
- Configure port, scaling, hc, security, networking observability ...
- Going to deploy ASG, Scaling trigger, Fargate container, LB ...


# Section 19 Serverless Overview from a Solution Architect Perspective

## Serverless Introduction
What is serverless?
- Serverless is a new paradigm in which the developers don’t have to manage servers anymore…
- They just deploy code
- They just deploy… functions !
- Initially... Serverless == FaaS (Function as a Service)
- Serverless was pioneered by AWS Lambda but now also includes anything that’s managed: “databases, messaging, storage, etc.”
- **Serverless does not mean there are no servers…** it means you just don’t manage / provision / see them

Serverless in AWS
- AWS Lambda
- DynamoDB
- AWS Cognito (log in)
- AWS API Gateway (rest API)
- Amazon S3 (static content)
- Amazon SNS & SQS
- Amazon Kinesis Data Firehose
- Aurora Serverless
- Step Functions
- Fargate

## Lambda
Why Lambda
EC2
- Virtual Servers in the Cloud
- Limited by RAM and CPU
- Continuously running
- Scaling means intervention to add or remove servers
Lambda
- Virtual **functions** - no servers to manage
- Limited by time - **short execution**
- Run **on-demand**
- **Scaling is automated**

Benefits of Lambda
- Easy Pricing
	- Pay per request and compute time
	- Free tier of 1,000,000 AWS Lambda requests and 400,000 GBsec of compute time
- Integrated with the whole AWS suite of services
- Integrated with many programming languages - Node.js, Python, Java, C#, Ruby, Custom Runtime API (community supported example Rust or Golang)
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 10 GB of RAM!)
- Increasing RAM will also improve CPU and network
- Lambda Container Image -- The container image must implement the Lambda Runtime API, ECS/Fargate is preferred for running arbitrary Docker images

Lambda Integrations 
Main ones
API Gateway, Kinesis, DynamoDB, S3, CloudFront, CloudWatch Events EventBridge, CloudWatch Logs, SNS, SQS, Cognito

Example: Serverless Thumbnail creation
New image in S3 -- trigger -> AWS Lambda Function Creates a Thumbnail -- push -> new Thumbnail in S3; also -- push -> Metadata in DynamoDB

Example: Serverless CRON Job
CloudWatch Events EventBridge -- Trigger every 1 hour -> AWS Lambda Function Perform a task

Lambda Pricing: example
- Pay per **calls**
	- First 1,000,000 requests are free
	- $0.20 per 1 million requests thereafter
- Pay per **duration** (in increment of 1 ms)
	- 400,000 GB-seconds of compute time per month if FREE
	- == 400,000 seconds if function is 1GB RAM
	- After that $1.00 for 600,000 GBs
- It's usually **very cheap** to run AWS Lambda so it's very popular.

Hands On
- Create a function -- by blueprint
- Memory 120 - 10240 MB
- Timeout 1 sec - 15 min
- Monitoring - usage and logs

### Limits - **per region**
- **Execution**
	- Memory allocation 128 MB - 10 GB (1 MB increments)
	- Maximum execution time 900 secs
	- Environment variables (4 KB)
	- Disk capacity in the "function container" (in ``/tmp``): 512 MB to 10 GB
	- Concurrency executions: 1000 (can be increased)
- **Deployment**
	- Lambda function deployment size (compressed .zip): 50 MB
	- Size of uncompressed deployment (code + dependency): 250 MB
	- Can use the ``/tmp`` directory to load other files at startup
	- Size of environment variables: 4KB

### Lambda SnapStart
- Improves your Lambda functions performance up to 10x at no extra cost for **Java 11** and above
- When enabled, function is invoked from a **pre-initialized state** (no function initialization from scratch)
- When you publish a new version:
	- Lambda **initializes** your function
	- Takes a snapshot of memory and disk state of the initialized function
	- Snapshot is cached for low-latency access

### Lambda at Edge and CloudFront Functions
- Many modern applications execute some form of the logic at the edge
- **Edge Function:**
	- A code that you write and attach to CloudFront distributions
	- Runs close to your users to minimize latency
- CloudFront provides two types: **CloudFront Functions & Lambda@Edge**
- You don’t have to manage any servers, deployed globally
- Use case: customize the CDN content
- Pay only for what you use
- Fully serverless

Use cases for Lambda at Edge and CloudFront Functions
- Website Security and Privacy
- Dynamic Web Application at the Edge
- Search Engine Optimization (SEO)
- Intelligently Route Across Origins and Data Centers
- Bot Mitigation at the Edge
- Real-time Image Transformation
- A/B Testing
- User Authentication and Authorization
- User Prioritization
- User Tracking and Analytics

CloudFront Functions
- Lightweight functions written in JavaScript
- For high-scale, latency-sensitive CDN customizations
- Sub-ms startup times, millions of requests/second
- Used to change **Viewer requests and responses**:
	- **Viewer Request**: after CloudFront receives a request from a viewer
	- **Viewer Response**: before CloudFront forwards the response to the viewer
- Native feature of CloudFront (manage code entirely within CloudFront)

Lambda @ Edge
- Lambda functions written in NodeJS or Python
- Scales to **1000s of requests/second**
- Used to change CloudFront requests and responses:
	- **Viewer Request**: after CloudFront receives a request from a viewer
	- **Origin Request**: before CloudFront forwards the request to the origin
	- **Origin Response**: after CloudFront receives the response from the origin
	- **Viewer Response**: before CloudFront forwards the response to the viewer
- Author your functions in one AWS Region (us-east-1), then CloudFront **replicates** to its locations

CloudFront Functions vs Lambda@Edge

|                                    | CloudFront Functions                | Lambda@Edge                                      |
| ---------------------------------- | ----------------------------------- | ------------------------------------------------ |
| Runtime Support                    | JavaScript                          | NodeJS, Python                                   |
| Number of request                  | Millions of requests per sec        | Thousands of requests per sec                    |
| CloudFront Triggers                | Viewer Request/Response             | Viewer Request/Response; Origin Request/Response |
| Max Execution time                 | < 1ms                               | 5-10 seconds                                     |
| Max Memory                         | 2MB                                 | 128 MB - 10 GB                                   |
| Total Package Size                 | 10 KB                               | 1MB - 50 MB                                      |
| Network Access, File System Access | No                                  | Yes                                              |
| Access to the Request Body         | No                                  | Yes                                              |
| Princing                           | Free tier available, 1/6th of @Edge | No Free Tier, charged per request and duration   |
Different Usecase
CloudFront Functions
- Cache key normalization
	- Transform request attributes (headers, cookies, query strings, URL) to create an optimal Cache Key
- Header manipulation
	- Insert,/modify/delete HTTP headers in the request or response
- URL rewrites or redirects
- Request authentication & authorization
	- Create and validate user-generated tokens (e.g. JWT) to allow/deny requests

Lambda@Edge
- Longer execution time (several ms)
- Adjustable CPU or memory
- Your code depends on a 3rd libraries (e.g., AWS SDK to access other AWS services
- Network access to use external services for processing
- File system access or access to the body of HTTP requests

### Lambda In VPC
- By default, your Lambda function is launched outside your own VPC (in an AWS-owned VPC)
- Therefore, it cannot access resources in your VPC (RDS, ElastiCache, internal ELB…)

- You must define the VPC ID, the Subnets and the Security Groups
- Lambda will create an ENI (Elastic Network Interface) in your subnets

Lambda with RDS Proxy
- If Lambda functions directly access your database, they may open too many connections under high load
- RDS Proxy
	- Improve scalability by pooling and sharing DB connections
	- Improve availability by reducing by 66% the failover time and preserving connections
	- Improve security by enforcing IAM authentication and storing credentials in Secrets Manager
- The Lambda function must be deployed in your VPC, **because RDS Proxy is never publicly accessible**

### Lambda and RDS - Invoking Lambda and RDS event notifications
Invoking Lambda from RDS and Aurora
- Invoke Lambda functions from within your DB instance
- Allows you to process **data events** from within a database
- Supported for **RDS for PostgreSQL and Aurora MySQL**
- **Must allow outbound traffic to your Lambda function** from within your DB instance (Public, NAT GW, VPC Endpoints)
- **DB instance must have the required permissions to invoke the Lambda function** (Lambda Resource-based Policy & IAM Policy)

RDS Event Notifications
- Notifications that tells information about the DB instance itself (created, stopped, start, …)
- You **don’t have any information about the data itself**
- Subscribe to the following event categories: **DB instance, DB snapshot, DB Parameter Group, DB Security Group, RDS Proxy, Custom Engine Version**
- Near real-time events (up to 5 minutes)
- Send notifications to SNS or subscribe to events using EventBridge

## Dynamo DB
- Fully managed, highly available with replication across multiple AZs
- NoSQL database - not a relational database - with transaction support
- Scales to massive workloads, distributed database
- Millions of requests per seconds, trillions of row, 100s of TB of storage
- Fast and consistent in performance (single-digit millisecond)
- Integrated with IAM for security, authorization and administration
- Low cost and auto-scaling capabilities
- No maintenance or patching, always available
- Standard & Infrequent Access (IA) Table Class

Basics
- DynamoDB is made of **Tables**
- Each table has a **Primary Key** (must be decided at creation time, partition key + sort key)
- Each table can have an infinite number of items (= rows)
- Each item has **attributes** (can be added over time – can be null)
- Maximum size of an item is **400KB**
- Data types supported are:
	- **Scalar Types** – String, Number, Binary, Boolean, Null
	- **Document Types** – List, Map
	- **Set Types** – String Set, Number Set, Binary Set
- **Therefore, in DynamoDB you can rapidly evolve schemas**

### Read/Write Capacity Modes
- Control how you manage your table’s capacity (read/write throughput)
- **Provisioned Mode (default)**
	- You specify the number of reads/writes per second
	- You need to plan capacity beforehand
	- Pay for **provisioned** Read Capacity Units (RCU) & Write Capacity Units (WCU)
	- Possibility to add **auto-scaling** mode for RCU & WCU
- **On-Demand Mode**
	- Read/writes automatically scale up/down with your workloads
	- No capacity planning needed
	- Pay for what you use, more expensive (\$\$\$)
	- Great for **unpredictable** workloads, **steep sudden spikes**

### DynamoDB Accelerator (DAX)
- Fully-managed, highly available, seamless in-memory cache for DynamoDB
- **Help solve read congestion by caching**
- **Microseconds latency for cached data**
- Doesn’t require application logic modification (compatible with existing DynamoDB APIs)
- 5 minutes TTL for cache (default)
- **VS ElastiCache**: ElastiCache for storing aggregation results, while DAX for individual objects cache; query and scan cache

### DynamoDB - Stream Processing
- Ordered stream of item-level modifications (create/update/delete) in a table
- Use cases:
	- React to changes in real-time (welcome email to users)
	- Real-time usage analytics
	- Insert into derivative tables
	- Implement cross-region replication
	- Invoke AWS Lambda on changes to your DynamoDB table

DynamoDB Stream vs Kinesis Data Stream
- DynamoDB Streams
	- 24 hours retention
	- Limited number of consumers
	- Process using AWS **Lambda** Triggers, or DynamoDB Stream **Kinesis** adapter
- Kinesis Data Streams
	- 1 year retention
	- High number of consumers
	- Process using AWS Lambda, Kinesis Data Analytics, Kineis Data Firehose, AWS Glue Streaming ETL …

### DDB Global Tables
- Make a DynamoDB table accessible with **low latency** in **multiple-regions**
- Active-Active replication, two-way replication
- Applications can **READ and WRITE** to the table in any region
- Must enable **DynamoDB Streams** as a pre-requisite

### DDB - TTL
- Automatically delete items after an expiry timestamp
- Use cases: reduce stored data by keeping only current items, adhere to regulatory obligations, **web session handling**…

### DDB - Backups for disaster recovery
- Continuous backups using point-in-time recovery (PITR)
	- Optionally enabled for the last 35 days
	- Point-in-time recovery to any time within the backup window
	- The recovery process creates **a new table**
- On-demand backups
	- Full backups for long-term retention, until explicitly deleted
	- Doesn’t affect performance or latency
	- Can be configured and managed in AWS Backup (enables cross-region copy)
	- The recovery process creates **a new table**

### DDB - Integration with S3
- **Export to S3 (must enable PITR)**
	- Works for any point of time in the last 35 days
	- Doesn’t affect the read capacity of your table
	- Perform data analysis on top of DynamoDB (Athena)
	- Retain snapshots for auditing
	- ETL on top of S3 data before importing back into DynamoDB
	- Export in DynamoDB JSON or ION format
- **Import from S3**
	- Import CSV, DynamoDB JSON or ION format
	- Doesn’t consume any write capacity
	- Creates a new table
	- Import errors are logged in CloudWatch Logs
## API Gateway
- AWS Lambda + API Gateway: No infrastructure to manage
- Support for the WebSocket Protocol
- Handle API versioning (v1, v2…)
- Handle different environments (dev, test, prod…)
- Handle security (Authentication and Authorization)
- Create API keys, handle request throttling
- Swagger / Open API import to quickly define APIs
- Transform and validate requests and responses
- Generate SDK and API specifications
- Cache API responses

### API Gateway - Integrations High Level
- **Lambda Function**
	- Invoke Lambda function
	- Easy way to expose REST API backed by AWS Lambda
- **HTTP**
	- Expose HTTP endpoints in the backend
	- Example: internal HTTP API on premise, Application Load Balancer…
	- Why? Add rate limiting, caching, user authentications, API keys, etc…
- **AWS Service**
	- Expose any AWS API through the API Gateway
	- Example: start an AWS Step Function workflow, post a message to SQS
	- Why? Add authentication, deploy publicly, rate control…
	- Example: Client --request-> API Gateway --send--> Kinesis Data Stream --records-> Kinesis Data Firehose --store json files-> Amazon S3

### API Gateway - Endpoints Types
- **Edge-Optimized (default): For global clients**
	- Requests are routed through the CloudFront Edge locations (improves latency)
	- The API Gateway **still lives in only one region**
- **Regional:**
	- For clients within the same region
	- Could manually combine with CloudFront (more control over the caching strategies and the distribution)
- **Private**
	- Can only be accessed from your VPC using an interface VPC endpoint (ENI)
	- Use a resource policy to define access

### API Gateway - Security
- **User Authentication through**
	- IAM Roles (useful for internal applications)
	- Cognito (identity for external users – example mobile users)
	- Custom Authorizer (your own logic)
- **Custom Domain Name HTTPS** security through integration with AWS Certificate Manager (ACM)
	- If using Edge-Optimized endpoint, then the certificate must be in **us-east-1** 
	- If using Regional endpoint, the certificate must be in the API Gateway region
	- Must setup CNAME or A-alias record in Route 53

Hands On
API Types - take rest API as an example
Endpoint Type - Regional, Edge-optimized, private
Integration Type - Lambda, Http Endpoint, Mock, AWS service, VPC Link
Configuration - permission - resource based policy, will be updated
Request and Response will be process by gateway (JSON to HTTP request/response)
Stage and invoke URL


## Step Functions
- Build serverless visual workflow to orchestrate your Lambda functions
- **Features: sequence, parallel, conditions, timeouts, error handling, …**
- Can integrate with EC2, ECS, On-premises servers, API Gateway, SQS queues, etc…
- Possibility of implementing human approval feature
- **Use cases**: order fulfillment, data processing, web applications, any workflow

## Cognito
- Give users an identity to interact with our web or mobile application
- **Cognito User Pools**:
	- Sign in functionality for app users
	- Integrate with API Gateway & Application Load Balancer
- **Cognito Identity Pools (Federated Identity):**
	- Provide AWS credentials to users so they can access AWS resources directly
	- Integrate with Cognito User Pools as an identity provider
- **Cognito vs IAM**: “hundreds of users”, ”mobile users”, “authenticate with SAML”

### Cognito User Pools (CUP) – User Features
- **Create a serverless database of user for your web & mobile apps**
- Simple login: Username (or email) / password combination
- Password reset
- Email & Phone Number Verification
- Multi-factor authentication (MFA)
- Federated Identities: users from Facebook, Google, SAML…
- CUP integrates with **API Gateway** and **Application Load Balancer**
- With API Gateway, client authenticate with CUP and retrieve tokens, then trigger rest API with pass token on API Gateway, API Gateway evaluate Cognito Token and interact with backend
- With ALB, client send request to ALB, ALB authenticate with CUP and route request to different target group based on LB policies.

### Cognito Identity Pools (Federated Identities)
- **Get identities for “users” so they obtain temporary AWS credentials**
- Users source can be Cognito User Pools, 3rd party logins, etc…
- **Users can then access AWS services directly or through API Gateway**
- The IAM policies applied to the credentials are defined in Cognito
- They can be customized based on the user_id for fine grained control
- **Default IAM roles** for authenticated and guest users

Example: row level security in Dynamo DB

# Section  20 Serverless Solution Architecture Discussion

## MyTodoList
- Requirements
	- Expose as REST API with HTTPS
	- Serverless architecture
	- Users should be able to directly interact with their own folder in S3
	- Users should authenticate through a managed serverless service
	- The users can write and read to-dos, but they mostly read them
	- The database should scale, and have some high read throughput
- Start with Client -- API Gateway -- Lambda -- DynamoDB
- For Auth, add Cognito
- For S3 access, use CIP and **temporary credentials**
- For DDB caching, add DAX (without increasing RCU much)
- Another level of caching at API Gateway level

Summary
- Serverless REST API: HTTPS, API Gateway, Lambda, DynamoDB
- Using Cognito to generate temporary credentials to access S3 bucket with restricted policy. App users can directly access AWS resources this way. Pattern can be applied to DynamoDB, Lambda…
- Caching the reads on DynamoDB using DAX
- Caching the REST requests at the API Gateway level
- Security for authentication and authorization with Cognito

## MyBlog.com
- Requirements
	- This website should scale globally
	- Blogs are rarely written, but often read
	- Some of the website is purely static files, the rest is a dynamic REST API
	- Caching must be implement where possible
	- Any new users that subscribes should receive a welcome email
	- Any photo uploaded to the blog should have a thumbnail generated
	- All serverless
- To serve globally, use CloudFront to cache data from S3
- To serve securely, add OAC to S3 bucket policy (only from cloudfront dsitribution)
- API -- Lambda -- DAX -- DDB (global table)
- Welcome email: DDB stream changes --invoke-> Lambda(with IAM role) --SDK to send email-> SES(simple email servcie)
- For photo upload, optionally through CloudFront Global distribution; upload of a file --trigger-> Lambda --Thumbnail-> S3 --optional-> SQS or SNS

Summary
- We’ve seen static content being distributed using CloudFront with S3
- The REST API was serverless, didn’t need Cognito because public
- We leveraged a Global DynamoDB table to serve the data globally
- (we could have used Aurora Global Database)
- We enabled DynamoDB streams to trigger a Lambda function
- The lambda function had an IAM role which could use SES
- SES (Simple Email Service) was used to send emails in a serverless way
- S3 can trigger SQS / SNS / Lambda to notify of events

## MicroService Architecture
- We want to switch to a micro service architecture
- Many services interact with each other directly using a REST API
- Each architecture for each micro service may vary in form and shape

- We want a micro-service architecture so we can have a leaner development lifecycle for each service

Discussion
- **You are free to design each micro-service the way you want**
- Synchronous patterns: API Gateway, Load Balancers
- Asynchronous patterns: SQS, Kinesis, SNS, Lambda triggers (S3)
- Challenges with micro-services:
	- repeated overhead for creating each new microservice,
	- issues with optimizing server density/utilization
	- complexity of running multiple versions of multiple microservices simultaneously
	- proliferation of client-side code requirements to integrate with many separate services.
- Some of the challenges are solved by Serverless patterns:
	- API Gateway, Lambda scale automatically and you pay per usage
	- You can easily clone API, reproduce environments
	- Generated client SDK through Swagger integration for the API Gateway

## Software updates distribution
- We have an application running on EC2, that distributes software updates once in a while
- When a new software update is out, we get a lot of request and the content is distributed in mass over the network. It’s very costly
- We don’t want to change our application, but want to optimize our cost and CPU, how can we do it?

Our application current state
Client -> ELB multi AZs -> EC2 instances sharing EFS
Solution: just place a CloudFront in front of ELB
- No changes to architecture
- Will cache software update files at the edge
- Software update files are not dynamic, they’re static (never changing)
- Our EC2 instances aren’t serverless
- But CloudFront is, and will scale for us
- Our ASG will not scale as much, and we’ll save tremendously in EC2
- We’ll also save in availability, network bandwidth cost, etc
- Easy way to make an existing application more scalable and cheaper!


# Section 21 Database in AWS

### Choosing the right database
- We have a lot of managed databases on AWS to choose from
- Questions to choose the right database based on your architecture:
	- Read-heavy, write-heavy, or balanced workload? Throughput needs? Will it change, does it need to scale or fluctuate during the day?
	- How much data to store and for how long? Will it grow? Average object size? How are they accessed?
	- Data durability? Source of truth for the data ?
	- Latency requirements? Concurrent users?
	- Data model? How will you query the data? Joins? Structured? Semi-Structured?
	- Strong schema? More flexibility? Reporting? Search? RDBMS / NoSQL?
	- License costs? Switch to Cloud Native DB such as Aurora?

## DB Types
- **RDBMS (= SQL / OLTP)**: RDS, Aurora – great for joins
- **NoSQL database – no joins, no SQL** : DynamoDB (~JSON), ElastiCache (key / value pairs), Neptune (graphs), DocumentDB (for MongoDB), Keyspaces (for Apache Cassandra)
- **Object Store**: S3 (for big objects) / Glacier (for backups / archives)
- **Data Warehouse (= SQL Analytics / BI)**: Redshift (OLAP), Athena, EMR
- **Search**: OpenSearch (JSON) – free text, unstructured searches
- **Graphs**: Amazon Neptune – displays relationships between data
- **Ledger**: Amazon Quantum Ledger Database
- **Time series**: Amazon Timestream
- Note: some databases are being discussed in the Data & Analytics section

## Amazon RDS – Summary
- Managed PostgreSQL / MySQL / Oracle / SQL Server / DB2 / MariaDB / Custom
- Provisioned RDS Instance Size and EBS Volume Type & Size
- Auto-scaling capability for Storage
- Support for Read Replicas and Multi AZ
- Security through IAM, Security Groups, KMS , SSL in transit
- Automated Backup with Point in time restore feature (up to 35 days)
- Manual DB Snapshot for longer-term recovery
- Managed and Scheduled maintenance (with downtime)
- Support for IAM Authentication, integration with Secrets Manager
- RDS Custom for access to and customize the underlying instance (Oracle & SQL Server)
- **Use case**: Store relational datasets (RDBMS / OLTP), perform SQL queries, transactions

## Amazon Aurora – Summary
- Compatible API for PostgreSQL / MySQL, separation of storage and compute
- Storage: data is stored in 6 replicas, across 3 AZ – highly available, self-healing, auto-scaling
- Compute: Cluster of DB Instance across multiple AZ, auto-scaling of Read Replicas
- Cluster: Custom endpoints for writer and reader DB instances
- Same security / monitoring / maintenance features as RDS
- Know the backup & restore options for Aurora
- **Aurora Serverless** – for unpredictable / intermittent workloads, no capacity planning
- **Aurora Globa**l: up to 16 DB Read Instances in each region, < 1 second storage replication
- **Aurora Machine Learning**: perform ML using SageMaker & Comprehend on Aurora
- **Aurora Database Cloning**: new cluster from existing one, faster than restoring a snapshot
- **Use case**: same as RDS, but with less maintenance / more flexibility / more performance / more features

## Amazon ElastiCache – Summary
- Managed Redis / Memcached (similar offering as RDS, but for caches)
- In-memory data store, sub-millisecond latency
- Select an ElastiCache instance type (e.g., cache.m6g.large)
- Support for Clustering (Redis) and Multi AZ, Read Replicas (sharding)
- Security through IAM, Security Groups, KMS, Redis Auth
- Backup / Snapshot / Point in time restore feature
- Managed and Scheduled maintenance
- **Requires some application code changes to be leveraged**
- **Use Case**: Key/Value store, Frequent reads, less writes, cache results for DB
queries, store session data for websites, cannot use SQL.

## Amazon DynamoDB – Summary
- AWS proprietary technology, managed serverless NoSQL database, millisecond latency
- Capacity modes: provisioned capacity with optional auto-scaling or on-demand capacity
- Can replace ElastiCache as a key/value store (storing session data for example, using TTL feature)
- Highly Available, Multi AZ by default, Read and Writes are decoupled, transaction capability
- DAX cluster for read cache, microsecond read latency
- Security, authentication and authorization is done through IAM
- Event Processing: DynamoDB Streams to integrate with AWS Lambda, or Kinesis Data Streams
- Global Table feature: active-active setup
- Automated backups up to 35 days with PITR (restore to new table), or on-demand backups
- Export to S3 without using RCU within the PITR window, import from S3 without using WCU
- **Great to rapidly evolve schemas**
- **Use Case**: Serverless applications development (small documents 100s KB), distributed serverless cache

## Amazon S3 – Summary
- S3 is a… key / value store for objects
- Great for bigger objects, not so great for many small objects
- Serverless, scales infinitely, max object size is 5 TB, versioning capability
- **Tiers**: S3 Standard, S3 Infrequent Access, S3 Intelligent, S3 Glacier + lifecycle policy
- **Features**: Versioning, Encryption, Replication, MFA-Delete, Access Logs…
- **Security**: IAM, Bucket Policies, ACL, Access Points, Object Lambda, CORS, Object/Vault Lock
- **Encryption**: SSE-S3, SSE-KMS, SSE-C, client-side, TLS in transit, default encryption
- **Batch operations** on objects using S3 Batch, listing files using S3 Inventory
- **Performance**: Multi-part upload, S3 Transfer Acceleration, S3 Select
- **Automation**: S3 Event Notifications (SNS, SQS, Lambda, EventBridge)
- **Use Cases**: static files, key value store for big files, website hosting

## DocumentDB
- Aurora is an “AWS-implementation” of PostgreSQL / MySQL …
- **Not Serverless! Not Global!**
- **DocumentDB is the same for MongoDB (which is a NoSQL database)**
- MongoDB is used to store, query, and index JSON data
- Similar “deployment concepts” as Aurora
- Fully Managed, highly available with replication across 3 AZ
- DocumentDB storage automatically grows in increments of 10GB
- Automatically scales to workloads with millions of requests per seconds

## Amazon Neptune
- Fully managed **graph database**
- A popular **graph dataset** would be a **social network**
	- Users have friends
	- Posts have comments
	- Comments have likes from users
	- Users share and like posts…
- Highly available across 3 AZ, with up to 15 read replicas
- Build and run applications working with highly connected datasets – optimized for these complex and hard queries
- Can store up to billions of relations and query the graph with milliseconds latency
- Highly available with replications across multiple AZs
- Great for knowledge graphs (Wikipedia), fraud detection, recommendation engines, social networking

Neptune Stream
- **Real-time ordered** sequence of every change to your graph data
- Changes are available immediately after writing
- **No duplicates, strict order**
- Streams data is accessible in an **HTTP REST API**
- Use cases:
	- Send **notifications** when certain changes are made
	- Maintain your graph data **synchronized in another data store** (e.g., S3, OpenSearch, ElastiCache)
	- **Replicate data across regions** in Neptune
## Amazon Keyspaces(for Apache Cassandra)
- *Apache Cassandra is an open-source NoSQL distributed database*
-  A managed Apache Cassandra-compatible database service
- Serverless, Scalable, highly available, fully managed by AWS
- Automatically scale tables up/down based on the application’s traffic
- Tables are replicated 3 times across multiple AZ
- Using the Cassandra Query Language (CQL)
- Single-digit millisecond latency at any scale, 1000s of requests per second
- Capacity: On-demand mode or provisioned mode with auto-scaling
- Encryption, backup, Point-In-Time Recovery (PITR) up to 35 days
- Use cases: store IoT devices info, time-series data, …

## Amazon QLDB
- QLDB stands for ”Quantum Ledger Database”
- A ledger is a book **recording financial transactions**
- Fully Managed, Serverless, High available, Replication across 3 AZ
- Used to **review history of all the changes made to your application data** over time
- **Immutable** system: no entry can be removed or modified, cryptographically verifiable

-  2-3x better performance than common ledger blockchain frameworks, manipulate data using SQL
- Difference with Amazon Managed Blockchain: **no decentralization component**, in accordance with financial regulation rules
## Amazon Timestream
- Fully managed, fast, scalable, serverless **time series database**
- Automatically scales up/down to adjust capacity
- Store and analyze trillions of events per day
- 1000s times faster & 1/10th the cost of relational databases
- Scheduled queries, multi-measure records, SQL compatibility
- Data storage tiering: recent data kept in memory and historical data kept in a cost-optimized storage
- Built-in time series analytics functions (helps you identify patterns in your data in near real-time)
- Encryption in transit and at rest
- Use cases: IoT apps, operational applications, real-time analytics, …
- Data From: AWS IoT, Kinesis DS + Lambda, Prometheus, telegraf, Kinesis DS & MSK + Kinesis DA for Apache Flink
- Data To: QuickSight, SageMaker, Grafana, JDBC connection

# Section 22 Data & Analytics

## Amazon Athena
- **Serverless** query service to analyze data stored in Amazon S3
- Uses standard SQL language to query the files (built on Presto)
- Supports **CSV, JSON, ORC, Avro, and Parquet**
- Pricing: $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards
- **Use cases**: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, **CloudTrail trails**, etc...
- **Exam Tip**: analyze data in S3 using serverless SQL, use Athena

### Athena Performance Improvement
- Use **columnar data** for cost-savings (less scan)
	- Apache Parquet or ORC is recommended
	- Huge performance improvement
	- Use Glue to convert your data to Parquet or ORC
- **Compress data** for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- **Partition** datasets in S3 for easy querying on virtual columns
```
s3://yourBucket/pathToTable
				/<PARTITION_COLUMN_NAME>=<VALUE>
				/<PARTITION_COLUMN_NAME>=<VALUE>
				/<PARTITION_COLUMN_NAME>=<VALUE>
				/etc…
```

- Example: ``s3://athena-examples/flight/parquet/year=1991/month=1/day=1/``
- **Use larger files (> 128 MB)** to minimize overhead

### Athena Federated Query
- Allows you to run **SQL queries** across data stored in **relational, non-relational, object, and custom data sources** (AWS or on-premises)
- Uses **Data Source Connectors** that run on AWS Lambda to run Federated Queries (e.g., CloudWatch Logs, DynamoDB, RDS, ElastiCache, DocumentDB, Redshift, Aurora, HBase in EMR…)
- Store the results back in Amazon **S3**

Hands On

SQL create table and run queries
## Redshift
• Redshift is based on PostgreSQL, but it’s **not used for OLTP**
• It’s **OLAP – online analytical processing (analytics and datawarehousing)**
• 10x better performance than other data warehouses, scale to PBs of data
• Columnar storage of data (instead of row based) & parallel query engine
• Pay as you go based on the instances provisioned
• Has a SQL interface for performing the queries
• BI tools such as Amazon Quicksight or Tableau integrate with it
• **vs Athena**: faster queries / joins / aggregations thanks to indexes

### Redshift Cluster
- Leader node: for query planning, results aggregation
- Compute node: for performing the queries, send results to leader
- You provision the node size in advance
- You can used Reserved Instances for cost savings

### Redshift Snapshot and Disaster Recovery
- **Redshift has “Multi-AZ” mode for some clusters**
- Snapshots are point-in-time backups of a cluster, stored internally in S3
- Snapshots are incremental (only what has changed is saved)
- You can restore a snapshot into **a new cluster**
- Automated: every 8 hours, every 5 GB, or on a schedule. Set retention between 1 to 35 days
- Manual: snapshot is retained until you delete it
- **You can configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region**

### Loading data into Redshift
- **Large inserts are MUCH better**
- Three patterns
- Kinesis DF -> Redshift Cluster
- S3 Bucket -- Internet w/o Enhanced VPC Routing or Through VPC w/ Enhanced Routing --> Redshift Cluster
- EC2 instance --JDBC driver--> Redshift Cluster

### Redshift Spectrum
- Query data that is already in S3 without loading it
- **Must have a Redshift cluster available to start the query**
- The query is then submitted to thousands of **Redshift Spectrum nodes**

## Amazon OpenSearch Service
- **Amazon OpenSearch is successor to Amazon ElasticSearch**
- In DynamoDB, queries only exist by primary key or indexes…
- **With OpenSearch, you can search any field, even partially matches**
- It’s common to use OpenSearch as a complement to another database
- Two modes: managed cluster or serverless cluster
- Does not natively support SQL (can be enabled via a plugin)
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Cognito & IAM, KMS encryption, TLS
- Comes with OpenSearch Dashboards (visualization)

### OpenSearch Patterns
- DDB Table -> DDB Stream -> Lambda Function -> OpenSearch; EC2 instances behind ELB to **retrieve** items from DDB table or **search** items from OpenSearch by calling APIs
- CloudWatch Logs --> Subscription Filter --> Lambda Function --Real time--> OpenSearch
- CloudWatch Logs --> Subscription Filter --> Kinesis Data Firehose --Near Real Time--> OpenSearch
- Kinesis DS -> Kinesis DF (optional transformation by Lambda) 

## EMR
Hadoop and Big Data
bundled with Spark, Flink, HBase, Presto
Master, Core and Task nodes
## QuickSight
Serverless Business Intelligence
SPICE - need to load data into QS
Own users groups
## Glue
Serverless ETL
Catalog Crawler --write--> metadata then integrate with Athena, redshift, EMR
Bookmark, DataBrew, Elastic View, Studio, Streaming ETL
## Lake Formation
Fine Grained Access Control
S3, RDS, NoSQL BD as sources
## Kinesis Data Analytics
KDS, KDF -> KDA -> KDS,KDF
KDA for Apache Flink <- MSK
## MSK
Like Kinesis, managed Kafka on AWS. Serverless.


# Section 23 Machine Learning
Rekognition -- objects, people, text, scenes in images and videos
Transcribe -- speech to text, PII
Polly -- Lexicon(customize pronunciation) and SSML(addressing, breathing)
Translate
Lex & Connect -- Speech Recognition for chatbots and contact center
Comprehend -- NLP, Medical PHI
SageMaker -- own ML model
ForeCast -- forecasting
Kendra -- document searching service
Personalize -- personalization
Textract -- OCR

# Section 24 Monitoring Audit and Performance

## CW Metrics
For every services.
Belong to namespaces
Dimension
timestamps
Dashboard
Custom Metric
Filtering
CW Metrics Streams - near-real-time delivery

Workflow: CW Metrics - KDF - S3 + Athena / Redshift / OpenSearch (near real-time)
## CW Logs
Log groups, Log streams
expiration policy
Destination: S3, KDS, KDF, Lambda, OpenSearch
Encrypted (optionally KMS)
CW Logs Insight
S3 Export directly -- slow, use subs
Logs Subscription (Filter) --> KDS, KDF, Lambda --> S3
Aggregation
Cross-account subscription (use subs destination, configure access policy & IAM role)
Live Tail
## CW Agent
Write CW logs on EC2, need IAM permissions
Agent can also on-premises
Logs Agent and **Unified Agent**, latter has system-level metrics, may with SSM Parameter Store
## CW Alarms
Trigger notification for metrics, can be created based on Metrics Filters
States: OK, INSUFFICIENT_DATA, ALARM
Period: vary, high resolution
Target: Action to an EC2 instance; Auto Scaling Action, SNS
Composite Alarm: AND, OR conditions
Work with Instance Recovery: same private public elastic IP, placement group
Test alarm: set alarm state
## EventBridge
Schedule Cron jobs
Event Pattern
Trigger Lambda, send SQS/SNS messages

Send JSON to target
Event bus with partner or custom
Schema Registry

Resource-based Policy from another AWS account or Region (example: aggregate all events from AWS Org)
## Insight
Container
Lambda
Contributor (what is impacting)
Application (services and apps with Sagemaker)
## CloudTrail
Provides governance, compliance and audit for your AWS Account
Get an history of events / API calls made within your AWS Account
Can put logs from CloudTrail into CloudWatch Logs or S3
A trail can be applied to All Regions (default) or a single Region.
For deletion investigation
Management, Data and CloudTrail Insights events
CloudTrail Insights: detect unusual activity pattern
Retention: default 90 days, further to S3

With EventBridge -- Intercept API calls or monitor API calls
## Config
auditing and recording compliance of your AWS resources
receive alerts (SNS notifications) for any changes
per-region service

Config Rules: managed or custom, does not prevent from happening

Remediation and Notification

## CW vs CT vs Config
CW - perf metrics, events, alarm. (log aggr, analysis)
CT - monitor API calls
Config - config change, compliance rules

# Section 25 IAM advanced TODO

## Organization

## Policies

## Policies Evaluation Logics

## IAM Identity Center

## Directory Services

## Control Tower

# Section 26 Security TODO


# Section 27 VPC
## CIDR, Private and Public IP
private IP only allow
10.0.0.0/8 - big network
172.16.0.0/12 - AWS default VPC in this range
192.168.0.0/16 - home network

## VPC
Default
- Come with new AWS account
- New EC2 instance launch here if subnet not specified
- Come with Internet Connectivity and EC2 auto assign public IPv4 address  (need to enable manually in subnet setting for new VPC)
- Come with a public and a private DNS name
- 3 subnets (one for each AZ), 5 reserved IP address for each subnet
- subnet has: route table, NACL, CIDR reservation, sharing

General
- 5 VPCs per region, 5 CIDR (16 - 65536) per VPC
- VPC CIDR should not overlap with other network

## Subnet
- First 4 and last 1 IP addresses are reserved by AWS
- Name, AZ(HA), CIDR Block (does not overlap with other subnet)

## IGW and Route Tables (VPC and VPC/subnet)
- Allow resources in VPC connect to the Internet
- Scales horizontally and highly available and redundant
- Must be created separately from a VPC
- One VPC can only be attached to one IGW and vice versa
- Internet Gateways on their own do not allow Internet access.... Route tables must be edited!
- Create IGW, create route tables (associate with public subnets) add rule routing all other IP to IGW
## Bastion Hosts (subnet)
- Users access private subnet: user --ssh-- bastion hosts --ssh-- instances in private subnet
- Must be in the public sub net (then connected to all private subnets because it's in VPC)
- **SG of Bastion Hosts must allow** inbound traffic from Internet on port 22 from **restricted CIDR** i.e. public CIDR of corporation
- **SG of EC2 in private subnets** must allow SG of Bastion Host or the private IP of the Bastion host
- EC2 instance SG: allow ssh from custom source -- sg of bastion host
## NAT Instances and NAT Gateways (subnet/inter-subnet)
NAT Instances
- To allow EC2 in private subnet have internet access
- NAT = Network Address Translation
- Must be launched in a public subnet (with proper SG)
- Must **disable source/destination Check**
- Must have EIP attached to it
- Route Tables must be configured to route (outgoing) traffic from private subnets to the NAT Instances
- ``ping`` use ICMP protocol

- There pre configured AMI reaching end of support
- Not highly available / resilient set up out of the box
	- You need to create ASG in Multi-AZ and resilient user-data script
- Internet bandwidth depends on EC2 instance type
- You must manage Security Groups and rules
	- Inbound of HTTPS from Private Subnets and SSH from home/corporate network
	- Outbound of HTTPS to the Internet

NAT Gateways
- AWS Managed NAT, higher bandwidth, high availability, no admin
- Pay per hour for usage and bandwidth
- NATGW is created **in a specific AZ**, uses **an Elastic IP**
- Can't be used by EC2 instance in the same subnet (only from other subnet)
- Requires an IGW (Private subnet -- NATGW -- IGW)
- 5 Gbps bandwidth with automatic scaling up to 100 Gbps
- No Security Groups to manage / required

- NAT Gateway is **resilient within a single Availability Zone**
- Must create **multiple NAT Gateways** in **multiple AZs for fault-tolerance**
- There is **no cross-AZ failover** needed because if an AZ goes down it doesn't need NAT
- HandsOn: choose subnet, connectivity type, EIP. **Configure private route table**

NATGW vs NAT Instance
- Availability -- HA within AZ -- use a script to manage failover between instances
- Bandwidth -- Up to 100 Gbps -- Depends on EC2 instance types
- Maintenance -- Managed by AWS -- Managed by you
- Cost -- Per hour and amount of data transferred -- Per hour EC2 instance type and size + network
- Public IPv4 -- yes -- yes
- Private ipv4 -- yes -- yes
- Security Groups -- no -- yes
- Use as Bastion Host -- no -- yes
## NACL (subnet)
- SG rules are **stateful** (once in, auto allow out) while NACL rules are **stateless** (does not care if once in)
- Inbound and outbound of SG is defined by who trigger the connection/session
- NACL are like a firewall which control traffic from and to **subnets**
- **One NACL per subnet, new subnets are assigned the Default NACL**
- You define **NACL Rules**:
	- Rules have a number (1-32766), higher precedence with a lower number
	- First rule match will drive the decision
	- The last rule is an asterisk (\*) and denies a request in case of no rule match
	- AWS recommends adding rules by increment of 100
- Newly created NACLs will deny everything
- NACL are a great way of blocking a specific IP address at the subnet level

- Do **NOT** modify the **Default NACL**(every thing in every thing out), instead create custom NACLs
- Ephemeral Ports -- Clients connect to a **defined port**, and expect a response on an **ephemeral port**, the port range depends on the OS

SG vs NACL
- Instance level -- Subnet level
- allow rules only -- allow and deny
- Stateful (return traffic is automatically allowed) -- stateless
- All rules are evaluated -- first match (lower to highest)
- Have to apply to the instance -- automatically applied to the instances in the subnet

## VPC Peering (inter VPC, inter region, inter account)
- Privately connect two VPCs using AWS’ network
- Make them behave as if they were in the same network
- Must not have overlapping CIDRs
- VPC Peering connection is **NOT transitive** (must be established for each VPC that need to communicate with one another)
- **You must update route tables in each VPC’s subnets to ensure EC2 instances can communicate with each other (pcx-xxxx)**
- You can create VPC Peering connection between VPCs in **different AWS accounts/regions**
- You can reference a security group in a peered VPC (works cross accounts – same region)


## VPC Endpoints (VPC / subnets?)
- Every AWS service is publicly exposed
- To Access AWS services without going through public internet
- VPC Endpoints (powered by AWS PrivateLink) allows you to connect to AWS services using a **private network** instead of using the public Internet
- They’re redundant and scale horizontally
- They remove the need of IGW, NATGW, … to access AWS Services
- In case of issues:
	- Check DNS Setting Resolution in your VPC
	- Check Route Tables

Types of Endpoints
- **Interface Endpoints (powered by PrivateLink)**
	- Provisions an ENI (private IP address) as an entry point (must attach a Security Group)
	- Supports most AWS services
	- \$ per hour + \$ per GB of data processed
- **Gateway Endpoints**
	- Provisions a gateway and must be used **as a target in a route table (does not use security groups)**
	- Supports both **S3 and DynamoDB**
	- Free
- Gateway is most likely going to be preferred all the time at the exam
- Cost: free for Gateway, $ for interface endpoint
- Interface Endpoint is preferred access is required from on-premises (Site to Site VPN or
Direct Connect), a different VPC or a different region
- HandsOn - can be attached to subnets, and modify route table in configuration, cli default region us-east-1, use ``aws s3 ls --region eu-central-1``

## VPC Flow Logs (multi-level)
- Capture information about IP traffic going into your interfaces, **VPC, Subnet, ENI** levels
- Helps to monitor & troubleshoot connectivity issues
- Flow logs data can go to **S3, CloudWatch Logs, and Kinesis Data Firehose**
- Captures network information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift, WorkSpaces, NATGW, Transit Gateway…
- schema -- src/dst addr; src/dst port; Action ...
- **Query VPC flow logs using Athena on S3 or CloudWatch Logs Insights**
- VPC Flow Logs troubleshooting SG and NACL issues: **check the action field**
- Workflow: CW Logs + CW Contributor Insights; CW Logs + CW Alarm + SNS ); S3 + Athena + QuickSight

## Site-to-Site VPN
- Virtual Private Gateway
	- VPN concentrator on AWS side of VPN connection
	- Attached to VPC
- Customer Gateway
	- Software application or physical device on customer side of the VPN connection
	- Should use Public Internet-routable IP address for CGW, if behind a NAT that's enabled for NAT-T (NAT traversal), use public IP of NAT device
	- **Important**: enable **Route Propagation** for the VGW in the route table associated with the subnet
	- using ping, don't forget ICMP port on SG
- VPN CloudHub -- communication between multiple sites, if you have multiple VPN connection on the same VGW

## Direct Connect (DX, VPC)
- Provides a dedicated **private** connection from a remote network to your VPC
- Dedicated connection must be setup between your DC and AWS Direct Connect locations
- You need to setup a Virtual Private Gateway on your VPC
- Access **public resources** (S3) and **private** (EC2) on same connection by virtual interfaces
- Use Cases:
	- Increase bandwidth throughput - working with large data sets – lower cost
	- More consistent network experience - applications using real-time data feeds
	- Hybrid Environments (on prem + cloud)
- Supports both IPv4 and IPv6
- In VPC, VGW is needed. In DX Location, 2 cages DX Endpoint and Customer or partner router. In data center, Customer router/firewall

DX Gateway
- If you want to setup a Direct Connect to one or more VPC **in many different regions**(same account), you must use a Direct Connect Gateway
- Still pay attention CIDR do not overlap

Connection Types
- **Dedicated Connections**: 1Gbps,10 Gbps and 100 Gbps capacity
	- Physical ethernet port dedicated to a customer
	- Request made to AWS first, then completed by AWS Direct Connect Partners
- **Hosted Connections**: 50Mbps, 500 Mbps, to 10 Gbps
	- Connection requests are made via AWS Direct Connect Partners
	- Capacity can be **added or removed on demand**
	- 1, 2, 5, 10 Gbps available at select AWS Direct Connect Partners
- Lead times are often longer than 1 month to establish a new connection

Encryption
- Data in transit is **not encrypted** but is private
- AWS Direct Connect + **VPN** provides an IPsec-encrypted private connection (between dx endpoint and customer network)
- Good for an extra level of security, but slightly more complex to put in place

Resiliency
- High Resiliency -- One connection at multiple locations
- Max Resiliency -- separate connections terminating on separate devices in more than one location

DX + S2S VPN
DX or VPN as backup connection, different cost.


## Transit Gateway (region/ inter region)
- Network topology becomes complicated
- **For having transitive peering between thousands of VPC and on-premises, hub-and-spoke (star) connection**
- Regional resource, can work cross-region
- Share cross-account using Resource Access Manager (RAM)
- You can peer Transit Gateways across regions
- **Route Tables: limit** which VPC can talk with other VPC
- Works with Direct Connect Gateway, VPN connections (Diagram)
- Supports **IP Multicast** (not supported by any other AWS service)

Transit Gateway: S2S VPN ECMP
- ECMP = Equal-cost multi-path routing
- Routing strategy to allow to forward a packet over multiple best path
- Use case: create multiple Site-to-Site VPN connections **to increase the bandwidth of your connection to AWS**
- TGW can use 2 tunnels of S2S VPN, double throughput, and you can add more S2S VPN and increase throughput further (but costly)
- TGW: **share DX between multiple accounts/VPC**

## Traffic Mirroring (VPC or inter VPC)
- Allows you to capture and inspect network traffic in your VPC
- Route the traffic to security appliances that you manage
- Capture the traffic
	- **From (Source)** – ENIs
	- **To (Targets)** – an ENI or a Network Load Balancer
- Capture all packets or capture the packets of your interest (optionally, truncate packets)
- Source and Target can be in the same VPC or different VPCs (VPC Peering)
- Use cases: content inspection, threat monitoring, troubleshooting, …

## IPv6
- IPv4 designed to provide 4.3 Billion addresses (they’ll be exhausted soon)
- IPv6 is the successor of IPv4, providing 3.4 × 10^38 unique IP addresses
- Every IPv6 address in AWS is public and Internet-routable (no private range)
- Example: ``::abcd::a000`` first 6 segments are zero

IPv6 in VPC
- **IPv4 cannot be disabled for your VPC and subnets**
- You can enable IPv6 (they’re public IP addresses) to operate in dual-stack mode
- Your EC2 instances will get **at least** a private internal IPv4 and a public IPv6
- They can communicate using either IPv4 or IPv6 to the internet through an Internet Gateway

IPv6 Troubleshooting
- if you cannot launch an EC2 instance in your subnet
	- It’s not because it cannot acquire an IPv6 (the space is very large)
	- It’s because there are no available IPv4 in your subnet
- **Solution**: create a new IPv4 CIDR in your subnet

HandsOn : add ipv6 cidr, don't forget auto-assign, SG, NACL.

## Egress-only Internet Gateway
- **Used for IPv6 only**
- (similar to a NAT Gateway but for IPv6)
- Allows instances in your VPC outbound connections over IPv6 while preventing the internet to initiate an IPv6 connection to your instances
- **You must update the Route Tables**
- With it, EC2 instances in Private subnet can initiate connections with Internet, but no for other direction
IPv6 routing
- Route table for public subnet -- CIDR range - local and others - IGW
- Route table for private subnet -- CIDR range - local; others ipv4 - IGW; others ipv6 - EIGW. 

## Networking Cost

## Network Firewall
- To protect network on AWS, we’ve seen
	- Network Access Control Lists (NACLs)
	- Amazon VPC security groups
	- AWS WAF (protect against malicious requests)
	- AWS Shield & AWS Shield Advanced
	- AWS Firewall Manager (to manage them across accounts)
- But what if we want to protect in a sophisticated way our entire VPC?
- Network Firewall Protect your entire Amazon VPC
- From Layer 3 to Layer 7 protection
- Any direction, you can inspect
	- VPC to VPC traffic
	- Inbound / Outbound to internet
	- To / from Direct Connect & Site-to-Site VPN
- Internally, the AWS Network Firewall uses the AWS Gateway Load Balancer
- Rules can be centrally managed crossaccount by AWS Firewall Manager to apply to many VPCs

Fine Grained Control
- Thousands of rules
- **Traffic filtering: Allow, drop, or alert for the traffic that matches the rules**
- **Active flow inspection** to protect against network threats with intrusion-prevention capabilities (like Gateway Load Balancer, but all managed by AWS)
- Send logs of rule matches to Amazon S3, CloudWatch Logs, Kinesis Data Firehose


# Section 28 Disaster Recovery
Recovery Point Objective and Recovery Time Objective
Backup and Restore -- Pilot Light -- Warm Standby -- Hot Site / Multi Site Approach

## DMS
Homogeneous or Heterogeneous
Must run on an EC2 instance
Change Data Capture
Schema Conversion Tool
Sources: On-Premises, Azure, Amazon RDS (including Aurora), S3, Document DB
Target: Sources + DDB, Redshift, OpenSearch, KDS, Apache Kafka, Neptune, Redis & Babelfish
Can be multi-AZ

MySQL Migration
- RDS to Aurora
	- Snapshots
	- Aurora Read Replica with lag 0, promote it
- External to Aurora
	- Percona XtraBackup
	- mysqldump
- DMS if both db are up and running

PostgreSQL Migration
- RDS to Aurora
	- Snapshots
	- Aurora Read Replica with lag 0, promote it
- External DB
	- backup and put it in S3, import it 
- DMS

## AWS Backup
Fully managed. 
Cross-Region, Cross-account.
On-Demand and Schedule
Backup Plans
Backup Vault Lock (WORM)


## Application Migration Service
Migrate on-premises data center to AWS
agentless or agent-based
Replacing SMS
Lift and Shift

## Transferring Large Dataset
S2S VPN
DX
Snowball
on-going replication: S2S VPN/DX with DMS or DataSync

## VMware Cloud


# Section 29 More SAA TODO
## Event Processing

## Caching Strategies

## Blocking IP address

## HPC

## EC2 HA

# Section 30 



