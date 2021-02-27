# Getting Started with Azure App Service

More details [here](https://azure.microsoft.com/en-us/services/app-service/)

> A cloud-based web hosting service

Maybe it is analogous to heroku

With Azure App Service you can:
- Authentication and Authorization through Azure AD
- Application Insights for monitoring
- Configure automatic backups
- Setup a custom domain name
- Configure TLS/SSL
- Config and customize network
- Scale up or down your app
- Run background jobs based on triggers, schedules or manual triggers
- setup alerts (e.g. too many http 500 errors)
- setup push notifications (for mobile apps)

Service Plans:
- linux not supported on shared tier
- cannot run windows and linux in the same Service Plan
- Cannot mix windows and linux in the same region+resource-group combo
- resources are shared by apps in the same service plan
- plan tier dictates:
  - how many apps are supported (free:10, standard:100)
  - what features are available (e.g. autoscaling, deployment slots)


Supported frameworks and languages:
- Ruby
- PHP
- Python
- NodeJS
- Java
- DotNet Core
- DotNet
- Docker containers

When creating a simple webapp (using provided template from the azure portal)
- you need to select whether you are using __code__ or a __docker container__
- if you use code, you need to select the runtime task from the above supported languages and frameworks
- you also select the operating system and the usual region, resource group, etc
- you select the App Service Plan
  - Which is sorta like the hardware tier for your app
  - Apps in a plan, all share the same resources (thus they share performance and can impact one another, but can be cheaper)
  - Different plans do have some distinct features

In Azure App Services, Deployment Center:
- it seems to be integrated with Azure DevOps but I need to investigate it further
- you configure CI/CD from:
  - Azure Repos
  - Github
  - Bitbucket
  - local git (custom git)
- you can make __manual deployments__ from:
  - OneDrive
  - Dropbox
  - External GIT
  - FTP

Using containers:
- can be done with an Azure Container Registry (can also use docker hub or other registry)
- you select docker instead of code and select your image:tag

Limitations:
- AppService on linux is not supported on shared pricing tier
- can mix windows and linux on same service plan
- in the same resource group, cannot mix windows and linux in the same region (really specific huh)
- code deployed to built-in images go to an storage volume slower than the container main storage, meaning that to get better storage speed you should use a custom container, thus the code will be on the main storage instead of an attached slower volume

Functionality and Configuration
- Scaling Up
  - you service plan dictates your available features, auto-scaling is one of them and is not included in the lowest plan tiers
  - you can scale it up and down manually or based on custom rules based on metrics such as CPU, tcp, disk IO

Configuration
- Application Settings  (ENV vars)
- General Settings (mostly related to platform, runtime, pipeline, http, web socket, etc)

Authentication and Authorization
- Can be configured through the portal, integrated with AzureAD, Microsoft, Google, Facebook, Twitter
- works really well

Networking
- common networking tasks
  - ip whitelisting
  - run on isolated vpc
  - inbound and outbound rules
  - connect to other VNETs
  - hybrid connection
    - e.g. you can connect azure app services to an db running on-prem (requires an agent)

Monitoring
- App Service Logs
  - Can collect web server logs, error messages, etc
  - can select log level
- Metrics
- Alerts

API Documentation
- wtf cloudacademy module just glances over it showing too much code and not much of a reason or how it is useful in AppServices

Mobile APIs
- Azure used to have and section specific to mobile apis, but is has been decomissioned since apis can be consumed by both web and mobile, thus mobile apis were redundant and under-utilized

Deployment Slot
- Useful for staging or UAT versions of the app
- plan must be S1 or higher (production tier)
- useful for blue-green and canary
- you can select % of users to go to a deployment slot

Service Plan tiers:
- Free (Windows only): Run a small number of apps for free
- Shared (Windows only): Run more apps and provides support for custom domains
- Basic: Run unlimited apps and scale up to three instances with built-in load balancing
- Standard: The first tier recommended for production workloads. It scales up to ten (10) instances with Autoscaling support and VNet integration to access resources in your Azure virtual networks without exposing them to the internet
- Premium: Scale up to 20 instances and additional storage over the standard tier
- Isolated: Scale up to 100 instances, runs inside of an Azure Virtual Network isolated from other customers, and supports private access use cases
