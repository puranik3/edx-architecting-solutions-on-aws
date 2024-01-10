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

## Reading 4.1: Multi-account Strategies
By using multiple AWS accounts to help isolate and manage your business applications and data, you can __optimize across most of the AWS Well-Architected Framework pillars__ including __operational excellence__, __security__, __reliability__, and __cost optimization__.
- __Group workloads based on business purpose and ownership__
    - this way you can __align the ownership and decision-making of those accounts__
    - also avoids dependencies and conflicts with how workloads in other accounts are secured and managed
- __Apply distinct security controls by environment__
    - it is common to apply different policies for security and operations to the non-production and production environments of a given workload
- __Constrain access to sensitive data__
    - principle of least-privilege access
- __Promote innovation and agility__
    - technologists are builders because they build value by using AWS products and services
        - application developers, data engineers, data scientists, data analysts, security engineers, and infrastructure engineers
    - promote innovation by providing your builders with separate accounts in support of experimentation, development, and early testing
        - give broader access to AWS services while also using guardrails that help prohibit access to (and the use of) sensitive and internal data
    - often provide greater freedom than more tightly controlled production-like test and production environments
    - __Sandbox accounts__
        - disconnected from your enterprise services and don't provide access to your internal data
        - offer the greatest freedom for experimentation
    - __Development accounts__
        - limited access to your enterprise services and development data
        - support day-to-day experimentation with your enterprise-approved AWS services, formal development, and early testing work
    - you can support later stages of the workload lifecycle by using distinct test and production accounts for workloads or groups of related workloads
- __Limit scope of impact from adverse events__
    - an AWS account applies boundaries for security, access, and billing boundaries to your AWS resources
    - limits the risks of an application-related issue, misconfiguration, or malicious actions
- __Support multiple IT operating models__
    - organizations often have multiple IT operating models
        - way to divide responsibilities among parts of the organization to deliver their application workloads and platform capabilities
        - __IT Service Management (ITSM) - Three example operating models__
            - __Traditional Ops model__
                - __application teams__ who own custom and commercial off-the-shelf (COTS) applications are responsible for __engineering their applications__
                - __cloud platform engineering team__ is responsible for __engineering the underlying platform capabilities__
                - __cloud operations team__ is responsible for the __operations of both applications and platform__.
            - __CloudOps model__
                - __application teams__ are also responsible for __production operations of their applications__
                - __cloud platform engineering team__ is responsible for both the __engineering and operations of the underlying platform capabilities__
            - __DevOps model__
                - __application teams__ take on the additional responsibilities of __engineering and operating platform capabilities that are specific to their applications__
                - __cloud platform engineering team__ is responsible for the __engineering and operations of shared platform capabilities that are used by multiple applications__
    - you might benefit from establishing separate groups of accounts in support of different operating models
- __Manage costs__
    - more easily report, control, forecast, and budget your cloud expenditures by using different accounts
- AWS has built-in support to consolidate and report costs across your entire set of accounts as well
- when you require __fine-grained cost allocation__, you can __apply cost allocation tags__ to __individual resources__ in __each of your accounts__
- __Distribute AWS service quotas and API request rate limits__
    - __AWS service quotas (aka limits)__ are the maximum number of service resources or operations that apply to an account
        - eg. the number of S3 buckets that you can create for each account
    - __AWS services can also throttle (or limit) the rate of requests__ that are made to their API operations
    - because service quotas and request rate limits are allocated for each account, use separate accounts for workloads to help distribute the potential impact of the quotas and limits

## IAM Roles - The AWS Authentication Core Mechanism
- IAM users, IAM groups, and IAM roles.
- IAM users and IAM Roles help manage credentials and permissions for access to an AWS account
    - users have permanent credentials
    - roles provide a temporary credential or a URL for sign in
        - this operation is called "assume role"
        - roles help prevent replicating IAM users on every account

### Demo
- Account A (developer-account-a) shall have a role, allowing an IAM user from Account B, to do the assume-role operation
- __Role Trust Relationship__ - who is authorized to assume this role (i.e. user called "rafael" from Account B)
- __Role permissions__ - will have __AdministratorAccess__
    - if it was an auditor role, for example, we could create an IAM policy that only allow listing resources
- __Role name__ - will be called AdminRole
- when we refer to that IAM user rafael, we will need the __Amazon Resource Name (ARN)__ of that user
    - apart from other AWS accounts (for cross account roles), even EC2 instances, Lambdas can assume a role
