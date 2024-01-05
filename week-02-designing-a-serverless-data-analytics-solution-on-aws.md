# Week 2 - Designing a serverless data analytics solution on AWS

## Week 2 Introduction
Tells something about the customer and their product
    - restaurant product
    - need help on data ingestion, processing and visualization

## Customer #2: Use Case and Requirements
- customer call to understand current architecture and requirements
- company that provide online menus at restaurants using QR codes
- currently S3 static website with restaurant menus
- admin has an interface is used to login and edit the menu stored in S3
- system in place that displays a QR code pointing to the bucket, so restaurants can add the QR code at their tables
- new requirement - to add an "Order this item" option to the menu
    - need data analytics on how customers browse the menu - what parts do they stop and view, which ones do they skip
    - can help in restructuring the menu
    - there is already a library the customer created and shall be used to send clickstream data (so it is out-of-scope for this use case)
- such analytics are called __clickstream data analytics__
    - clickstream data - data collected by systems (mostly frontends) regarding user interactions with that system
    - can help in giving suggestions like "You may like this item from Restaurant B"
- prefer services where billing is for usage, and not time
- prefer managed services
- prefer data backed up with encryption in another AWS region

## Customer #2: Requirements Breakdown
- solution needs to provide
    - HTTPS endpoint for clickstream data ingestion (collection)
    - managed services
        - API Gateway seems promising
    - prefer billing on usge
        - avoid EC2 and use serverless
    - cross-region backup
        - managed services already provide that

## An Overview of Data Analytics on AWS
- getting insight into data can sometimes
    - need simple techniques
    - be complex and need ML techniques
- first step is to know how data shall come into the system
    - data can come from different sources, devices and be of different types (audio, log files, pictures etc.)
- we need to find the right tool for the job
    - performs best, saves on costs
- AWS provides services to transform data while it is being ingested, or after it has arrived
    - in this use case, it is simple as customer is already prepared with what data to send
    - so no data transformation is required
    - we can start analyzing as is
- Data Analytics Services
    - __Amazon Athena__
        - interactive analytics from data stored in S3
        - uses SQL!
        - serverless
        - pay only for queries you run
    - __Amazon EMR__, __AWS Glue__
        - useful for further data transformation
        - big data processing
        - run and manage Hadoop clusters and jobs
        - massive parallel processing of distributed data
        - uses open-source analytics frameworks like Apache Spark, Apache Hive, Presto etc.
- Questions that can help decide which solution to use (in general, and not this use case in particular)
    - what latency is acceptable between ingestion and making it available
    - can customer wait some time to get the data
    - where is the data coming from
        - database migration
        - clickstream
        - batch file upload
    - sometimes customers want to use tools they have licenses for already, or solutions they have created
    - does customer want to use federated access using existing identity providers

## Reading 2.1: A Look into AWS Data Services
- AWS has services for
    - Data
        - movement
        - storage
        - lakes
    - Analytics
        - big data
        - log
        - stream
    - Business Intelligence (BI)
    - Machine Learning (ML)
- services are
    - purpose-built
    - scalability
    - provide among the best price-performance

### Data lakes and data storage
- __Amazon S3__
    - use cases such as __data lakes, cloud-native applications, and mobile apps__
    - __build a data lake__
        - run big data analytics, AI/ML, and high performance computing (HPC) apps to unlock data insights
    - __backup and restore critical data__
        - meet Recovery Time Objectives (RTO), Recovery Point Objectives (RPO), and compliance requirements with the robust replication features of Amazon S3
- __Amazon S3 Glacier__
    - purpose-built for data archiving at low cost, high availability and unlimited scalability
- __AWS Lake Formation__
    - set up a secure data lake in days
    - data lake
        - is a centralized, curated, and secured repository
        - stores all your data both in its original form and prepared for analysis
    - breaks down data silos and combines different types of analytics to gain insights and guide better business decisions

