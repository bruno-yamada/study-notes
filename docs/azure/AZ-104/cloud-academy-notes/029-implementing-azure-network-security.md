# Implementing Azure Network Security

## Network Security Groups (NSG)
NSG are deployed to a subscription, resource group and region, 
they are not attached to a VNet, they are instead manually attached to subnets,
so they can be re-used between vNets

Can contain as many security `rules` as required, where rules have:
- a unique name
- priority (number between 100-4096 lower has higher priority)
  - when a request matches a rule, it stops and dont call sub-sequent ones
- source destination
  - single ip, any ip, cidr block, service tag(e.g. vms, vnets), another NSG
- protocol
  - TCP, UDP, ICMP, any
- direction
  - inbound or outbound
- port range
  - specific port or range
- action
  - allow or deny

## Azure Firewall
TODO dig further https://docs.microsoft.com/en-us/azure/firewall/overview

Demo Notes:
- Firewalls are created through the vNet, they don't need a subnet of their own
but it is recommended
- They require an public ip address, but can have up to 100 ips attached
- Route tables are individual resource you can attach to multiple subnets
  - on it he configured all traffic (0.0.0.0/0) to be redirected to the
    firewall ip, meaning it will be controlled by it
- Then, a rule was added to only allow outbout traffic to cloudacademy.com
- and an entry to allow udp requests to the dns server
  (as it needs to query the hostname of cloudacademy.com)

## Application Security Groups
considerations:
- up to 3k per subscription
- can be associanted to more than one vm, and vms can have more than one AppSG

They are associated with an resource/applicaiton (eg. vm), then, in the NSG,
you can actually use the ApplicationSG in a source/destination rule

## Just-in-time provisioning
useful for protecting against brute force attacks because it limits the amount
of time ports are open, enabled by creating __policies__ that states which ports
are open, for how long, and which IPs are allowed

It requires the standard tier of azure security center (not supported in basic)
