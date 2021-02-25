# Configuring Azure Kubernetes Service (AKS)
- facilitates scalability
- more aligned with the move to microservices
- easy to manage huge amounts of containers

# Authentication
Cluster Infrastructure
- Authentication Method
  - Service Principal
  - System-assigned managed identity (sorta automatic, due to the keyword "managed")
Authentication and Authorization
- Role-Based access control RBAC (enabled/disabled)
- AKS-managed Azure Active Directory (enabled/disabled)
  - allows access by giving access to a particular group in AD, requires extra steps

# Integrations
- Can be integrated with Azure Container Registry
- Can be integrated with Azure Monitor
  - can enable container-level monitoring

- Azure CLI has the kubectl cli integrated
```
# get kubeconfig credentials
az aks get-credentials --resource-group aks1 --name cluster1

# then you can use the cli normally
kubectl get nodes
```