### Data analytics
- __Amazon Athena__
    - interactive query service to analyze data in S3 by using SQL
        - serverless
        - pay only for the queries you run
    - Using Athena is straightforward
        - point to your data in S3
        - define the schema
        - __start querying using SQL__
            - __no complex ETL jobs to prepare your data for analysis!__
            - __no special skills needed except SQL__
    - most results are delivered within seconds
- __Amazon EMR__
    - a __big data solution__ for petabyte-scale data processing, interactive analytics, and ML that use open-source frameworks, such as __Apache Spark__, __Apache Hive__, and __Presto__
    - in this use case customer is short-staffed, and EMR __requires a learning curve to operate the open-source frameworks that it uses__
        - EMR would a good candidate for the task if the customer already had big data knowledge
    - __use cases__
        - run large-scale data processing and __what-if analysis__ by using __statistical algorithms__ and __predictive models to uncover hidden patterns, correlations, market trends, and customer preferences__
        - extract data from various sources, process it at scale, and make the data available for apps and users
        - analyze events from streaming data sources in real time to create long-running, highly available, and fault-tolerant streaming data pipelines
        - analyze data using open-source ML frameworks, such as __Apache Spark MLlib__, __TensorFlow__, and __Apache MXNet__
        - connect to __Amazon SageMaker Studio__ for large-scale model training, analysis, and reporting
- __Amazon OpenSearch Service__
    - perform __interactive log analytics__, __real-time application monitoring__, __website search__, and more
    - an open source, distributed search and analytics suite that is __derived from Elasticsearch__
        - successor to Amazon Elasticsearch Service
    - __visualization capabilities__ that are __powered by OpenSearch Dashboards and Kibana__

### Data movement
- __Amazon Kinesis__
    - __collect, process, and analyze real-time__, streaming data at any scale so that you can get timely insights and __react quickly to new information__
    — no need to wait for all your data to be collected before processing can begin
    - ingest video, audio, logs, clickstreams, and IoT telemetry data for ML, analytics, and other apps
    - flexibility to choose tools that best suit the requirements of your app
- __AWS Glue__
    - serverless
    -__data integration service__
        - you can use to discover, prepare, and combine data for analytics, ML, and app development
        - you can start analyzing your data in minutes instead of months
        - data integration involves multiple tasks
            - discovering and extracting data from various sources
            - enriching
            - cleaning
            - normalizing
            - combining data
            - loading and organizing data in DBs, data warehouses, and data lakes
        - these tasks are often handled by different types of users who each use different products
- __AWS Database Migration Service (DMS)__
    - helps migrate DBs (from popular open-source and commercials DBMSes) to AWS quickly and securely
    - source database remains fully operational during the migration, minimizing downtime

### Predictive analytics and Machine Learning
- __Amazon SageMaker__
    - generic ML solution
    - build, train, and deploy ML models for any use case
    - fully managed infrastructure, tools, and workflows.
    - choice of tools
        — IDEs for data scientists
        - no-code interfaces for business analysts
- __Amazon Rekognition__
    - serverless
    - __automate image and video analysis__ by adding __pretrained or customizable computer vision API operations__ to your apps
        -  __no need to build ML models and infrastructure from scratch!__
    - is __NOT__ a data analytics service
        - you can use it as __part of a data analytics solution__
    - designed to analyze millions of images, streaming, and stored videos within 3 seconds
        - eg. detect objects (such as packages, pets, or people) in real time from live video streams
        - eg. detect faces that appear in images and videos, and recognizing attributes (such as open eyes, glasses, and facial hair) for each face
- __Amazon Comprehend__
    - __Natural-Language Processing (NLP) service__ that __uses ML__ to uncover valuable insights and connections in text, which is instrumental for a data analytics solution
        - eg. you could mine business and call center analytics or process financial documents
    - __Amazon Comprehend Medical__ focuses on extracting information accurately and quickly from unstructured medical text

