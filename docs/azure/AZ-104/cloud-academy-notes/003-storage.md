# Storage
- durable, highly availability built in
- encryption
- scalability
- access through powershell cli, portal and storage explorer

## Blob Storage
> https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction
- text and binary data for example, images, video, audio, documents, bkp data, archived data, etc
- HTTP, HTTPS, Azure Rest API, powershell scripting, Azure CLI, or languages SDK
- hierarchy:
  - Storage Account 
    - Container 
      - Blob
- Blob types
  - Block - up to 4.7tb of text and binary
  - Append - blocks of data good for logging, optimized for append
  - Page - storage random access files up to 8TB

## Azure Files
- fully managed file share system, 
- can access through SMB
- can use Azure file sync to cache files in on-prem servers
- can now completely replace tradicional on-prem file servers and even NAS devices
-  can be used with Azure files AD authentiction
- no longer need to worry about power outages, redundancy, etc, for on-prem file servers
- created through `File shares`
  - can set a quota
  - seems to be limited to 5tb per file share, needs to check

## Azure Queues
- For storing messages, acccessible throuigh HTTP or HTTPS
- 64kbs max
- queue name and storage account name used to build the URL

## Azure Tables
> there is a "premium" table experience provided through CosmosDB
- Structured NoSQL data
- key/attribute store Schema-less
- contain many tables, each table can have many entities
  - each entity can have up to 1mb
    - each entity have many properties
      - up to 252 properties per entity

## Azure Managed Disks
- managed Storage volumes
- used to provide storage to VMs
- Designed for 99.999% availability
- Types:
  - Ultra Disks - Premium SSD - Standard SSD - Standard HDD
- They are integrated with availability set (within datacenter)
- They are integrated with availability zones (datacenters)
- Only up to 4tb
- Easy to upload vm VHD disk to cloud
- Encryption
  - Server Side encryption (SSE)
  - Azure disk encryption(ADE) (through BitLocker in Windows and DM-crypt in linux)
- Disk roles
  - Data: attach to VM, registered as SCSI driver (up to 32tb, qtd of disks depend on instance class)
  - OS: deploys along with the VM, max 4tb
  - Temporary: not a managed disk, (host page files and swap files, lost upon redeploy or restart)
    - D: on windows, /dev/sdb on linux
- [Disk Types](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-types)
  - Ultra:
    - High IOPS, data intensive workload, high-end databases, transaction heavy workloads
    - can only be used as Data disks, not OS
    - 4tb to 64tb, 300IOPS per GB, up to 160.000 per disk
    - supports only a handful of regions and VM series, can only be created as empty disks and do not support snapshots, vm images, disk encryption, etc
  - Premium SDD:
    - throuput is guaranteed
    - high performance low latency for VM
    - recommended for IO intensive workloads
    - can only be used for VMs that support premium storage
    - Bursting
      - supported on disks smaller than P30
      - burst IOPS to 3500 and bandwidth to 170 Mbps
      - credits accumulates into a __burst bucket__
      - useful for disk boot, or uneven trafic
      - automatically enabled on disks qualified for such
  - Standard SDD:
    - for VM that do not require high IOPS provider by ultra or premium
    - more affordable and better availability and performancethan HDD
  - Standard HDD:
    - backedup bu magnetic drives
    - low cost disks
- Snapshots
  - copy of specific disk at specific point in time (only one disk, if vm has more than one, it will only snapshot selected disk)
- Snapshots x Images
  - snapshot is only of one disk
  - Images includes all disks attached to the vm

## Storage Accounts
- [storage-account-overview](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)
- [replication & storage-redundancy](http://docs.microsoft.com/azure/storage/common/storage-redundancy)
- logical Container that can hold blobs, files, queues tables and disks, must have a unique global name
  - that unique name is prepended to the URL
- Types:
  - General Purpose V2 (most recommended)
  - General Purpose V1 (legacy)
  - Block Blob Storage
    - Premium performance for block bobs and append blobs
  - File Storage
    - High performance, file only, recommended for enterprise 
  - Blob Storage (legacy)
    - used for blob-only, better to use General Purpose V2 instead

# to check
- disk redundancy and SLA
