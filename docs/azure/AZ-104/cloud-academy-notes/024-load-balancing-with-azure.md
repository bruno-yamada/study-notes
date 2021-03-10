# Load Balancing with Azure

## Load Balancer
- distribute network traffic across multiple resources
- operates at transport layer (layer 4)
- types
  - public
    - internet to machines
	- has public ip
	- can be used to allow outbound traffic to a private vm 
  - internal(private)
    - when no public ip is required
SKUs
- basic
  - up to 300 instances
  - no support for AZ
  - open by default, with optional security groups
- standard
  - up to 1k instances
  - support zone-redundant and zonal frontends for inbound/outbound traffic
  - closed by default, traffic allowed by SGs

Use cases
- redundant applications
- allow private vms outbound traffic

Components (resources within a LB):
- Frontend IP config
- Backend pool
  - pool resources configured to receive traffic from the LB
- Health probes
  - determina if instance is healthy and can receive traffic
- Load balancing Rules
  - eg Allows Port 80 on backend instances
- Inbound NAT rules
  - eg. allow RDP to instances
- Outbound Rules
  - outbound from instance to internet (only on standard tier)

## Application Gateway
- works at layer 7 (application layer)
- like a LB, but with features for dealing with web traffic
- can route based on url and path
- Features:
  - ssl termination
    - offloads encryption
  - autoscaling
    - can autoscale instances based on traffic
  - web application firewal (WAF)
    - can protect agains sql injection, cross site encryption, etc
  - zone redundancy
    - LB can be spanned across many zones
  - session affinity

Components (resources within a Application GW):
- Frontend IP Address
- Backend pool
- Health Probes
- Listeners
  - protocol, port, hostname, ip address
- Routing rules
- Http settings

Demo notes:
- you can enable autoscaling, and min/max scale units
- __requires an subnet dedicated only to application gateways__

## Azure Front Door
- Global LB across regions or even clouds
- has features from Application LB and more
- works at layer 7 (application)
- clients are connected to the closest point
- [details](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-overview)

Demo notes:
- steps: create frontend/domains, backend-pools and routing rules to connect the aforementioned

## Web Application Firewall (WAF)
- protect from common vulnerabilities 
  - SQL injection
  - XSS attack
can be used with:
- Application GW
  - can use for multiple applications (up to 40 sites per gateway)
- Front Door
- CND

Demo notes:
- requires a storage account to store logs and other data
- after you have the application gateway (must be WAF v2 tier):
  - create a WAF policy
    - can be set to detect or prevent mode
	- associante with application GW

## Traffic Manager
a lb that distributes traffic through DNS, usually to distribute across regions, based on client region
- e.g. it resolves the hostname for the client based on the endpoint with the lowest latency

It is not a proxy or gateway, only directs the traffic through dns resolution

Routing methods:
- priority
  - select main, other as backup
- weighted
  - give weight to endpoints to distribute traffic
- performance
  - user to endpoint with lowest latency(region)
- geographic
  - user to closest endpoint
- multivalue
  - if can only use ipv4 or only ipv6
- subnet
  - map ip range to specific endpoints

Demo notes:
- need to create a traffic manager profile
  - select routing method from the list above
  - in the manager profile:
    - create endpoints