## Why Amazon S3 for Storage?
- need to store clickstream data
    - but where? - S3, EBS, EFS, RDS, DynamoDB?
    - for data analytics we usually need a storage that can live by itself
        - i.e. not have requirement to be attached to EC2 etc.
- __EBS__
    - needs to be attached to an EC2 instance
    - billing isn't as granular (as S3 for eg.) - depends on allocated space, not how much is used
    - not as durable
    - ruled out
- __EFS__
    - higher durability than EBS
    - filesystem that needs to be attached to EC2, containers etc.
    - ruled out
- So...__S3__ is the winner
    - durable
    - available
    - standalone (__we will be able to separate storage from data processing which is needed for analytics tools etc.__)
        - __other AWS services and comercial tools support S3__ :-)
    - unlimited scalability
        - charges for usage and requests
    - __supports encryption in transit and at rest__
    - __intelligent tiering__ helps save costs as well (data moved across tiers based on age and historical usage)
    - __S3 is a popular choice as a data lake__
- __Question__: Why not a DBMS? Especially since API Gateway can store data in HTTP requests directly to these! Need to find out why...

## Exploring Amazon S3
Nothing much - demonstrated how to host a static website

## Reading 2.2: Amazon S3 Cross-Region Replication and Object Lifecycle

### S3 Cross-Region Replication (CRR)
- you can automatically replicate objects, and their metadata and object tags, into other AWS Regions for
    - reduced latency
        - minimize latency by maintaining multiple object copies in AWS Regions that are geographically closer to your customers
    - regional efficiency
        - if you have compute clusters in 2+ regions that analyze the same set of objects, you can maintain copies in those regions
    - compliance
        - Amazon S3 stores your data across multiple AZs by default. However, compliance requirements might require you to store data at even greater distances
    - security
    - disaster recovery
- replicate from a single, source S3 bucket to one or more destination buckets in another AWS Region
- you can set up replication at
    - bucket level
    - object level (by using Amazon S3 object tags)
    - shared prefix level
- you can change account ownership for the replicated objects to protect data from accidental deletion

### S3 Lifecycle and Storage Classes
- add rules in an S3 Lifecycle configuration to transition objects to another Amazon S3 storage class
- use cases
    - infrequently accessed objects may be transitioned to the S3 Standard-IA storage class
    - archive objects that you don't need to access in real time to the S3 Glacier Flexible Retrieval storage class
- use __S3 Intelligent-Tiering storage class__ if you are not sure about the data access patterns
    - automatically moves data to the most cost-effective access tier when access patterns change
    - for data with unknown, changing, or unpredictable access patterns
    - is the only cloud storage class that's designed to deliver automatic storage cost savings when data access patterns change, __without performance impact or operational overhead__
    - there is a small monthly object monitoring and automation charge
        — independent of object size or retention period
            - no minimum eligible object size, but objects < 128 KB are not eligible for automatic tiering
            - these can be stored, but they will be charged at the Frequent Access tier rates, and don't incur the monitoring and automation charge.
        - __you can use S3 Intelligent-Tiering as the default storage class for__ virtually any workload, especially
            - data lakes
            - data analytics
            - new applications
            - user-generated content
- __Amazon S3 Glacier storage classes__
    - purpose-built for lost-cost data archiving
    - high availability and unlimited scalability
    - options for fast/slow access to your archive data
        - __Glacier Instant Retrieval storage__
            - for long-lived data that is accessed once per quarter and requires millisecond retrieval
            - millisecond retrieval for archives that need immediate access, such as medical images or news media assets
            - rarely accessed data that still needs immediate access in performance-sensitive use cases like image hosting, online file-sharing applications, medical imaging and health records, news media assets, and satellite and aerial imaging
        - __Glacier Flexible Retrieval__
            - for archive data that is accessed 1 - 2 times per year and is retrieved asynchronously
            - provides three retrieval options
                - expedited retrievals that typically complete in 1 – 5 minutes
                - standard retrievals that typically complete in 3 – 5 hours and start within minutes when initiated using S3 Batch Operations
                - free bulk retrievals that return large amounts of data typically in 5 – 12 hours
            - backup, disaster recovery, offsite data storage needs, and for when some data needs to occasionally retrieved in minutes, and you don't want to worry about costs
        - __Glacier Deep Archive__
            - for long-lived archive data that is accessed < 1 time per year and is retrieved asynchronously
            - provides two retrieval options ranging from 12 - 48 hours
            - usually for data maintained for compliance purposes in finance, healthcare sectors

