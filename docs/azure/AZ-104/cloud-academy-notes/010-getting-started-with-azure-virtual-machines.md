# Getting started with Azure Virtual Machines

# Getting started with VMs
- What is a VM
  - an on-demand scalable compute resource
  - Flexibility of virtualization without the need to buy/maintain physical hardware
  - still needs to do server patching, installation and config
  - easy to setup, so good for dev/test envs, with pay-as-you-go
  - also used to extend on-prem data centers to MicrosoftAzure
- Things to consider
  - vm naming convention
  - deployment location
  - vm sizing requirements
  - quantity of VMs needed
  - OS requirements
  - VM configuration
- Related resources
  - Resource Group
  - VNet (Virtual Network)
    - VNIC
  - if using unmaged disks, also need StorageAccount
  - for remote access, need public ip address
  - data disks can be attached to extend storage
- Pricing
  - pay-as-you-go
    - pay by second
    - no long-term commit
    - no upfront payment
  - reserved vm instances
    - advanced purchase
    - commit for 1 or 3 year
    - upfront payment, up to 72% cheaper than pay-as-you-go
    - best if have steady-state usage
  - spot pricing
    - purchase unused compute capacity
    - upt to 90% cheaper than pay-as-you-go
    - workloads must tolerate interruptions
    - good if running interruptible applications
- VM Options - [linux](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)
  - General Purpose (balanced)
  - Compute optimized (+cpu)
  - Memory optimized (+memory)
  - Storage Optmized (higher disk throughput and IO)
  - GPU (heavy graphic rendering and video editing)
  - High Performance Compute (to handle high performance compute, optional high throughput network)

# High Availability Features
- Availability sets
  - protect apps from hardware failure within a azure datacenter
  - 2+ vms in an availability set = VM SLA of 99.95% uptime
  - 1 VM in an availability set = 99% uptime (if premium SSD or ultra-disk for all OS disks and data disks attached to the VM)
  - when using availability sets, that are a few things to consider:
    - update domain:
      - an availability set with 2 update domains means:
      - 2 vms, 1 per update domain, which are given update-domain numbers of UD-1
      - when and maintenance update occurs, it is executed one vm at a time, ensuring one of the vms with the id UD-1 is always available
    - fault domain:
      - machines that shares a common power source and network switch
  - using availability sets provides VM in 2 to 3 fault domains, which means you are protected from powersource and network outages within a data center
- Availability Zones
  - protect apps from complete failure of a azure datacenter
  - unique physical location (datacenter) within an azure region
  - 99.99% SLA
  - deploying 
- Availability region
  - at least 3 azure zones completely physically separeted

# notes
- vms deallocated dont result in compute charges
  - vms stopped still incurr charges
- when deallocated, dynamically assigned IPs are lost
- can use static IPs to avoid it
- if you restart a vm, it does not lose dynamically associante IPs
- when you stop through the portal, it sends the signal to the vm guest os, which attempts to do the shutdown routine associated, thus performs a graceful shutdown
- redeploy function (portal)
  - migrates the VM to a new host(node), thus changes the underlying hardware
  - causes outage
  - useful for some types of connectivity problems
- vm resizing
  - if the vm uses premium storage, the new size also needs to allow it
    - premium storage is identified by the `S` in the size name (e.g. DS2)
  - if resize to size not available in the underlying cluster, the vm needs to be deallocated
    - if on a availability set, all vms needs to be deallocated
    - troublesome if using dynamically assigned ips and dont want those ips to change
