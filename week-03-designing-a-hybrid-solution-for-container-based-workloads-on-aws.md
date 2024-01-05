# Week 3 - Designing a hybrid solution for container based workloads on AWS

## Week 3 Introduction
- enterprise customer working in insurance business is migrating containers to AWS

## Customer #3: Use Case and Requirements
- half of contracts on existing on-premises data center provider expired
    - this half has to be moved to AWS
    - remaining half will stay on-premises for a couple more years
    - they have to communicate with each other
    - i.e. customer is looking for a __hybrid deployment__ with AWS
- customer requires lowest latency and consistent throughput in network between the 2 environments
- since apps are all internal, need to make sure the environments are not accessible via public internet
- on-premises resources will produces data which will need to be stored as files in AWS and accessed by apps in the AWS cloud
- customer currently uses container for all apps except DBs
    - plan to migrate a few PostgreSQL DBs to containers
    - but do not want to rewrite apps due to this migration - is there such a solution??
        - i.e. want a lift-and-shift for this part
- need to use same orchestration tools in both environments
- need high up-time (availability)

## Customer #3: Requirements Breakdown
- container services to be explored
- host PostreSQL DB on AWS
- same tools for container orchestration in 2 environments
- store data generated on-premises in AWS with minimal refactoring (needs further discussion with customer)
- optimize for resilience
    - needs to be fault-tolerant - llok out for single points of failure
- customer does not want to change app
    - more of lift-and-shift migration needed
    - luckily containers are portable in nature!
        - migration, especially lift-and-shift, should not be much of an issue

## Hybrid Networking and Connectivity Services
- when designing the solution, we need to think about "service blocks" to be placed on both on-premises side and AWS cloud side
- Since apps are not publicly accessible, we shall go in for larger private subnets
- __Candidates for network connectivity__ between on-prem and AWS
    - __public internet__
        - non-encrypted, no security
            - company has internal apps and is into insurance - need more security
        - throughput is at the mercy of the internet
            - company needs low latency for apps
        - rejected!
    - __AWS VPN__
        - 2 types of AWS VPNs
            - __site-to-site VPN__
                - connects on-prem to VPC
            - __client VPN__
                - connects users (like network admins, ops, devs etc.) to VPC
        - encrypted
            - good
        - throughput is at the mercy of the internet
            - bad - company needs low latency for apps
        - rejected!
    - __AWS Direct Connect__
        - private dedicated connection
            - traffic remains on AWS global network and never on public internet
            - secure
            - low latency
    - So...__AWS Direct Connect__ is the winner
- for simplicity we assume on AWS side
    - 1 account
    - 1 VPC
    - 1 Region
    - we may decide to change the design later if needed
    - If the customer needs to work with __multiple AWS accounts__, or __regions__ we need an __AWS Transit Gateway__

## Reading 3.1: Hybrid Networking and Connectivity
- __AWS Direct Connect__
    - consistent, dedicated throughput for the high volume of data that flows between the data center and AWS
    - shortest path to your AWS resources
        - while your network traffic is in transit, it remains on the AWS global network and never touches the public internet
        - low latency
    - you can choose
        - __hosted connection__
            - provided by an AWS Direct Connect Delivery Partner
        - __dedicated connection from AWS__
            - deploy at over 100 AWS Direct Connect locations around the globe
            - __AWS Direct Connect SiteLink__
                - send data between AWS Direct Connect locations to create private network connections between the offices and data centers in your global network
                - each dedicated AWS Direct Connect connection consists of
                    - a single dedicated connection between ports on your router and an AWS Direct Connect device
                    - we recommend establishing a second connection for redundancy
                    - when you request multiple ports at the same AWS Direct Connect location, they will be provisioned on redundant AWS equipment
        - if you have configured a __backup__ __Internet Protocol security (IPSec) VPN__ connection instead, __all VPC traffic will fail over to the VPN connection automatically__
            - traffic to or from public resources, such as S3, will be routed over the internet
        - If you __don't have a backup AWS Direct Connect link or an IPSec VPN link__, then __VPC traffic will be dropped if a failure occurs__
            - traffic to or from public resources, such as S3, will be routed over the internet