## Choosing a Service for Data Ingestion
- How are we going to bring data to S3? Answer depends on if you want to
    - bring data in batches
    - bring data from IoT
    - migrate large amounts of data offline
- clickstream data for restaurant customers can help in real-time to
    - update recommendations
    - perform advanced A/B testing
    - send push notifications based on session length etc.
- __candidates are__
    - __Amazon EMR__
        - managed Hadoop cluster
        - install a streaming framework - eg. Spark Streaming
        - but service charges __per time__ and __needs big data knowledge__
            - rejected!
    - __AWS DMS__
        - this is for migrating DBs without downtime for apps
        - we are not using DBs - just clickstream data to be sent
            - rejected!
    - __AWS Data Exchange__
        - data analytics service that provides data catalogs and especially suited for ingesting data from third-party data providers into data lakes
            - rejected!
    - __Kinesis is a family of data services__ - you can use one of them or a combination of these for your purposes
        - __Kinesis Data Analytics__
            - used to perform analysis
            - used for data transformation, aggregation, cleaning
            - we don't need these operations in our use case
                - rejected!
        - __Kinesis Data Streams__
            - similar to __Kinesis Data Firehose__
            - __however requires some code to be written at both producer and consumer end__
            - advantage is there is __lesser latency from the moment data is generated, to the moment it is available for analysis__
        - __Kinesis Data Firehose__
            - __no need for additional code__
            - some latency (say ~2 minutes)
- So, which one to use?
    - ask the customer!
    - __customer says there are no real-time dashboards and hence some latency is ok__!
- So...Kinesis Data Firehose is the winner
    - no additional code!
- Next someone needs to make the __Kinesis API calls (like PUT record)__. __Candidates are__
    - __Amazon Cognito__
        - authentication services
        - provides identity pool
            - use AWS credentials to perform Kinesis API calls
            - but we would end up exposing the Kinesis API calls (which is ok but not ideal)
            - also customer has proposed requirement of making HTTP/HTTPS calls to ingest clickstream data
                - rejected!
    - __API Gateway__
        - can receive requests and make Kinesis API calls
        - winner!

## Exploring Amazon Kinesis
- Demonstration on using Amazon Kinesis Data Firehose __delivery stream__
    - __source__
        - Kinesis data stream
        - Direct PUT
    - __destination__
        - many including S3
        - for S3, choose bucket
            - __prefix__ tells what prefix to use when naming the files
    - you can also
        - enable CloudWatch logging
        - enable Lambdas to transform data

## Reading 2.3: Differences between Amazon Kinesis Services
- __Amazon Kinesis Family__
    - makes it easier to collect, process, and analyze real-time, streaming data so that you can get timely insights and react quickly to new information
    - cost-effective
    - scalable
    - flexibility to choose the tools that best suit your requirements
    - ingest real-time data such as video, audio, logs, clickstreams, and IoT, telemetry data for ML, analytics
    - analyze and respond in real-time
- __Amazon Kinesis Data Streams__
    - designed to continuously capture GBs / second of data from thousands of sources
        - clickstreams
        - DB event streams
        - financial transactions
        - social media feeds
        - IT logs
        - location-tracking events
    - extremely low latency
        - data collected is available in milliseconds
    - __use cases__
        - build real-time analytics dashboards
        - real-time anomaly detection
        - dynamic pricing