- After creating a role by pecifying basic details, we can add trust relationship to mention only user rafael from Account B can assume AdminRole
- Now we can log in as Rafael from Account B and assume AdminRole from Account A by choosing the option __Switch role__
- there are AWS services that can provision new accounts as needed
    - this service will already create administrative roles on newly created accounts
- IAM role are vital because it is the core cross account authentication mechanism (__centralized credentialing__)

## Reading 4.2: IAM Roles, Trust Relationships, and Permissions
- roles provide cross-account access without needing to create static credentials for each account, such as IAM users
- you can create in an account
- has specific permissions
- has some similarities to an IAM user
    - both AWS identities with permissions policies that determine what the identity can or can’t do in AWS
    - use is uniquely associated with one person
    - role can be assumed by anyone who needs it
    - role doesn't have standard long-term credentials (such as a password or access keys)
        - when you assume a role, it provides you with temporary security credentials for your role session
- __roles can be used by__ the following entities
    - __An IAM user in the same AWS account__ as the role
    - __An IAM user in a different AWS account__ than the role
    - __An AWS service, such as EC2__
    - __An external user who was authenticated by an external identity provider (IdP) service that's compatible with Security Assertion Markup Language (SAML) 2.0, OpenID Connect (OIDC), or a custom-built identity broker__
- __AWS service role__
    - a role that a service assumes to perform actions in your account on your behalf
        - includes all the permissions that the service needs to access the AWS resources that it must work with
        - __You can create, modify, and delete a service role from within IAM__
- __AWS service-linked role__
    - a unique type of service role that's linked directly to an AWS service
    - __service-linked roles are predefined by the service__, and they include all permissions that the service needs to call other AWS services on your behalf
    - The linked service also defines how you create, modify, and delete a service-linked role
        - A service might automatically create or delete the role
        - It might allow you to create, modify, or delete the role
            - as part of a wizard or process in the service
            - or it might require you to use IAM to create or delete the role
- __Delegation__
    - __granting of permissions to someone to allow access to resources that you control__
    - involves setting up a trust between two accounts
    - the __trusted__ and __trusting accounts__ can be any of the following
        - same account
        - separate accounts that are both under your organization's control
        - separate accounts owned by different organizations
    - __to delegate permissions__ to access a resource, __you create an IAM role in the trusting account__ that __has two policies attached__
        - the __permissions policy__ grants the user of the role the needed permissions to carry out the intended tasks on the resource
        - the __trust policy__ specifies which trusted account members are allowed to assume the role
            - when you create a trust policy, you can't specify a wildcard (*) as a principal
    - the __other half is a permissions policy__ that's attached to the __user in the trusted account__
        - the permissions policy __allows that user to switch to (or assume) the role__
        - __a user who assumes a role temporarily gives up his or her own permissions__, and instead takes on the role's permissions
        - When the user exits (or stops using) the role, their original user permissions are restored
        - An additional parameter that's called __external ID__ can help ensure the __secure use of roles between accounts that are not controlled by the same organization__
- __Federation__
    - creation of a trust relationship between an external Identity Provider (IdP) and AWS
        - Login with Amazon, Facebook, Google, or any IdP that is compatible with OIDC
        - Users can also sign in to an enterprise identity system that's compatible with SAML 2.0, such as Microsoft Active Directory Federation Services
        - When you use OIDC and SAML 2.0 to configure a trust relationship between these external IdPs and AWS, __the user is assigned to an IAM role__
        - the user also receives temporary credentials that allow the user to access your AWS resources
    - __Federated user__
        - identities as a result of federation
- __Trust policy__
    - JSON policy document where you define the principals that you trust to assume the role
    - principals that you can specify in the trust policy include users, roles, accounts, and services
- __Permissions boundary__
    - an advanced feature where you use policies to limit the maximum permissions that an identity-based policy can grant to a role
    - you can't apply a permissions boundary to a service-linked role
- __Principal__
    - an entity in AWS that can perform actions and access resources
    - A principal can be an AWS account root user, an IAM user, or a role

## Organizing Your Accounts Together Using OUs (Organizational Units)
- __AWS Organization__
    - earlier the service was called __Consolidated Billing__
- a managed service that helps organize accounts
- you create an Organization from a primary AWS account
- then define __Organizational Units (OUs)__
- OUs can be multi-level (have other OUs), and have
    - other AWS accounts
    - other OUs
