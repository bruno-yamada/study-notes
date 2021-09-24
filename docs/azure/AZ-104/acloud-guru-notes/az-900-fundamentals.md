# acloud.guru Azure fundamentals (AZ-900)

## Chapter 1 - Cloud Shell
For interacting with Azure, we have the following interfaces:
- Azure Portal
- Azure cli
  - since it is considered stable, it is good for automation
- Azure powershell
  - have Cmdlet (commandlets) for you to use
- Azure cloudshell
  - you can choose between bash and powershell
  - is a cloud based offering
  - bash only alows use of azure cli, powershell allows both cli and cmdlets
  - can be used from anywhere
- Azure Mobile Apps
  - have cloud shell!
  - doesnt have all the azure features
  - is good on-the-go
- Azure Advisor, gives tips on:
  - High availability
  - Security
  - Performance
  - Operational Excellence: health alerts
  - Cost

### What are ARM templates?
It seems the templates work in the same declarative and indempotent way terraform works

Plus it leverages source control

They are templates used to deploy whole stacks from a template

## Chapter 2 - Cloud concepts
> zet: 20210728013340Z

- High Availability
- Fault Tolerance
- Elasticity
- Disaster Recovery
- Agility
- Scalability

## Shared Responsability Model
When on-prem you are responsible for everything, with cloud, Azure takes responsability for some part of the infrastructure, such as servers, network, platform, etc, it usually depends on the type of offering (iaas, paas, saas..)

The model can help define who is responsible for a given area of the application

## Cloud Architect Models
Private Cloud
- complete control of the infra
- can be hosted on-prem or by a third-party
- same benefits as public
- harder to manage
- requires more staffing
- better security (in some cases)

Public Cloud
- aws, azure, gcp are public clouds
- no hardware purchased
- low fees
- no control over the hardware or where the data is stored

Hybrid Cloud
- Have both public and private cloud
- usually due to requirements from regulations or governance
- but it can get complex

## Chapter 3 - Azure Architecture
Regions & Availability Zones
- Region: set of datacenters, deployed within latency-defined perimeter,
  connected through a dedicated low-latency network
    - Not all features are available in all regions
    - Price might differ betewen regions
    - Paired region, each region is paired with another region for outage
      failover, only one of the pair is updated at a time
  - Availability Zone: physical location within a region, each has its own power
    cooling and networking, minimum of 3 zones per region

Resource Groups
- Not a resource, but a container for resources
- can move resources across resource groups and resource-groups can have
  multiple regions
- can be used for access control
- needs a location for storing its metadata
- removing the resource group will remove all resources within

Azure Resource Manager
- Resources deployed on Azure use ARM (cli, portal, powershell cmdlets)
- easy grouping of resources
- easy tagging for resources (thus easy cost management)

## Chapter 4 - Compute
Virtual Machines
- IaaS, Shares hardware with other VMs
- Pricing is hourly
- Good if you need access to the OS, have existing infrastructure on VMs
- Easily manage and monitor huge amount of VMs through the portal and tools

Scale Sets
- IaaS
- Looks like Auto Scaling Group from AWS
- (Needs|works best with) a load balancer
- Does not cost anything, you pay just for the VMs
- Useful for high-availability and scalability

App Services
- PaaS
- App Services Web Apps
  - Automatically host an application and have azure manage the infrastructure
    for it
  - Three options:
    - Code Web Apps, based on supported programming languages (.NET, Java,
      Node.JS, PHP, Python and Ruby)
    - Container Web Apps, applications are deployed as containers, you can use
      whatever stack you want on them
    - API Apps (need to check)
- Similar to Fargate + API Gateway (?)
- App Service Plan: 

Azure Container Instances
- PaaS
- Primary services for running containers on Azure
- Similar to Fargate

Azure Kubernetes Service (AKS)
- Open source project to manage and orchestrate containers across multiple
  servers
- Can be integrated with Azure Container Registry (ACR)
- Can be integrated with other Identity services from Azure

Azure Virtual Desktop
- Can only use windows OS
- Connect from anywhere (mobile, browser, windows, mac)
- Can re-use existing Windows licenses
- Multiple users can use the same VM

Azure Functions
- Serverless (infrastructure is abstracted away from the user)
- a single function of compute
- A Cloud Guru is serverless

## Chapter 5 - Networking
Virtual Network (VNet)
- Subnets: Allows to segregate the address space
- Beliongs to a single region and subscription
- VNet Peering allows connection two VNets
- Network Security Groups are created independently and are associated at the 
  __Network Interface__ or __subnet__ level

Load Balancer
- Distribute inbound traffic to backend pool instances, according to rules and
  health probes
- Scenarios: internet traffic, internal network traffic, port-forwarding, can
  also be used for outbound connectivity

Virtual Network Gateway
- to create a gateway you need to create a gateway subnet

VPN Gateway
- to traffic between cloud and on-prem
- needs on-prem gateway
- also called site-to-site
- very important for hybrid cloud

Application Gateway
- Can make routing decisions instead of just load-balancing
- can have end-to-end encryption
- can use the same app gateway for multiple sites/backends/machines

Content Delivery Network
- saves temporary copies of files fetched from the "Origin Server"
- distributed network of servers, to deliver content closer to users
- cache and cache invalidation is paramount to CDNs

ExpressRoute
- Gateways that don't go over the public internet, thus are more secure and
  private, also faster and with lower latency

## Chapter 6 - Storage

Introduction
- Storage Account = Unique Azure namespace
  - every obj has its own unique we address
- Types of Storage
  - Blob
  - Disk
  - File
  - Archive

Blob
- Binary Large Object
- You create a blob container
- good for logs, images, video, so forth
- Blob types (important)
  - Block: text and binary up to 4.7TB
  - Append: optimized for append operations, good for logs
  - Page: files up to 8TB, good for Virtual HD
- Tiers: three, in order of cost and frequency of access, high to low
  - Hot
  - Cool
  - Archive

Disk
- Azure manages uptime and backup
- Tiers (costs low to high)
  - HDD: low cost, good for backup
  - Standard SSD: high velocity best for production
  - Premium SSD: super fast, for critical workloads
  - Ultra Disk: up to 64TB, for the most demanding workload

File
- Used to share files across multiple machines and on-prem infrastructure
- Good for hybrid clouds
- To have your file server in the cloud

Archive
- Lowest price
- Low performance, but durable, encrypted, etc
- Good for moving data from higher tiers of storage that are no longer used
- Archive Storage is storage of type "Blob"!

## Chapter 7 - Database
Cosmos DB
- NoSQL
- Multi-region
- Single digit millisecond latency anywhere in the world
- Automatically scaled and managed
- Can get expensive

Azure SQL
- Easily migrate from on-prem SQL to cloud
- suggest optimizations and performance improvements using machine learning
- Two products:
  - SQL Database: fully managed cloud db
  - SQL Managed Instance: Seems like an option to add to your on-prem cluster

Azure Database for MySQL
- Open source
- PaaS

Azure Database for PostgreSQL
- No comment

Database Migration Services
- Dedicated service to help migrate from on-prem MicrosoftSQL to Azure SQL
- Also can help you migrate MySQL or Postgres dbs to Azure SQL, but additional
  steps might be required