- __AWS Managed VPN__
    - __AWS Site-to-Site VPN__
        - by default, __instances that you launch into a VPC can't communicate with your own on-premises network__
        - enable access to your network from your VPC by creating AWS Site-to-Site VPN connection
        - Site-to-Site VPN supports __IPSec VPN connections__
        - __Virtual Private Gateway (VPG)__
            - the VPN concentrator on the Amazon side of the Site-to-Site VPN connection
                - attach it to the VPC where you want to create the Site-to-Site VPN connection
            - on the customer end, there is/are a _customer gateway device(s)_ (physical or software) owned and managed by the customer
                - attach it to the on-premises network
                - connects to the VPG over the IPSec VPN
            - consider taking this approach when you want to take advantage of an AWS managed VPN endpoint that includes automated redundancy and failover built into the AWS side of the VPN connection
                - supports and encourages multiple user gateway connections that implement redundancy and failover on your side of the VPN connection
    - __AWS Client VPN__
        - __fully managed__, remote-access VPN solution that your remote workforce can use to securely access resources within both AWS and your on-premises network
        - __fully elastic__
        - AWS Client VPN, including the software client, __supports the OpenVPN protocol__
        - __Use cases__
            - __Note__: Refer to diagrams from the course to understand these use cases
            - The configuration for this scenario includes a single target VPC. We recommend this configuration if you need to give clients access to the resources inside only a single VPC.
            - The configuration for this scenario includes access to only an on-premises network. We recommend this configuration if you need to give clients access to the resources inside only an on-premises network.
            - In the configuration for the scenario shown in the following diagram, clients can access a single VPC, and clients can also route traffic to each other. We recommend this configuration if the clients that connect to the same client VPN endpoint also need to communicate with each other. Clients can communicate with each other by using the unique IP address that's assigned to them from the client Classless Inter-Domain Routing (CIDR) range when they connect to the client VPN endpoint.
- __AWS Transit Gateway__
    - if multiple regions or AWS accounts were all connected to a remote data center by using AWS Direct Connect, it can be used
    - data is automatically encrypted and never travels over the public internet
    - __connects your VPCs and on-premises networks through a central hub__
        - simplifies your network and minimizes complex peering relationships
    - acts as a cloud router
        — each new connection is made only once
    - inter-region peering connects transit gateways together through the AWS global network
        - __Note__: Refer to diagrams from the course to understand these use scenarios
        - __Without AWS Transit Gateway__
            - The following diagram shows what a network solution might look like without the use of AWS Transit Gateway. Notice that there are many different connection points between the different components. This network configuration is complex, and it can be difficult to manage.
        - __With AWS Transit Gateway__
            - In contrast, the following diagram shows what a network solution might look like with the use of AWS Transit Gateway. Transit Gateway is the hub for the connections between the different networks. Using a transit gateway can make the network easier to manage. You can apply route tables so that the transit gateway can connect networks to each other in a more centralized manner.
            - The following example shows what using AWS Transit Gateway with AWS Direct Connect could look like for a solution that has a remote network and three AWS Regions. Each Region has multiple VPCs.

## Running Containers on AWS
- container orchestration tools
    - help create, scale up, delete containers, and manage them in general
- AWS provides 2 services for container orchestration
    - ECS
    - EKS
- both services can be run in unmanaged (on EC2 instances) and fully-managed way (Fargate)
- EKS
    - uses Kubernetes (K8s)
        - K8s has a vibrant community
        - consisten open-source APIs
        - flexibility
        - uses K8s APIs
    - steep learning curve
        - good choice if already using K8s on-prem
        - not very convenient, but provides great control
- ECS
    - simple but opinionated (when it comes to network and security configurations, say)
        - for example you don't need to figure out which type of ELBs you need to connect to in ECS - it decides
    - designed to be convenient, while trading off control
    - deep integrations to AWS services such as VPC, load balancing, service discovery, and IAM roles
    - uses AWS (ECS) APIs
- Again for choice of compute (EC2/Fargate) you can consider convenience vs. control
    - eg. you do not get access to the underlying instances in Fargate
-- So, which ones to use?
    - ask the customer!
    - __customer says they have huge number of container but aren't doing any complicated with them__
    - __Their team tried learning K8s in the past but it was overwhelming__
    - __They would like to SSH into the servers__
    - A simpler solution will suffice!
