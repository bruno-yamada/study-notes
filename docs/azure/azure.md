# Notes
# Azure CLI
- directory `clouddrive` is persisted across cloud cli sessions
- from the cloudshell you can expose a port and open it in your browser, cloudshell takes care of everything

# Azure Container Registry
- You can use the cli to build containers in the cloud (billed by build-second at a flat rate)
```
# Store ACR name
acr_name=$(az acr list --query [0].name -o tsv)
# Run an ACR quick task to build image and push to app/tetris repository in ACR
az acr build -t app/tetris:1.0 -r $acr_name .
```

# Azure Kubernetes Service
- You can also use it with Azure Container Instances (serverless)
- you can check and manage resources through the portal (services, ingress, etc)

# Visual Studio
- really well integrated with azure
- you can even create azure stuff from visual studio
- also well integrated with docker (you can select it as a runtime for your app from the IDE)

# Extra
- [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
- Might be good to study the limitations and FAQs of each topic for the exam
- Azure App Services  might be an important topic not explored enough in the course
