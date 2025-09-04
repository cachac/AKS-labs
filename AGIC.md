# 1. Info. Configurar AGIC (Application Gateway Ingress Controller)
> [Link](https://learn.microsoft.com/en-us/azure/application-gateway/ingress-controller-install-new)

>[Link](https://www.youtube.com/watch?v=ULXhozeoH0U)

# 2. Crear resource group

# 3. Crear el VNET
- cidr: 10.0.0.0/16
- subnet aks: 10.0.0.0/24
- subnet appgw: 10.0.1.0/24
# 4. Crear Cluster
- Seleccionar vnet y subnet aks
- kubernetes cidr: 176.16.0.0/24
- Networking: Azure CNI Node Subnet (addon is not supported with Azure CNI Overlay)

# Crear Application Gateway
- VNET
- Subnet appgw
- Frontend:
  - agregar public IP
- Backends:
  - name: backends
  - Add backend pool without targets: Yes
- Config: Add a routing rule
  - name: default
  - priority: 100
  - listener tab
    - name: default
  - backends targets
    - target: backend
    - settings: new - name: settings
  - add
- Review and create

# Habilitar AGIC en el cluster

appgwId=$(az network application-gateway show -n <appgwname> -g <resourcegroupname> -o tsv --query "id")
echo $appgwId
az aks enable-addons -n <aksclustername> -g <aksresourcegroupname> -a ingress-appgw --appgw-id $appgwId

# Crear Ingress y Deployment

```yaml
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true" # Opcional para dirigir el trafico https

```

# Validar Application Gateway Backen pools
- Backend pool FQDN
