# Create VM
- Subscription
  - is an isolated part of the account, resources in each subscription are isoleted from one another, and they generate separate invoices within the billing
- Resource Group
  - a smaller group within the subscription
  - you can delete a resource group to delete all of its resources
- Region
  - Geographical regions, some which might contain or not availability zones
  - Availability Zone: if resources are spread across AZs they are assured to remain available in the case of outages
- They also have spot instances (lower cost, but VM might get shutdown with 30sec notice)

# Services

## Azure Migrate
- tool that scans for hyper-v/vmware machines and assesses wether they can be migrated, how much they will cost, instance class, etc.
- can also migrate Microsoft Virtual Desktop
- AD can also be synced to Azure Active Directory

## Azure DevOps
- Azure Pipelines
- Azure DevTest Labs
- Azure CDN

## Azure IoT
- Azure IoT Central
- Azure IoT Hub
- Azure Sphere

## Azure Analytics (wide array of services)
- Azure HDInsight
  - Big data frameworks, hadoop, spark, hive, storm, etc
- Azure Databricks
  - more user-friendly spark option
- Azure Synapse Analytics
  - data warehouse funcionality with support for spark

## Azure AI
- Azure Cognitive Services
  - collection of pre-built easy to use tools
    - decision, language, speech, vision, web search
- Azure Bot Service
  - Tools to create a chat bot
- Azure Machine Learning Studio
  - Easy to use drag-and-drop tool for developt ML
- Azure Machine Learning Services
  - more advance, better for users who know how ML in depth

## Integration
- Azure Logic Apps
  - Can be used to build complex workflows involving not only Azure services but also 3rd party services such as twitter and dropbox
- Azure Event Grid

# Desiging a Solution
- [Azure Architecture Center](https://docs.microsoft.com/en-us/azure/architecture/) has a number of useful links and guides for building your architecture
- TODO study
  - use cases from the architecture center
  - creating cost estimates 
  - Azure cogniive search (appears to be their elasticsearch alternative)
  - Azure Active Directory B2C
  - Queue Storage
  - Azure Function
  - Application Insights

# Managing Services

## Management
- Azure Monitor
  - Log Analytics
  - Metrics
  - Alerts
- Azure Backup
  - Can also be used to backup on-prem systems
- Azure Advisor
  - provides recommendations for savings, security
- Security Center
  - Azure policy
  - Advanced Thread Protection
- ARM Template
  - For creating resources templates
- Azure Blueprints
  - collection of ARM templates

# Summary
- Compute
  - Virtual Machines
  - Azure App Service
  - Azure Kuberntes Service
  - Azure Functions
- Storage
  - Raw Storage
    - Azure Blob Storage
    - Azure File Storage
    - Azure Data Lake Storage Gen2
  - Relational DBs
    - Azure SQL Database
    - Azure SQL Database for MySQL
    - Azure DB for MariaDB
    - Azure DB for PostgresSQL
  - NoSQL DBs
    - Azure Cosmos DB
    - Azure Cache for Redis
- Networking
  - VNet
  - VNet Peering
  - Azure VPN
  - Azure ExpressRoute
- Migration
  - Azure Migrate
  - Azure Active Directory
- DevOps
  - Azure DevOps
  - Azure DevTest Labs
  - Azure CDN
- IoT
  - Azure IoT Central
  - Azure IoT Hub
  - Azure Sphere
- Analytics
  - Azure Databricks
  - Azure Synapse Analytics
  - Azure HDInsight
- AI
  - Azure Cognitive Services
  - Azure Bot Service
  - Azure Machine Learning Studio
  - Azure Machine Learning Services
- Integration
  - Azure Logic Apps
- Management
  - Azure Monitor
   - Azure Security Center
   - ARM Templates (Azure Resource Manager)
   - Azure Blueprints
- Azure Access
  - Web
  - Terminal
  - WebTerminal
  - App
