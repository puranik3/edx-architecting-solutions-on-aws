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
- a managed service that helps organize accounts

## Resources
- [Establishing Your Cloud Foundation on AWS](https://docs.aws.amazon.com/whitepapers/latest/establishing-your-cloud-foundation-on-aws/welcome.html)
- [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/)
- [Organizing Your AWS Environment Using Multiple Accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html)
- [Roles terms and concepts](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html)
- []()
- []()
- []()
- []()
- []()