- Define __Service Control Policies (SCPs)__
    - enable central administration over the services available within the accounts in the organization
- Demo summary
    - A __Shared services account__ creates and __maintains the Organization__
        - defines OUs, SCPs etc.
    - A __Developer account__ for developers to use
        - creates and maintains an __IAM role for developers__
        - I am assuming that devs will log into their respective accounts and assume this role from the Developer account (??)
    - Demo had an OU hierarcy and also showed account creation from with AWS Organization
        - The OU of the created account had an SCP that allowed EC2 instance creation, but denied all instances types except t2.micro!
        - the demo showed the user logging into thei account and assuming the role
            - user could not create any EC2 instance except t2.micro!

## Moving from One Account to Another
- we can move from our user account to dev account (with AssumeRole), to another account etc.
    - but this process does not scale to many accounts that we may have to switch over to
    - is there a better way?
- Yes, use __IAM Identity Center__
    - service that enables Single Sign-On (SSO)
    - formerly called AWS Single Sign-On
- here too we can create users
- After user creation, we create a Permission set
    - a convenient abstraction on top of permissions
    - eg. pre-defined ones include AdministratorAccess, Billing, DatabaseAdministrator, NetworkAdministrator, DataScientist etc.
        - you can also define custom permission sets
    - say, we chose AdministratorAccess
    - this is used to create a role behind-the-scenes associated with this account
- Next we associate the users/groups that will have multi-account access to this new account
    - choose a user account in the OU hierarchy (which was created through the AWS Organizations console) and add this new user to it
    - also choose the role for the user (AdministratorAccess)
- IAM Identity Center has an __AWS Access Portal URL__ which is used for SSO login
    - __when user logs in through this portal, they can see all accounts they have access to!__

## Reading 4.3: AWS IAM Identity Center
- helps you securely create or connect your workforce identities and manage their access centrally across AWS accounts and applications
- recommended approach for workforce authentication and authorization on AWS for organizations of any size and type
- __Workforce identities__
    - Human users who are members of your organization are also known as workforce identities or workforce users
    - You can create workforce users and groups in IAM Identity Center
- __Application assignments for SAML applications__
    - With application assignments, you can grant your workforce users in IAM Identity Center SSO access to Security Assertion Markup Language (SAML) 2.0 applications, such as Salesforce and Microsoft 365
    - Your users can access these applications in a single place, without the need for you to set up federation separately
- __Identity Center enabled applications__
    - AWS applications and services, such as Amazon Managed Grafana, Amazon Monitron, and Amazon SageMaker Studio Notebooks, discover and connect to IAM Identity Center automatically to receive sign-in and user directory services. This feature provides users with a consistent SSO experience for these applications, with no additional application configuration. Because the applications share a common view of users, groups, and group membership, users can also have a consistent experience when they share application resources with others.
- __Multi-account permissions__
    - With multi-account permissions, you can plan for and centrally implement IAM permissions across multiple AWS accounts at one time, without the need for you to configure each of your accounts manually. You can create fine-grained permissions based on common job functions, or define custom permissions that meet your security needs. You can then assign those permissions to workforce users to control their access over specific accounts.
- __AWS access portal__
    - The AWS access portal provides your workforce users with one-click access to all their assigned AWS accounts and cloud applications through a web portal

## Choosing Services for Logging
- Application logging
    - done at the app level
    - exception, debug logs etc.
    - from EC2, Lambda, ELB, CloudFront etc.
    - CloudWatch logs, logs stored in S3
- Infrastructure logging
    - data produced by AWS services and events
    - AWS service calls, how infrastucture changed due to an event like auto-scaling, networking logs, security logs
    - AWS CloudTrail, AWS Config, VPC Flow logs, Amazon Guard Duty
- CloudTrail
    - Create a trail
        - give a name
        - mention S3 bucket to store it in
        - Choose event types that have to be captured
            - Management events - management ops performed on AWS resources
            - Data events - resource ops performed within a resource
            - Insight events - unusual activity, errors, user behavior in your account
    - Make sure to use appropriate SCP policy to deny access / change by other accounts to the S3 bucket on which the logs are captured
    - CloudTrail captures and sends accounts data to the organization account making it easy to get the big picture of how multiple users are using the accounts in the organization

