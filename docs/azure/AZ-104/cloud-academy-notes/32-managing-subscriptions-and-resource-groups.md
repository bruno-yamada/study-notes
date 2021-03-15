# Managing Azure Subscriptions and Resource Groups

## MANAGING AZURE SUBSCRIPTIONS
### Overview of Key Roles
Four key roles worth noting(but there are many more):
- Contributor
  - access to manage resources, but cannot assign permission to other users
- Owner
  - access to manage resources, but can also assign roles to other users
- Reader
  - can view resources, but not manage them
- User Access Administrator
  - Can only manage users, but not resources
 
[more details about built-in roles](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)

Notes:
- You can add owners to subscription, or resource group levels
- Account Administrator technically lacks access to Azure Services 
- You can add co-administrators to your subscription (only needed if using 
classic azure deployments)

### Configure Subscription Policies
Policies can be edited and monitored, there are many built-in policies, e.g:
- System updates: checks systems for missing security and critical updates
- Disk encryption: recommands enabling disk ecnryption in VMs to enhance data
protection at rest
- [additional built-in policies](https://docs.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies)

## MANAGING RESOURCE GROUPS
## Overview of Resource Groups
Useful for logically grouping resources, making it easy to manage and control cost

They can be spread across multiple regions (as there is multi-region resources)

## Resource Policies
They can be used to control:
- SQL Server required versions
- allowed storage account SKUs
- disable certain resources from being deployed
- and much more

Policies evaluation happens about oncea every hour

## Resource Locks
To save resources from being deleted or modified without authorization, you can
set the lock to __ReadOnly__ and __CanNotDelete__

Resources can also be used in a parent scope and all childs will be affected
e.g.:
- applying resource lock to a resource will affect all its resources

be careful as giving ReadOnly to storage account can have unexpected effects and some actions
might surprisingly require write access to work

By default, only `Owner` and `User Access Administrator` are allowed to create
resource locks

## Implement and Set Tagging on Resource Groups
- You can filter tags across resource groups
- They are limited to 15 per resource
- Up to 512 characters to tag names, and 256 to tag values
- Can't contain special characters

## Moving Resources across Resource Groups
- Both resource groups are blocked when moving the resources
- Moving the resource doesn't actually change the resource region
- both subscriptions must exist in the same AzureAD tenant
- subscription quota must be enough to receive the resource