- So...ECS with EC2 is the winner
    - scalable but simple!
    - can access underlying compute infrastructure through SSH
    - choose ECS service
    - choose EC2 instances to be in a private subnet
        - without internet connectivity - neither ingress nor egress
        - but __EC2 instances in the private subnet may need to download from the internet__ (especially when they spin up and install software)
            - add a __Network Address Translation (NAT)__ in a __public subnet in the same VPC__
                - __Two options for NAT__
                    - __NAT instance__
                        - unmanaged
                        - NAT software installed on an EC2 instance
                        - performance and throughput based off of underlying EC2 instance size and type
                        - you also need to host multiple NAT instances in each AZ, and also manage failover via scripts
                        - good for someone with smaller workload as it can reduce costs (if managed well)
                    - __NAT gateway__
                        - managed and massively scalable
                        - a serverless NAT service
                        - good choice for enterprise which has large worloads
            - we __choose NAT gateway__ over NAT instance in this case

## Reading 3.2: Running Containers on AWS and NAT Gateways
- requirement is containers
    - running internal apps
    - don't require inbound communication from the internet
    - require outbound communication to the internet
    - need own custom Amazon Machine Image (AMI) for the cluster that hosts the containers
    - also have SSH access to underlying instances
- hence choice is __ECS as the container orchestration tool__ and __EC2 as the launch type__
- include a NAT gateway so that private instances could download from the internet

### Amazon ECS launch types
- __EC2__
    - deploy and manage your own cluster of EC2 instances (you register those instance to your ECS cluster)
    - an ECS agent is installed on each EC2 instance
        - enables the orchestration tool that ECS uses to manage nodes
- __Fargate__
    - run containers directly, without self-provisioned EC2 instances
- EC2 launch type is suitable as customer wants to use
    - a custom AMI
    - SSH to access underlying instances
- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_types.html

### NAT devices
- a NAT device allow resources in private subnets to connect to
    - the internet
    - other VPCs
    - on-premise networks
- can communicate with services outside the VPC, but they can't receive unsolicited connection requests
    - __NAT device replaces the source IPv4 address of the instances with the address of the NAT device__
    - __sends response traffic to the instances__, and __translates the addresses back to the original source IPv4 addresses__
- Two options
    - __NAT gateway__
        - managed NAT device offered by AWS
    - __NAT instance__
        - your own NAT device on an EC2 instance
    - recommended is NAT gateways because they
        - are managed
            - provide better availability and bandwidth
            - automatically scalable
            - help in administration
- __manage how the traffic flows from the private resources to the NAT device__ by using __route tables__
- __Connectivity types__
    - when you create a NAT gateway, you specify one of the following connectivity types
    - __Public (default)__
        - instances in private subnets can connect to the internet through a public NAT gateway
        - they can't receive unsolicited inbound connections from the internet
            - __you create a public NAT gateway in a public subnet__
            - you __must associate an elastic IP address with the NAT gateway__ at creation
            - you route traffic from the NAT gateway to the internet gateway for the VPC
                - alternatively, you can use a public NAT gateway to connect to other VPCs or your on-premises network
                - here, you route traffic from the NAT gateway through a transit gateway or a virtual private gateway
    - __Private__
        - instances in private subnets can connect to other VPCs or your on-premises network through a private NAT gateway
        - you can route traffic from the NAT gateway through a transit gateway or a virtual private gateway
        - __you can't associate an elastic IP address with a private NAT gateway__
        - you can attach an internet gateway to a VPC with a private NAT gateway, but if you route traffic from the private NAT gateway to the internet gateway, the internet gateway drops the traffic!
- The NAT gateway replaces the source IP address of the instances with the IP address of the NAT gateway
    - for a public NAT gateway, this is the elastic IP address of the NAT gateway
    - for a private NAT gateway, this is the private IP address of the NAT gateway
- https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html

## Amazon Relational Database Service (RDS)
- RDS has __Multi-AZ deployment__
    - one is a __primary instance__, and the other a __secondary instance__
        - primary is the only writer
    - synchronous replication for non-Aurora RDBMS
    - asynchronous replication for Aurora
    - apps connect via a DNS, and in a failover RDS manages the DNS record changes
- RDS supports __Read Replicas__
    - asynchronous replication
    - business application can use the primary instance, and BI applications can use the Read Replicas, say
    - __Cross-Region Read Replicas__ also supported
- in this case, customer needs to migrate DB
- AWS __Database Migration Service (AWS DMS)__ can be used
    - can migrate without downtime
    - you can __set on-prem DB as source and RDS DB as target__
        - now, the source data changes on-prem, it is continuously replicated to the target DB in AWS
        - you can switchover to the target DB whenever convenient
    - you have
        - __Homogeneous DB migrations__
            - eg. SQL DB to another SQL DB
        - __Heterogeneous DB migrations__
            - eg. SQL DB to NoSQL DB
            - aided by AWS Schema conversion tool
    - DMS can also be used to
        - keep development and test DBs in-sync with production
        - consolidate data from multiple DBs into one
        