## Reading 4.4: Logging Services
- __AWS CloudTrail__
    - monitors and records account activity across your AWS infrastructure, which gives you control over actions for storage, analysis, and remediation
    - __captures AWS API call events and logs them__
    - __trail__ is a configuration that enables delivery of events to an Amazon S3 bucket that you specify
        - delivers logs within an average of about 15 minutes after an API call (not guaranteed)
    - deliver and analyze events in a trail with these for real-time monitoring of events
        - Amazon CloudWatch Logs
        - Amazon EventBridge
    - you can create an __organization event data store__ that logs all __events for all AWS accounts in that organization__
        - member accounts can see the organization trail, but they can't modify or delete it
            - by default, member accounts don't have access to the log files for the organization trail in the S3
    - __CloudTrail Lake__ (CloudTrail data in a columnar storage format called Apache ORC) lets you log events from applications outside of AWS, including from any source in your hybrid environments, such as in-house or SaaS applications hosted on-premises or in the cloud, virtual machines, or containers by creating __integrations__
    - Two types of trails
        - A trail that applies to all Regions
        - A trail that applies to one Region
    - CloudTrail maintains an event history that includes the past 90 days of activity
- __AWS Config__
    - __AWS Config provides a detailed view of how AWS resources are configured in your AWS account__
        - __like version control for your resource configurations on AWS!__
    - as the demand for your application grows, your need to keep track of your AWS resources also grows
    - AWS Config is designed to help you manage your application resources
        - how resources are related to one another
        - get an inventory of the resources that you have in your AWS account, and then you can apply rules for how those resources are configured
        - how they were configured in the past
        - notify you when resources are created, modified, or deleted without needing to monitor these changes by polling
    - __Resource administration__
        - you need fine-grained visibility into what resources exist
            - for better governance over your resource configurations
            - to detect resource misconfigurations
        - use __AWS Config rules__ to __evaluate the configuration settings of your AWS resources__
            - when violation is detected, flags and sends a notification
            - designed to continuously evaluate your resources as they are created, changed, or deleted
    - __Auditing and compliance__
        - provides access to the historical configurations of your resources for audit
    - __Managing and troubleshooting configuration changes__
        - view how the resource that you want to modify is related to other resources, and thus assess the potential impact of your change
        - use the historical configurations to troubleshoot issues and to access the last-known good configuration of a problem resource
    - __Security analysis__
        - help analyze potential security weaknesses using historical information about your AWS resource configurations
            - eg. IAM permissions granted to users, or EC2 SG rules that control access to your resources
- __VPC Flow Logs__
    - used to __capture information about the IP traffic__ that goes to and from network interfaces in your VPC
    - can be published to Amazon CloudWatch Logs or Amazon S3
    - Use cases
        - Diagnosing overly restrictive SG rules
        - Monitoring the traffic that reaches your instance
        - Determining the direction of the traffic to and from the network interfaces
    - you can create or delete flow logs with a minimal risk of impact to network performance
    - __Flow Log Records__
        - capture information such as source and destination IP addresses, ports, protocol, and the amount of data transferred
    - __Flow Log Types__
        - There are three types of flow logs
            - VPC Flow Logs
            - Subnet Flow Logs
            - Elastic Network Interface (ENI) Flow Logs
        - each captures traffic at a different level of granularity
    - __How Flow Logs Work__
        - __Flow Log Configuration__
            - You create a flow log by associating it with a VPC, subnet, or network interface. You define the traffic type (all, accepted, or rejected) and choose a destination for the logs (Amazon S3 bucket, CloudWatch Logs group, or both).
        - __Flow Log Data__
            - Flow logs capture data for accepted and rejected traffic based on the specified filter criteria. Logs include metadata like source and destination IP addresses, ports, and timestamps.
        - __Destination Options__
            - You can choose to store flow logs in an Amazon S3 bucket, send them to a CloudWatch Logs group, or use both destinations for redundancy and different analysis purposes.
- __Amazon GuardDuty__
    - __near-continuous security monitoring service__ that analyzes and processes data sources, such as
        - CloudTrail data events for Amazon S3 logs
        - CloudTrail management event logs
        - DNS logs
        - EBS volume data
        - EKS audit logs
        - VPC flow logs
    - uses threat intelligence feeds (such as lists of malicious IP addresses and domains) and ML to identify unexpected, potentially unauthorized, and malicious activity within your AWS environment
        - escalation of privileges
        - use of exposed credentials
        - communication with malicious IP addresses or domains
        - presence of malware on your EC2 instances and container workloads
            - eg. GuardDuty can detect compromised EC2 instances and container workloads that are serving malware or mining bitcoin
        - also monitors AWS account access behavior for signs of compromise, such as unauthorized infrastructure deployments
            — eg. instances that are deployed in a Region that has never been used, or unusual API calls (such as a password policy change to reduce password strength)

