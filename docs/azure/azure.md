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
