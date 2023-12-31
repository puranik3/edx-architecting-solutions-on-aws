# Week 1 - Designing a serverless web backend on AWS

## Customer #1: Use Case and Requirements
- Have used lift-and-shift to migrate to AWS so fay
- looking to use cloud-native (AWS-native) technologies now
- e-commerce store with website and app
    - customer shops
    - customer can also purchase wholesale, for selling in their retail stores
    - __orders service__ accepts orders from web and mobile and is __hosted on-premise__
        - authenticates, validates and accepts (or rejects) orders, processes them, and stores them in the DB
        - calls downstream services like
            - inventory service
            - fulfilment service
            - accounting service
            - __these downstream services are already on AWS!__
        - __Assumption__: Payment has already been processed using a payment gateway, before the order service is invoked
    - web server accepting order requests and application server are right now the same
    - __uses a MySQL DB on-premise__
    - downstream services work with their own DB
    - open to migrating DB
    - DB required to be highly available and durable
    - order details are right now maintained in only 1 table! - data is minimal and maintaining a whole DB seems overkill
    - what are the current pain points that are driving rewrite/re-architecting rather than lift-and-shift?
        - when there are large number of orders, service is unable to handle it, and orders get dropped
            - i.e. scaling quickly on-demand on-premise is a problem currently
            - looking for automatic scaling
        - sometimes some downstream service calls are completed but others are not as order service crashes before order completion
            - eg. call to inventory service completed
            - before its result could be processed by the order service (and calls made to fulfilment and accounting), the order service crashes
                - __problem is the services are tightly coupled__
            - need is to decouple the services to increase resilience of the app
        - result in loss of orders, inconsistency in orders data for customers
    - app encounters spiky demand from customers (as opposed to low/high volume steady/cyclical demand)
        - demand increases when coupons are sent out, or as part of sales campaigns
        - otherwise close to no demand
        - right now on-premise data center needs to over-provision resources
    - company is open to rewrite code to go cloud-native
    - need monitoring and logging, and common logging system throughout the app
    - need to optimize the solution for cost and performance
        
## Customer #1: Requirements Breakdown
- migrating from on-premises to SOA on AWS
- orders service is built as a monolith
- web server, orders service, DB are in scope of migration
    - 3 downstream services are out-of-scope
- service needs to be more resilient
    - failure of one part of the app does not affect other parts
    - suggests loose-coupling through event-driven architecture
- manage scaling for DB and compute
    - suggests serverless architecture as its fully-managed!
- centralized monitoring and logging
    - serverless services are already well-integrated with CloudWatch and CloudWatch Logs
- optimizing for cost, performance efficiency, operational overhead
    - __cost shall be the main driver for choice of services__
    - __performance efficiency, operational overhead are secondary drivers for the choices__
    - again serverless optimizes these factors in this priority!
    - also, once we architect we can review various choices of servies and see how to optimize further for these 3 factors


## Resources