## Automating Account Provisioning and Maintenance
- __AWS Control Tower__
    - __like an account vending machine__
    - can automate account deployment (creation of AWS accounts) and account governance
    - can add __AWS Service Catalog__ products on creation (See below) and __provision infrastructure as well through it__!
        - uses __CloudFormation StackSets__ to set up resources in your accounts. Each __stack set has stack instances__ that correspond to accounts, and to AWS Regions per account. AWS Control Tower __deploys one stack set instance per account and Region__.
    - helps protect your organizations and accounts from being affected by drift (or divergence from best practices)
- __AWS Service Catalog__
    - managed
    - create and managed pre-approved portfolios and products
    - catalogs can include a variety of AWS resources and third-party software products
    - __like an infrastructure vending machine__ based on CloudFormation
        - since CloudFormation provisions and configures those resources for you, you can configure AWS resources, for example, to even send logs you need to CloudWatch! You can of course include more configurations
    - __product__ - an IaC defined using CloudFormation template, or an AMI
    - a product is added to a __portfolio__
    - users can browse portfolios and launch products
        - eg. A __Dev environment__ portfolio may have products like __Node JS environment single instance SQS queue for project X__ etc.

## Reading 4.5: Automatic Account Provisioning
- __Cloud Formation__
    - __Resource Management__
        - CloudFormation manages the entire lifecycle of the stack and its resources, including creating, updating, and deleting resources
    - __Change Sets__
        - Before making changes to a stack, CloudFormation allows you to preview the changes using change sets. This helps you understand the impact before applying modifications.
    - __Rollback Mechanism__
        - CloudFormation provides automatic rollback on failure, ensuring that the system returns to a stable state
- __AWS Control Tower__
    - orchestrates the capabilities of several other AWS services including
        - AWS Organizations
        - IAM Identity Center
        - AWS Service Catalog
    — helps build an AWS Landing Zone (for a new employee say) in typically less than an hour
    - __Guardrails__
        - pre-packaged, automated policies that help enforce governance at both the AWS organization and account levels
- __AWS Service Catalog__
    - create and manage catalogs of IT services that are approved for AWS
        - can include VM images, servers, software, DBs
        - more importantly, they can even include complete, multi-tier application architectures
    - __Benefits__
        - __Standardization__
            - Administer and manage approved assets by restricting where the product can be launched
                - the type of instance
                - other configuration options
        - __Self-service discovery and launch__
            - browse listings of products, locate and launch products
        - __Fine-grained access control__
            - admins assemble portfolios of products from their catalog, add constraints and resource tags that will be used when the products are provisioned
            - admins then grant access to the portfolio through IAM users and groups
        - __Extensibility and version control__
            - admins can add a product to different portfolios and restrict it without creating another copy
            - when the product is updated to a new version, the update is propagated to the product in every portfolio that references it

## Consider this Scenario
You are a consultant who is working with a customer that wants to use AWS to meet their business needs. This customer has multiple AWS accounts, but they want to better control the permissions scope of their accounts. You decide to recommend AWS Organizations for that task, with the help of service control policies (SCPs).  
  
In a few sentences, explain how SCPs are used to define permissions and how this can help an AWS customer follow best practices.

- https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html

**AWS Organizations Service Control Policies (SCPs):**

**Overview:**
Service Control Policies (SCPs) in AWS Organizations are a type of policy that helps you set fine-grained permissions and restrictions on the AWS services and actions that can be performed within your organization's accounts. SCPs act as guardrails to ensure that organization members comply with organizational policies.

**Key Concepts:**
1. **Organizational Units (OUs):**
   - AWS Organizations allows you to organize your accounts into OUs. SCPs can be applied at different levels, such as the root of the organization, an OU, or an individual account.
2. **Policy Inheritance:**
   - SCPs are inherited by child OUs from their parent OUs, forming a hierarchical structure. Permissions become more restrictive as you move down the hierarchy.
