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


## Resources
- [Analytics on AWS](https://aws.amazon.com/big-data/datalakes-and-analytics/)
