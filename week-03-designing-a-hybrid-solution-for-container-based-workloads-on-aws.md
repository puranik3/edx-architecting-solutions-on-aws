# Week 3 - Designing a hybrid solution for container based workloads on AWS

## Week 3 Introduction
- customer is migrating containers to AWS

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
    - plan to migrate a few Postgres DBs to containers
    - but do not want to rewrite apps due to this migration - is there such a solution??
        - i.e. want a lift-and-shift for this part
- need to use same orchestration tools in both environments
- need high up-time (availability)

## Customer #3: Requirements Breakdown
