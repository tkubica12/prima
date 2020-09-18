# Uvod do kontejneru

## Clanky
[O kontejnerech, cloudu, vesmíru a vůbec se zvláštním zřetelem na Kubernetes](https://www.tomaskubica.cz/post/2017/o-kontejnerech-cloudu-vesmiru-a-vubec-se-zvlastnim-zretelem-na-kubernetes/)

[https://www.tomaskubica.cz/tag/kontejnery/](https://www.tomaskubica.cz/tag/kontejnery/)

## Knizky

[The Developer’s Guide to Azure](https://azure.microsoft.com/en-us/campaigns/developer-guide/free/)

[APIs + Microservices](https://azure.microsoft.com/en-us/resources/apis-and-microservices/)

[Get up and running with Kubernetes](https://azure.microsoft.com/en-us/resources/kubernetes-ebook-collection/)

[Containerize Your Apps with Docker and Kubernetes](https://azure.microsoft.com/en-us/resources/containerize-your-apps-with-docker-and-kubernetes/)


## Skoleni a prakticke scenare

[Deploy a website to Azure with Azure App Service](https://docs.microsoft.com/en-us/learn/paths/deploy-a-website-with-azure-app-service/)

[Introduce DevOps Dojo: Create efficiencies that support your business](https://docs.microsoft.com/en-us/learn/paths/devops-dojo-white-belt-foundation)

[Create serverless applications](https://docs.microsoft.com/en-us/learn/paths/create-serverless-applications/)

[Connect your services together](https://docs.microsoft.com/en-us/learn/paths/connect-your-services-together/)

# Lab
## Build containers

```powershell
git clone https://github.com/tkubica12/kubernetes-demo

$rgName = "kubica-rg"
$registryName = "kubicakontejnery"

az group create -n $rgname -l westeurope
az acr create -n $registryName -g $rgname --sku Standard --admin-enabled  
az acr build -r $registryName --image mujweb:v1 ./kubernetes-demo/web/version1
az acr build -r $registryName --image mujweb:v2 ./kubernetes-demo/web/version2
```

## Run containers as container instance in Azure 

```powershell
$acrUsername=$(az acr credential show -n $registryName --query username -o tsv)
$acrPassword=$(az acr credential show -n $registryName --query passwords[0].value -o tsv)

az container create -n mujweb `
    -g $rgname `
    -e "PORT=80" `
    --image "$registryName.azurecr.io/mujweb:v1" `
    --registry-password $acrPassword `
    --registry-username $acrUsername `
    --ip-address Public
```

## Use PaaS service with containers

1. Create WebApp
2. Add slot for v2 and show A/B testing
3. Scale out - show balancer via curl
4. Add 10% and show canary
5. Swap slots to move v2 to production
6. Add authentication via AAD

## Kubernetes
Create cluster via GUI

Get credentials

```powershell
az aks get-credentials -n $registryName -n $rgname --admin
```

Apply deployment via GUI

Delete Pod

```powershell
kubectl get pod
kubectl delete pod myweb-deployment-5b568b9f67-fsxnk
```

Use GUI to change single pod label or:
```powershell
kubectl edit pod myweb-deployment-5b568b9f67-tctnl   # Change label
```

Access app via tunnel

```powershell
kubectl port-forward pod myweb-deployment-5b568b9f67-z45pc 12345:80  # Click on open port in cloud shell
```

Apply service via GUI

Apply deployment via GUI and change image tag to v2

Deploy Application Insights and monitor.yaml