- __Amazon Kinesis Data Firehose__
    - reliably load streaming data into data lakes, data stores, and analytics services
    - can capture, transform, and deliver streaming data to
        - S3
        - Redshift
        - OpenSearch
        - generic HTTP endpoints
        - service providers like Datadog, New Relic, MongoDB, and Splunk
    - fully managed
    - can batch, compress, transform, and encrypt your data streams before loading
        - minimizes the amount of storage
        - increases security
    - easier to operate when compared with Amazon Kinesis Data Streams
    - however has higher latency (in minutes)
- __Amazon Kinesis Data Analytics__
    - transform and analyze streaming data in real time with __Apache Flink__
        - Apache Flink is an open-source framework and engine for processing data streams
        - reduces the complexity of building, managing, and integrating Apache Flink apps with other AWS services
        - takes care of everything that's required to run streaming apps continuously
- __Amazon Kinesis Video Streams__
    - securely stream video from connected devices to AWS for analytics, ML, playback etc.
    - automatically provisions and elastically scales the infrastructure that's needed to ingest streaming video data from millions of devices
    - durably store, encrypt, and index video data in your streams, and you can access your data through the Kinesis Video Streams APIs.
    - playback video for live and on-demand viewing
    - build applications using computer vision and video analytics through integration with Amazon Rekognition Video and libraries for ML frameworks (such as Apache MXNet, TensorFlow, and OpenCV)
    - supports WebRTC, an open-source project that uses APIs to facilitate real-time media streaming and interaction between web browsers, mobile applications, and connected devices
        - video chat and peer-to-peer media streaming

## Accessing the Ingested Data
- we want a tool to process large amount of clickstream data that resides in small files on S3
- __candidates__
    - __Amazon S3 Select__
        - an S3 feature
        - managed and serverless
        - SQL queries are made to filter contents of S3 objects
        - can query only 1 file per query though
            - however our app is likely to produce multiple objects
            - also we are not doing anything to aggregate the data (into a single S3 object say)
        - rejected!
    - __AWS Glue__
        - shines when processing unstructured data using big data frameworks
        - learning curve and big data knowledge
        - rejected!
    - __Amazon EMR__
        - shines when processing unstructured data using big data frameworks
        - learning curve and big data knowledge
        - rejected!
    - __Amazon Athena__
        - interactive query service
        - managed - just what customer wants
        - pay only for queries you run - cost-effective
        - use SQL to run queries - not much of a learning curve
            - you can perform joins between tables defined using objects in __different S3 buckets__
            - each object can use a different format
                - JSON, CSV, Apache Parquet (FOSS column-oriented data storage format in the Apache Hadoop)
                - custom formats can be supports but they require writing SerDe (Serializer-Deserializer)
                    - this would in turn require usage of compute!
        - So, is data being ingested in non-standard format
            - ask the customer!
            - customer says is it JSON!
        - So...no need for any additional service for SerDe

## Exploring Amazon Athena
- Demo on using Athena

## Visualizing the Data
- __Candidates__
    - __Amazon CloudWatch__
        - offers dashboards
        - shows real-time metrics
        - is primarily for monitoring operational metrics
            - CPU utilization for EC2 instances, number of objects in an S3 bucket
        - not intended for data analytics
        - rejected!
    - __Amazon OpenSearch Service__
        - FOSS suite
        - it comes with storage and processing power embedded which probably is unnecessary since we use S3 and Athena
            - cost of the solution will be higher (than Athena + specialized data visualization colution)
            - we only want data visualization capability extra
            - rejected!
    - __Amazon Managed Grafana__
        - open-source tool for analytics and visualization
        - shines when we want to visualize time-series data
            - primarily for monitoring...
            - ...but also suitable for BI and data analytics
    - __Amazon QuickSight__
        - visualization service, that integrates with Athena, with built-in security
        - managed
        - pay as you use
    - both Grafana and QuickSight can work, but to reduce licensing costs and learning curve, we can check with customer if they are already using one of them in any of their projects
    - So, is one of Grafana / QuickSight being used in the organization?
        - ask the customer!
        - customer says QuickSight is!
    - So...QuickSight is the winner

