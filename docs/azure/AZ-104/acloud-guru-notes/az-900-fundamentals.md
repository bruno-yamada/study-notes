# acloud.guru Azure fundamentals

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

Deploying Your First Azure Virtual Machine
Scale Sets
App Services
Azure Container Instances
Azure Kubernetes Service
Azure Virtual Desktop
Functions
Demo: Azure App Services
Summary
AZ-900 Microsoft Azure Fundamentals 2020 - Compute Quiz
