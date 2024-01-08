# Week 4 - Designing a solution following account governance and management best practices

## Week 4 Introduction
- General introduction to what's coming

## Customer #4: Use Case and Requirements
- somethings in the customer's AWS environment are getting out of control
- right now, everything is running in the same AWS account - customer calls it the "prod account"
- customer's a marketing agency which creates websites for their customers
    - some websites run on EC2 instances with RDS DBs
    - some websites are serverless
    - some run in containers
- everything for all of the customers running in a single AWS account right now!
    - risk of touching the wrong environment by accident!!
    - even if you do IaC, which is currently being done for some workloads, there is risk of touching the wrong stack
- customer __looking for an organized way to divide these big accounts into smaller pieces of infrastructure__
- customer also concerned with the __management side of things as well as billing__
    - right now everything is tagged billing reports are based on tags
    - but not everything is tagged!
    - no way to reinforce tags
- only 1 person handling the AWS account, but developers keep asking for AWS access now!
    - customer not sure how to dole out that access, and manage the resources that devs create, make sure they're tagged etc.
- requirement is to devise a strategy that uses AWS services to provide a __multi-account strategy with proper governance and security guard rails__
- customer is right now logging in with sys admin's personal email address
    - i.e. root user to login!
    - some staff are IAM users who have promised that they're always super careful not to do things without letting sys admin know
        - relying on a very basic permission mechanism
- devs are really good technical people with some cloud and coding expertise
- __broad solution__
    - these devs in future can be part of a Cloud CCoE (Center of Excellence) and will be the ones who access the accounts
    - would have a shared services account where the devs log in and from there use single sign-on to access other accounts
    - multiple AWS accounts using centralized credentialing to avoid replicating users on every account
    - automatic account provisioning - because solution is to have multiple accounts, customer would need to be trained on how to reinforce resource provisioning and automatic configuration across these AWS accounts
    - a dedicated account for logging security, API activity etc. as a good practice

## Customer #4: Requirements Breakdown
- points covered as just recap of requirements from the discussion
- some ways to group AWS services into separate accounts
    - per workload
    - per client
    - per environment like dev, QA, prod...
    - etc.
- creating an account that users will initially sign into, followed by SSO to log into other accounts
    - helps avoid replicating user credentials across multiple accounts
- another requirement is to set automatic account provisioning and enforce configuration standards across newly created accounts
- centralized logging in an account

## Why Multi-Account Strategies?
- common way to __group accounts based__ on __business purpose__ and __ownership__
- different BUs or product teams might have different processes
    - most common approach is to isolate distinct BUs into different groups
        - can help teams operate with greater decentralized control while still having security guardrails
    - we shall create an account per client of the customer
        - on top of this we can apply a workload segmentation model
        - that way each environment from each separate client will have its own organizational unit or OU
            - Client A can have its own OU.
            - within that OU, Client A can have three other OUs
                - Dev
                    - developers can have dedicated AWS accounts for themselves with a cloud IDE configured for example
                - Testing
                - Prod
            - with the help of mechanisms, such as Service Control Policies (SCP) in AWS organizations you can prevent devs from spinning up resources that are not authorized like large instance types on dev environments
            - billing controls individually per account
            - splitting all that into separate AWS accounts facilitates governance and access, making it easier to limit the access to the product account to CCoE users only
        - also need to have automation and centralized credentialing
            - otherwise things will get hard to manage soon
- __Advantages of multiple accounts__
    - group workloads based on business purpose or ownership
    - constrain access to sensitive data
    - distribute AWS service quotas and API request rate limits across accounts
    - limit blast radius from adverse events
    - centralize logging
    - manage costs better