3. **Deny Overrides Allow:**
   - If an account is subject to multiple SCPs, the "deny" permissions take precedence over "allow" permissions. This ensures that you can set restrictive policies at higher levels while allowing more permissive policies at lower levels.

**Key Features:**
1. **Granular Control:**
   - SCPs provide granular control over the AWS services and actions that accounts within the organization can access.
2. **Default Allow Access:**
   - By default, all AWS services and actions are allowed. SCPs are used to restrict access based on the organization's security and compliance requirements.
3. **Visibility:**
   - SCPs are visible at all levels of the organization, and you can view the effective policies on a specific account to understand the combined set of permissions applied.
4. **Policy Evaluation:**
   - SCPs are evaluated in conjunction with IAM policies, allowing you to define both broad organizational policies and account-specific policies.

**Workflow:**
1. **Create SCP:**
   - Create SCPs in the AWS Organizations console or using the AWS Command Line Interface (CLI). Define the specific permissions and restrictions.
2. **Attach SCP:**
   - Attach SCPs to the desired level in the organization hierarchy (root, OU, or account).
3. **Inheritance:**
   - SCPs are inherited by child OUs, and the combined set of policies determines the effective permissions for each account.
4. **Policy Evaluation:**
   - SCPs are evaluated along with IAM policies to determine the permissions that apply to a specific action in an account.

**Use Cases:**
1. **Compliance Requirements:**
   - Enforce compliance requirements by restricting access to certain AWS services or actions based on organizational policies.
2. **Security Controls:**
   - Implement security controls by preventing specific actions or access to services that pose a risk to the organization.
3. **Custom Policies:**
   - Tailor policies to the specific needs of different organizational units or accounts.

**Benefits:**
1. **Policy Consistency:**
   - Ensure consistent policy enforcement across the organization.
2. **Hierarchical Control:**
   - Leverage the hierarchical structure to apply policies at different levels.
3. **Adaptability:**
   - Adapt policies to changing organizational needs and compliance requirements.

In summary, AWS Organizations' Service Control Policies (SCPs) provide fine-grained control over the permissions and restrictions within an organization. They are used to enforce compliance, security controls, and custom policies by defining access rules for AWS services and actions at different levels of the organizational hierarchy.

## Customer #4: Solution Overview
Explained the solution to the customer

## Week Wrap-Up: Taking this Architecture to the Next Level
- Restrict root user from accessing member accounts using SCP
- Restrict root user from accessing many AWS resources
- Restrict member accounts from disabling CloudTrail
    - you can put an SCP at the root OU for this, which restricts every account including the main account used to create other accounts
- Restrict console logging to only accounts with AWS or from the on-prem resources across the Private VPN (need the list of IP addresses of these resources to do this through a Condition in an SCP)
- __Tag policies__
    - make sure to use standardised tagging formats and policies using tag policies (a feature in AWS Organizations)
    - again these policies are mentioned in the SCP
    - eg. you can prevent tag modification on services that are already running
    - __Note__: Tags are case-sensitive
- You can create billing metrics for each account, and have CloudWatch alarms based on these
- Enable MFA for Identity Center SSO
- SCP on the primary account to prevent unauthorized users from moving child accounts from one OU to another

## Reading 4.6: Multi-account Best Practices
- __Multi-account environments__
    - we can use different organizational unit (OU) structures for different use cases
        - a corporate OU, a production public-facing OU, a production internal-facing OU, and a security OU. At the end, you might have a complex structure that looks like the following diagram:
