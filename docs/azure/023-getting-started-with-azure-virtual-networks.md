# getting started with azure virtual networks

# Concepts and Best Practices
- can connect to on-prem network through vpn, with other VNets and to the internet
- Key Concepts:
  - Address Space
    - public or private address taht conforms to the RFC 1918 standard
    - must be ensured to not overlap with your own private network
  - Subnets
    - segmentations of your VNet
    - allows better control and security (network security groups)
    - never create a subnet with the entire address space
    - but also avoid creating subnets too small, to avoid management overhead
    - in summary, plan ahead
  - Regions
    - each vNet is in a Region
    - can connect vNet in different regions using peering
  - Subscriptions
    - can have more than one vnet per subscription and subscriptions can span over more than 1 region
- Network Security Group:
  - filter traffic to and from azure resources attached to a vNet
  - allow/deny inbound/outbound traffic

# Communications
- to allow incoming traffic, assign an public IP address or assign public LB
- service endpoints
  - allows extension of vNet resource address to some __Azure resources__ through a direct connection
- vNet peering
  - connect 2 virtual networks
  - types to use when connecting to on-premises vnet:
    - point-to-site vpn
      - single computer on-prem TO vNet
      - each computer needs a connection
      - often use to connect remote workers to vNet to get access to private resources, through encrypted tunneling
    - site-to-site vpn
      - on-prem network TO vNet
      - vNet's AzureVPN gateway TO on-prem VPN device
    - ExpressRoute
      - private conenction between on-prem and azure environment
      - traffic __does not flow over the internet__
      - requires an ExpressRoute partner

# Filtering, Routing and Integration
Filtering:
- Through security groups 
  - Network SG
    - assigned to single NIC or entire subnet
  - Application SG
    - __logically__ groups NICs from resources on a vNet
    - rules are applied only to the grouped NICs
- Through network virtual appliance (NVA)
  - sorta like an VM that executes a specific function 
  - Firewalls, WAN optimization
  - e.g.
    - Barracuda CloudGen WAF for Azure
    - Citrix SD-WAN Center
    - pfSense
Routing:
- Route Tables
  - custom routes to control how traffic is routed for each subnet
- BGP Routes (BorderGatewayProcotol routes) 
  - when connecting azure vNet to onPrem through:
    - ExpressRoute 
    - Azure VPN Gateway
  - [details](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-bgp-overview?toc=/azure/virtual-network/toc.json)
Integration:
- [Private Link](https://docs.microsoft.com/en-us/azure/private-link/private-link-overview)
  - allows to privately access service/vm from another vNet or on-prem network
- [Service endpoint](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoints-overview)
  - allows connection between you vNet and Azure services through a direct link

# VPN Gateways
- types: point-to-site, site-to-site, ExpressRoute
- after you can create an IPSec or IKE VPN tunnel
- can also be used between azure vNets
- one gateway per network, but multiple clients can connect
- is deployed to the selected subnet
  - is actually 2 hidden vms with routing tables and gateway services
- [further details](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways )

# Site-to-Site VPNs
- connects on-prem to vpn-gateway through IPSec or IKE VPN tunnel
- client's VPN device must have public address
- process
  - create vNet
  - create gateway subnet (required, it is a special type of subnet)
  - create gateway (can take up to 45min)
  - create local network gateway
    - has the on-prem network address space and on-prem vpn device public ip
  - config on-prem vpn device
  - deploy vpn connection in azure (to the on-prem vpn device)
    - site-to-site, vNet-to-vNet, ExpressRoute
	- select vNet Gateway and local network Gateway
	- fill in connection name, shared key (PSK) and protocol (IKEv1|IKEv2)
- Demo notes:
  - vNet gateways have their own tiers(SKU), which dictates performance(and pricing)
  - the gateway will have a public ip assigned to it (you can give it a name at the creation screen: eg GatewayIP)
  - you can download a config file from the configured connection in azure for some popular vpn devices

# Point-to-Site VPNs
- connect single client to vNet
- protocol options:
  - OpenVPN
    - SSL/TLS protocol, (uses port 443) and can go through firewalls(since most firewalls allows https 443 outbound)
	- windows, linux and macos
  - Secure Socket Tunneling Protocol (SSTP)
    - proprietary, also uses TLS
	- only in windows
  - IKEv2 VPN
    - for MacOS
- Authentication
  - Azure Certificate
    - certificate must be on client device
  - Azure AD authentication
    - uses AD credentials, only works for OpenVPN
	- Windows10 requires AzureVPN client
  - Traditional AD domain server
- [details](https://docs.microsoft.com/en-us/azure/vpn-gateway/point-to-site-about)

# ExpressRoute
- direct connection from on-prem to azure through third party provider
- this connections do not traverse public internet
  - more secure, reliable and faster
- allows connection to cloud services across all regions within a geopolitical region
- global connectivity achieved through premium add-on
- dynamic routing between on-prem and microsoft through BGP
- high reliability (SLA 99.95%)
- you purchase ExpressRoute circuits ranging from 50Mbps to 10Gps
- Billing Models
  - unlimited data (inbound and outbound, with monthly fee)
  - metered data (monthly fee, inbound free, outbound is priced per GB)
  - premium add-on
    - route limits for public and private peering increased from 4k to 10k routes
	- global connectivity
	- number o vNet links per circuit increased from 10 to number based on the circuit tier

# VNet peering
- connect 2 azure virtual networks
- types:
  - vNet peering
    - connect vNets within same region
  - global vNet peering
    - connect vNets in different azure regions
- offers low latency and high bandwith between vNets
- available between different subscriptions, AD tenants, and Azure regions
- the process has no downtime
- traffic remain private
- latency between vms within the same region in different vNets through peering is the same as if the vms were in the same vNet

# Notes
- in your vNet you can also:
  - provision an Azure managed Bastion Hosts that you can create in the vNet (and connect to them over ssh through the Portal)
  - use manage DDoS protection, __basic__(builtin) and __standard__(paid, can get expensive) options available
  - use Azure Firewall