## Reading 2.4: Amazon QuickSight Features
- better understand data by asking questions in natural language and exploring interactive dashboards
- can automatically look for patterns and outliers (an ML feature)
- __connect and scale all your data__
    - data can be in AWS, in third-party cloud service providers, or on-premises
    - use SPICE in-memory storage to scale data exploration to thousands of users
    - combine data from multiple sources and create complex data models for governed data sharing
- __build customizable dashboards__
    - create customized, use-case-specific dashboards
    - deliver personalized email reports and alerts to end users
    - access information from anywhere - QuickSight web / mobile app
- __ML integrations for insights__
    - Anomaly Detection to continuously analyze all your data for anomalies / variations
    - forecast business metrics and perform interactive what-if analyses
    - customize Auto-Narratives and weave them into dashboards to provide deeper context for users
- __self-service BI for everyone__
    - dive deep into data through simple questions, without BI training
    - create visual analyses of data by using a web-based authoring interface
    - embed QuickSight capabilities in apps for data-driven user experiences
- __native integration with AWS__
    - use Private VPC connectivity for secure AWS access to Redshift, Snowflake, Exasol, RDS etc.
    - use IAM permissions for S3 and Athena, with fine-grained access control for serverless data exploration
    - use SageMaker integration to incorporate sophisticated ML models without complex data pipelines
- __fully managed, and pay by usage__
    - automatically scale serverless architecture to hundreds of thousands of users with high availability, with no need to overprovision for peak usage
    - consistent, fast response times for end users and analysts through the auto-scaling capabilities of the SPICE in-memory engine, with no need to scale DBs for high workloads
    - optimize costs through the pay-per-session model by paying only for actual usage, with no need to buy thousands of end-user licenses for large-scale BI or embedded analytics
- __built-in security, governance, and compliance__
    - use end-to-end data encryption for data, and use encryption at rest for data in SPICE
    - use row-level and column-level security with API support for control at the user or group level
    - rapid deployment for regulated workloads with compliance support for the HIPAA, HITRUST CSF, GDPR, SOC, PCI, ISO 27001, FedRAMP High etc.

## Consider this Scenario
You are a solutions architect who is working with a customer to design solutions on AWS to meet their business needs. Your customer wants to create a solution for ingesting data into a data lake on AWS. This data lake must be scalable and highly available. It must also be able to store both structured and unstructured data in the same storage location. After the data is stored, it will need to be transformed into different formats so that it can be used for analytics and to derive insights. The customer wants to store raw data without any modification, and they also want to store processed data in a separate location.

Write a few paragraphs that describe which AWS services you might use for this architecture. Define the scope of each service, and explain how they work together to form a solution to solve the customer's business need.

## Customer #2: Solution Overview
- customer wondering what would happen if data ingested was not formatted properly (invalid JSOn for example)
    - so fa not handled (incoming data is stored in S3 as sent from frontend)
    - two ways to handle it (needs additions to existing architecture)
        - Kinesis Firehose has data manipulation mechanisms
        - __S3 Bucket Actions__  lets you run Lambda functions in response to bucket events like when a new object (file) gets added
            - do data validation using this
            - also, you can keep one bucket with raw data, and one with processed data 
            - you can also __enrich the data__ by having the Lambda function connect to other data sources like RDS, DynamoDB etc.

