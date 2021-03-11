# Azure Network Watcher
It is a collection of network monitoring and troubleshooting tools

It needs to be enabled in the region you want to use it on

## Troubleshooting

### IP Flow Verify
Tests if a packet is allowed or not to reach a vm, and by which security rule 
it is being blocked, or where if a packet is being blocked from leaving the vm

It only checks the security groups attached to the selected vm, if you want to
see the other way around you need to swap the selected vm

### Next Hop
It is to check if the connection problem is due to a route problem

### Connection Troubleshoot
When both flow-verify and next-hop fails, you can use connection troubleshoot

It requires the network watcher agent to be installed

You install the agent on the vm, go to the portal, select the vm, and select the
destination and click check

It shows all the hops made through the check, latency and packet loss, sounds familiar?

### VPN Troubleshoot
If an network gateway is involved, you can try using __vpn troubleshoot__

It needs a blob storage container to store the logs

You select the gateway, or the connection and run the check, it can take a while
and you see the results in the portal

## Monitoring
### Connection Monitor
Monitors connection between VM and another VM

Also needs the network watcher agent to be installed on the vm

From __Azure Monitor__ you can configure an alert rule for the connection monitor
configured

### Logs
Network Watcher has 2 logs:
- NSG flow logs
  - what traffic was alloed through an NSG and what was denied
- Diagnostic Logs
  - track both NSG and network interfaces

You enable it on a NSG through the Network Watcher interface, it requires a storage
account, where the log is saved and you can download it to analyse it manually

### Traffic Analysis
Is a dashboard to see what is happening in the network, with drilldown and stuff

You can filter by resource group, subscription, see which vm is receiving more
traffic and if the traffic is blocked os malicious

### Network Performance Monitor (NPM)
Monitor health and performance of specific connections, similar to connection
monitor but with a few extra features

It requires an agent to be installed in at least one vm of each subnet you want
to monitor, it is not installable as an extension, being available instead

Seems to be a deprecated or unsupported old feature, need to dig further, a lot 
of its configuration is  a manual process, contrary to other azure features 
explored thus far

After all the setup is done, you can configure performance monitor rules
