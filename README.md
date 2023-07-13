# Data Transfer Cost considerations for Redis Enterprise Cloud on AWS

## Introduction
Often times when we architect a solution using  Redis Enterprise Cloud  for a specific customer use case requirement, we tend to overlook the costs involved with data transfer operations when we bring the data in to Redis.  This document tries to help identify areas where we could incur data transfer costs while choosing deployment options for Redis Enterprise Cloud and help make Solution Design choices.

Redis Enterprise Cloud pricing services and other AWS service charges are outside the scope of this document and hence an SA is encouraged to consider those costs carefully while crafting the solution design.

## Quick recap: Redis Enterprise Cloud Deployment options
High availability in Redis is achieved with data replication and automatic failover feature. Redis Enterprise Cloud offers 3 levels of data replication.
* No replication. This will give you only a single copy of your data
* Single-zone replication. This means that the primary and replica shards are co-located in the same availability zone.
* Multi-zone replication. This gives the primary and replica shards placed across multiple availability zones, but within the same Region.

### A word about Active - Active
With A-A CRDT databases, you can truly achieve Multi-Region and even Multi-Cloud  resilience for your Redis clusters.  Your entire Redis Enterprise Cloud cluster in one Region is replicated in another Region.

Given all of these topologies, let us now examine how data transfer charges can affect in each of these deployment scenarios.

## Redis Enterprise Cloud as a fully hosted DBaaS
When Redis is hosted as a fully managed DBaaS (Database as a service), the architecture would like this:
* Notice that there are 2 AWS accounts. One that customer owns, where a customer business application is running. And the other that  Redis Inc DevOps team manages. This is where Redis Enterprise Cloud is deployed. Each account has their respective VPCs setup .
* These two environments are peered together using VPC peering technology so that the customer applications can seamlessly connect to Redis Enterprise Cloud database endpoints.
* In this scenario, any data that the customer brings in to Redis Enterprise Cloud, will incur Data transfer charges, depending upon the network topology scenarios.

### Scenario 1: Same Region + Multi-AZ case:
If Redis is deployed as a multi-zone deployment and if the customer is bringing the data from a specific AZ, inter-AZ data transfer costs may be incurred.

### Scenario 2: Same Region + Single-AZ case:
If Redis is deployed as a single-az deployment and if the customer is bringing the data from a different AZ than where Redis is deployed, that may still incur inter-AZ data transfer costs.  Hence, it always makes sense to deploy Redis in the same AZ as the customer application’s AZ.  Also, AWS does not guarantee geographical co-location of data centers, even though their name may seem exactly same. Hence, you may still incur inter-AZ costs even though externally the AZ’s nomenclature seems exactly same.

### Scenario 3: Multi-Region case:
If Redis or customer workload components communicate across multiple Regions using VPC peering connections or Transit Gateway, additional data transfer charges apply. Even if the VPCs are peered across Regions, standard inter-Region data transfer charges will still apply.

In short, you may be always paying data transfer costs when you are migrating workloads to Redis on AWS.

![](images/dataxfer1.png)