## Week Wrap-Up: Taking this Architecture to the Next Level
- discussion on the out-of-scope parts of the architecture (system already in place in customer's app)
    - system that allows adming to update menu
        - change from EC2 to a managed (serverless) architecture
    - instead of serving website directly from S3, serve it through a CloudFront distribution
    - remove API Gateway and instead have Amazon Cognito
        - Cognito provides identity pools for auuthorization
            - you can set up authorization workflows with Cognito
            - once authenticated, you can send data directly to Kinesis Firehose
            - this will eliminate API Gateway and save costs
        - but this would require code updates to the JS library that send clickstream data
            - you can use the AWS JS SDK to do this in the library
                - the JS SDK also has retry logic withe xponential backoff built-in
    - create a CloudFormation template of the entire architecture
        - __Infrastructure as code (IaC)__
        - helps recreate the entire system in a different AWS account
        - what could be the point of doing so - you can for eg. set up the infrastructure exclusively for a restaurant chain if they need it
        - reduces manual mistakes when operating with the infrastructure
    - change content in bucket to be conducive to Athena and improve interactive analytics performance
        - partition the content
        - change to columnar data store by having data format in Apache Parquet

## Reading 2.5: Architecture Optimizations for Week 2
- __Serverless on AWS__
    - changing the system that generates QR codes into something serverless, or a container
- __Amazon CloudFront__
    - the request is routed to the edge location that provides the lowest latency
    - allows custom SSL certificates (issued by AWS Certificate Manager, or ACM), custom domain names, and DDoS protection that is powered by AWS WAF and AWS Shield
- __Amazon Cognito__
    - add user sign up, sign in, and access control to your web applications and mobile apps
    - scales to millions of users
    - supports sign in with social identity providers, such as Apple, Facebook, Google, and Amazon, and enterprise identity providers through SAML 2.0 and OpenID Connect
    - with Amazon Cognito, you can have a __mobile JavaScript library that assumes a role with a web identity__
        - the role has a strict policy associated with it - eg. it allows only the PutRecord API operation on a specific Kinesis data stream
- __Amazon CloudFormation and IaC__
    - helps you model and set up your AWS resources
    - provisions and configures resources based on the CloudFormation template you specify
    - Example
        - this YAML template snippet creates an S3 bucket with the following configurations
            - Static website hosting that uses index.html as the index document and error.html as the error document
            - Bucket policy that allows everyone to GET objects (otherwise, the bucket is private)
            - Deletion policy that asks CloudFormation to retain the bucket if the CloudFormation stack is deleted
            - __The snippet also outputs the https:// URL that's used by static website hosting__
```yml
AWSTemplateFormatVersion: 2010-09-09
Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
    DeletionPolicy: Retain
  BucketPolicy:
    Type: 'AWS::S3::BucketPolicy'
    Properties:
      PolicyDocument:
        Id: MyPolicy
        Version: 2012-10-17
        Statement:
          - Sid: PublicReadForGetBucketObjects
            Effect: Allow
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Join 
              - ''
              - - 'arn:aws:s3:::'
                - !Ref S3Bucket
                - /*
      Bucket: !Ref S3Bucket
Outputs:
  WebsiteURL:
    Value: !GetAtt 
      - S3Bucket
      - WebsiteURL
    Description: URL for website hosted on S3
  S3BucketSecureURL:
    Value: !Join 
      - ''
      - - 'https://'
        - !GetAtt 
          - S3Bucket
          - DomainName
    Description: Name of S3 bucket to hold website content
```
- __Error retries and exponential backoff in AWS__
    - components such as DNS servers, switches, load balancers, and others—can generate errors anywhere in the life of a given request
    - usual technique is to implement retries in the client
    - increases the reliability of the app and reduces operational costs for the developer
    - AWS SDKs already implement exponential backoffs by default
        - this behavior would be one benefit of using Amazon Cognito on the client side to send data to Kinesis, instead of using a custom routine that sends data to API Gateway
        - set a maximum delay interval and a maximum number of retries
- __Data optimizations (best practices) for Amazon Athena__
    - __compressing__
        - Athena charges per amount of data scanned (amount of data fetched to perform a query)
        - data compression can reduce file sizes
            - scans less data per query
                - reduces cost
                - faster
    - __partitioning__
        - for example if your queries are by month, then partition your data into different files by month
        - again, scans less data per query
    - __columnar formats__
        - __Apache Parquet__ and __ORC__ are __columnar storage formats__ that are __optimized for the fast retrieval of data__ and they are also used in AWS analytical applications
        - __Compression by column__, with the compression algorithm selected for the column data type to save storage space in Amazon S3, and reduce disk space and I/O during query processing
        - __Predicate pushdown__ in Parquet and ORC enables Athena queries to fetch only the blocks that it needs, thus improving query performance
            - When an Athena query obtains specific column values from your data, it uses statistics from data block predicates, such as max or min values, to determine whether to read or skip the block.
            - enables Athena to split the reading of data to multiple readers and increase parallelism when it processes queries

## Exercise 2 Walkthrough Part 1, Part 2
Walkthrough of the PoC

## Challenge: Build a Proof of Concept
- Exercise 2: Architecting Solutions: Building a Proof of Concept for Data Analytics
- https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/DEV-AWS-MO-Architecting/exercise-2-analytics.html

## Resources
- [Analytics on AWS](https://aws.amazon.com/big-data/datalakes-and-analytics/)
- [Amazon S3](https://aws.amazon.com/s3/)
- [Amazon S3 Glacier storage classes](https://aws.amazon.com/s3/storage-classes/glacier/)
- [AWS Lake Formation](https://aws.amazon.com/lake-formation/)
- [Amazon Athena](https://aws.amazon.com/athena/)
- [Amazon EMR](https://aws.amazon.com/emr/)
- [Amazon OpenSearch Service](https://aws.amazon.com/opensearch-service/)
- [Amazon Kinesis](https://aws.amazon.com/kinesis/)
- [Amazon Glue](https://aws.amazon.com/glue/)
- [AWS Data Migration Service](https://aws.amazon.com/dms/)
- [Amazon SageMaker](https://aws.amazon.com/sagemaker/)
- [Amazon Rekognition](https://aws.amazon.com/rekognition/)
- [Amazon Comprehend](https://aws.amazon.com/comprehend/)
- [Amazon Comprehend Medical](https://aws.amazon.com/comprehend/medical/)
- [Amazon S3 Cross-Region Replication](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html)
- [Amazon S3 Intelligent-Tiering storage class](https://aws.amazon.com/s3/storage-classes/intelligent-tiering/)
- [Amazon S3 Glacier storage classes](https://aws.amazon.com/s3/storage-classes/glacier/)
- [Amazon Kinesis](https://aws.amazon.com/kinesis/)
- [Amazon Kinesis Data Streams](https://aws.amazon.com/kinesis/data-streams/)
- [Amazon Kinesis Data Firehose](https://aws.amazon.com/kinesis/data-firehose/)
- [Amazon Kinesis Data Analytics - Amazon Managed Service for Apache Flink](https://aws.amazon.com/managed-service-apache-flink/)
- [Amazon Kinesis Video Streams](https://aws.amazon.com/kinesis/video-streams/)
- [Serverless on AWS](https://aws.amazon.com/serverless/)
- [Amazon CloudFront](https://aws.amazon.com/cloudfront/)
- [Amazon Cognito](https://aws.amazon.com/cognito/)
- [AssumeRoleWithWebIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html)
- [Template snippets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/CHAP_TemplateQuickRef.html)
- [AWS CloudFormation](https://aws.amazon.com/cloudformation/)
- [Error retries and exponential backoff in AWS](https://docs.aws.amazon.com/sdkref/latest/guide/feature-retry-behavior.html)
- [Top 10 Performance Tuning Tips for Amazon Athena](https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)
- [Partitioning data in Athena](https://docs.aws.amazon.com/athena/latest/ug/partitions.html)
- [Converting to columnar formats](https://docs.aws.amazon.com/athena/latest/ug/convert-to-columnar.html)
- [Exercise 2: Architecting Solutions: Building a Proof of Concept for Data Analytics](https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/DEV-AWS-MO-Architecting/exercise-2-analytics.html)