## Amazon RDS Exploration
- Demo of RDS DB creation
    - while creating it you can enable auto-scaling for DB
    - __Performance Insights__
        - an advanced DB performance monitoring feature that diagnose and helps solve performance issues

## Reading 3.3: Amazon RDS

### Multi-AZ deployments
- Multi-AZ deployment
    - high availability
    - automatic failover
    - 2 AZs are used by default - 1 primary and 1 standby
        - for even higher availability, deploy 2 standby DB instances (total 3 AZs)
        - primary is the only writer, standbys are readers
        - automatic failovers typically happen in < 35 seconds
        - transaction-commit latency up to 2 times faster when compared to with with only 1 standby
        - also gain additional read capacity, and a choice of AWS Graviton2–based or Intel–based instances for compute

### Read replicas
- increases aggregate read throughput
- enhanced performance and durability
- for __read-heavy DB workloads__ this makes it easier to elastically scale out beyond the capacity constraints of a single DB instance
- highly available
- uses the engine's native __asynchronous replication__ to update the replicas when source changes
    - RDS replicates all DBs in the source DB instance
- read replica allows only read-only connections
- read replica can also be promoted to become standalone DB instance, when needed
- eg. say that you arere running reports on your DB, which is causing performance issues with CPU-intensive reads
    - create read replica and direct all the reporting queries to that

### Scaling Amazon RDS instances
- __Scale your instance up vertically__
    - When you create an RDS DB instance, you choose a database instance type and size
        - CPU, memory, storage, and networking capacity
    - Not every instance type is supported for every DB engine, version, edition or region
    - you can vertically scale the instance and choose a larger instance size
        - do this when need __more CPU and storage ability for an instance__
- __Use read replicas__
    - if you need __more CPU capabilities but do not need more storage__
- __Enable RDS Storage Auto Scaling__
    - If you __need more storage, but do not need more CPU__, then you could __scale the storage horizontally__
    - allocate more storage volumes manually, or by enabling __RDS Storage Auto Scaling__
        - when auto-scaling there is virtually zero downtime
            - you need to set your desired maximum storage limit
            - continuously monitors actual storage consumption, and scales when actual utilization approaches provisioned capacity
            - works with new and existing DB instances
- __Change the storage type for increased performance__
    - using __Provisioned IOPS instead of General Purpose__ could improve performance
    - __3 storage types__
        - __General Purpose SSD__
            - good for general workloads
            - single-digit millisecond latencies
            - ability to burst to 3,000 IOPS for extended periods of time
        - __Provisioned IOPS__
            - meet needs of __I/O-intensive workloads__
            — low I/O latency and consistent I/O throughput
        - __Magnetic__
            - for backward compatibility
            - __Note__: __NOT__ recommended usually

### AWS DMS
- helps migrate DBs to AWS quickly and securely
- At a basic level, AWS DMS is a server in the AWS Cloud that runs and replicates from source DB to target DB
- source DB can be
    - on-premises outside of AWS
    - on an EC2 instance
    - on RDS
- target DB can be
    - on an EC2 instance
    - on RDS
- __AWS Schema Conversion Tool (AWS SCT)__
    - to _create some or all of the target_ __tables__, __indexes__, __views__, __triggers__ etc.

## Where Should Our Customer Store Their Data?
- __Candidates__
    - S3
    - EFS
    - FSx
    - EBS

## Resources
- [Network-to-Amazon VPC connectivity options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html)
- [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)
- [AWS Direct Connect FAQs](https://aws.amazon.com/directconnect/faqs/)
- [What is Amazon Site-to-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)
- [What is AWS Client VPN?](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/what-is.html)
- [What is a transit gateway?](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html)
- [Containers at AWS](https://aws.amazon.com/containers/)
- [Customers FAQs on the AWS Containers site](https://aws.amazon.com/containers/faqs/)
- [Amazon ECS launch types](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_types.html)
- [Connect to the internet or other networks using NAT devices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat.html)
- [Compare NAT gateways and NAT instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html)
- [AWS Database Migration Service](https://aws.amazon.com/dms/)
- [Amazon RDS Multi-AZ](https://aws.amazon.com/rds/features/multi-az/)
- [Amazon RDS Read Replicas](https://aws.amazon.com/rds/features/read-replicas/)
- [Amazon RDS DB instances](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.html)
- [What is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()