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
