# Advanced VM Management

## VM Scale Sets
- Limits
  - Up to 1k VMs usig azure images
  - Up to 600 VMs with custom images
- group o identical loadbalanced vms
- applications are replicated, increases availability and performance
- requires Load Balancer
- allows maintenance and updates without user-facing downtime
- easy to manage dozens or hundreds of machines
- Networking
  - Layer 4 networking through __load balancer__
  - Layer 7 (traffic rules, ssl termination) through __Azure Application Gateway__
- can be configured to automatically scale in or out as demand flutuates through autoscaling
- new vms are:
  - automatically created from central config
  - automatically integrated to the LB
  - automically distributed through availability zones and sets
  - deployed/scaled automatically based on customizable rules, such as: host metrics, guest metrics, application insights or schedules
- scale sets have no costs (but lbs and vms does)
- __Azure monitor for VMs__ can be used to monitor the vms of a scale set, and autoscale based on those metrics

## Vertical vs Horizontal Scaling
- Vertical (up and down)
  - upgrade or downgrade the VM (ram, cpu)
  - VM must restart (can cause downtime)
- Horizontal (in and out)
  - Add or reduce number of VMs in the scale set
  - Does not cause downtime as it is implied the need of LB

## Configuration Management tools for VMs
- Ansible
  - for VM creation, app deployment, and config management
  - does not require agents/client on the VMs, config is done through ssh
- Chef
  - requires chef client on the VMs
  - client communicates with chef servers to then run the configs on the machine
- Puppet
  - requires agents on the VMs
- Cloud INIT
  - agreed upon way for configuring linux VMs
  - scripts run during the boot process
- Azure Custom script extension
  - can be used for linux and windows vms
  - can also be used for config after the vm is created
- Azure VM extensions
  - can be managed through the Azure Portal
  - to perform post-deployment configs and automation tasks on Azure VMs
  - useful for: antivirus agents, monitoring agents, agents used by puppet or chef
  - can be used on deploy, or after
- Power State Desired State Configuration (DSC)
  - management platform used to specify the config of target machines
  - configs windows natively, or linux through an additional installation
  - to specify which software and config should be done on a VM
  - requires and agent/client on the target VM

## Azure Managed Disks
- types: Ultra disk, Premium SSD, Standard SSD and Standard HDD
- Designed for 99.999% availability
  - each disk has 3 replicas
  - up to 50k VM disks of each type per region per subscription
  - integrated into availability sets and zones
- on-prem VHDs can be uploaded to the cloud
- Server-Side Encryption (SSE)
  - enabled by default for managed disks
  - provides encryption at rest
  - also enabled for snapshots and images (in regions where encryption is available)
- Azure Disk Encryption (ADE)
  - enabled on the OS and data disks of a VM
- More details on __003-storage.md__ previously studied

## Azure monitor for VMs
- Can also monitor on-prem machines
- Must install agent
- The agent is supported on most linux distributions and on windows
- Monitor UI
  - need Log Analytrics Contributor Role for access to the Azure Monitor
  - need Log Analytrics Reader Role, for the VM you want to see in the Monitor

