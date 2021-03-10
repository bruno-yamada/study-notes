# Azure Network connectivity and name resolution

## Virtual Network Peering
- max 500 per vNet
- on peerings, from the VNet interfaces
- can be done across regions or subscriptions
- cannot do if vnets have overlapping address space
- cannot change address range after peering is created(can remove the peering, make modifiction and recreate peering)
- when you have multiple VNEts to connect to one another, it might be easier to have all of them connect to a "HUB"
and the HUB forwards traffic to the other networks, this way each VNet would only need to have 1 peering

## Virtual Network Gateways
- takes a long time to create
- require a dedicated subnet, usually /28
- creates 2 vms to act as gateway, one to be active and 1 as fallback
- is created to be a vpon gateway or expressRoute
- networks connects to it using IPSec or IKE VPN tunnels
- can also be used to connect 2 VNEts

## Azure DNS
- work mostly like AWS Route53 
- VNEts that dont autoregistration enabled are called resolution VNet

## Private Domains
  - need to link the dns zone with the VNet for it to work 
  - can be linked to more than one VNet
  - if you enable auto-registration in the link, VMs will automatically create a record in the zone whenever they are created
    - if you have more than one MV it will happen to vms created on both
  - you can have a public and private dns zones, for when you have different version of the application depending on internal/external users (it is called "Split horizon")