- __Tag policies and SCPs__
    - maintain standardized tags for AWS resources that are used with Organizations accounts.
    - eg. the following example tag policy defines a tag with a key of Environment and a value of Production, and this tag is enforced for EC2 instances.
    ```json
    {
        "tags": {
            "Environment": {
                "tag_key": {
                    "@@assign": "Environment"
                },
                "tag_value": {
                    "@@assign": [
                        "Production"
                    ]
                },
                "enforced_for": {
                    "@@assign": [
                        "ec2:instance"
                    ]
                }
            }
        }
    }
    ```
    - This tag policy prevents users from changing this tag on existing Amazon EC2 instances. However, it doesn't prevent a user from launching new instances with non-compliant tags, or no tags. This tag policy might be adequate if you use infrastructure as code (IaC) to provision environments. With IaC, environments will be created from a CloudFormation template, and you can thus embed the tags in the template.
    - However, if you want to prevent the creation of new AWS resources that aren't tagged, you need to use SCPs. You could use the following example SCP to make sure that the AWS resources are created only if a certain tag is present. This SCP requires specific tags on specified created resources, and it uses explicit deny statements.
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyCreateSecretWithNoProjectTag",
      "Effect": "Deny",
      "Action": "secretsmanager:CreateSecret",
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/Project": "true"
        }
      }
    },
    {
      "Sid": "DenyRunInstanceWithNoProjectTag",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:*:*:instance/*",
        "arn:aws:ec2:*:*:volume/*"
      ],
      "Condition": {
        "Null": {
          "aws:RequestTag/Project": "true"
        }
      }
    },
    {
      "Sid": "DenyCreateSecretWithNoCostCenterTag",
      "Effect": "Deny",
      "Action": "secretsmanager:CreateSecret",
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/CostCenter": "true"
        }
      }
    },
    {
      "Sid": "DenyRunInstanceWithNoCostCenterTag",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:*:*:instance/*",
        "arn:aws:ec2:*:*:volume/*"
      ],
      "Condition": {
        "Null": {
          "aws:RequestTag/CostCenter": "true"
        }
      }
    }
  ]
}
```

## Capstone Project
It's time to put together everything you learned!  

As part of this project, you will create a high-level architecture diagram that uses  AWS service icons and arrows  to depict an AWS solution for the given scenario. Create your diagram by using a tool like [diagrams.net](https://app.diagrams.net/?splash=0&libs=aws4) , or you can select a different tool by from the  AWS Architecture Icons  page by scrolling to the Drawing and diagramming tools section.  

Scenario: You are working for a customer that runs their workloads on premises. Your customer has two workloads:  

A three-tier architecture composed of a frontend (HTML, CSS, JavaScript), backend (Apache Web Server and a Java application), and database (MySQL). The three-tier application hosts a dynamic website that accepts user traffic from the internet.  

A data analytics workload that runs Apache Hadoop. The analytics workload analyzes a massive amount of data that stored on premises and it also uses visualization tools to derive insights.  

These components are currently running in the data center on physical servers. Currently, if a power outage occurred in the data center, all systems would be brought offline. Because of this issue (in addition to other benefits of the cloud), your customer wants to migrate all components to the cloud and, when possible, use AWS services to replace on-premises components.   

Instructions: You have been tasked with designing a solution that uses AWS services to decouple the application layers (frontend, backend, and database), and that hosts both the application and the data analytics workload in the cloud. You can use managed services and advocate for refactoring the code to take advantage of cloud-native technologies, or you can do a lift and shift and advocate for minimal refactoring. Also, the data analytics solution currently runs on Hadoop and you have a requirement to spin up an Amazon EMR cluster for it. However, it’s up to you to choose which AWS services you want to use for the ingestion, storage, and visualization of data.  

Whichever architecture you choose to create, write an explanation that details how the solution works and why you chose to use the services that you selected. Also, create an architecture diagram that depicts how both solutions will be hosted on AWS.  


## Resources
- [Establishing Your Cloud Foundation on AWS](https://docs.aws.amazon.com/whitepapers/latest/establishing-your-cloud-foundation-on-aws/welcome.html)
- [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/)
- [Organizing Your AWS Environment Using Multiple Accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html)
- [Roles terms and concepts](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html)
- [AWS Organizations](https://aws.amazon.com/organizations/)
- [What is IAM Identity Center?](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html)
- [How CloudTrail works](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html)
- [Logging IP traffic using VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [What is Amazon GuardDuty?](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
- [What is AWS CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [Deploy a Reliable Multi-tier Infrastructure Using CloudFormation](https://catalog.workshops.aws/well-architected-reliability/en-US/3-change-management/10-deploy-cloudformation)
- [What is AWS Control Tower?](https://docs.aws.amazon.com/controltower/latest/userguide/what-is-control-tower.html)
- [What is AWS Service Catalog?](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/introduction.html)
- [Best Practices for AWS Organizations SCPs in a Multi-Account Environment](https://aws.amazon.com/blogs/industries/best-practices-for-aws-organizations-service-control-policies-in-a-multi-account-environment/)
- [Organizing Your AWS Environment Using Multiple Accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html)
- [Establishing your best practice AWS environment](https://aws.amazon.com/organizations/getting-started/best-practices/)
- [Require a tag on specified created resources](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_examples_tagging.html#example-require-tag-on-create)
- [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/)
- []()
- []()
- []()
- []()