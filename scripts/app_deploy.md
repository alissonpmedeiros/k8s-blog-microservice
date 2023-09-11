# Manual app deployment on AKS cluster

# Check you still have ui container image from earlier task
docker images

# Connect to Azure
az login

# Login to ACR, where <acrName> is the name of the ACR created witha  random ID. you can get it from the azure portal
az acr login --name <acrName>

# Get ACR server address
az acr list --resource-group aks-rg --query "[].{acrLoginServer:loginServer}" --output table

# Tag the local copy of the UI Container Image
docker tag ui:v1.0 acr12601.azurecr.io/ui:v1.0

# Confirm new UI image with acr tag exist
docker images | grep ui -w

# Push image to ACR
docker push acr12601.azurecr.io/ui:v1.0

# An alternative option would be importing the image directly from your dockerhub
# az acr import --name acr_id --source docker.io/user_name/image:tag --image image:tag --username your_username --password your_password

# Verify the image is in ACR
az acr repository list --name acr12601 --output table

# Connect to AKS
az aks get-credentials --resource-group <resourceGroupName> --name <AKSClusterName>

# Verify that you are connected
kubectl get nodes

# Apply UI Manifest to the AKS cluster. Remember to update the acr id into demo-ui.yaml script
kubectl apply -f demo-ui.yaml

# Verify pod is deployed for UI
kubectl get pods -n ns-posthub--env

# See every related pod/service/deployment etc in this namespace
kubectl get all -n ns-posthub--env

# Describe a UI pod
kubectl describe pod <uiPodName> -n ns-posthub--env

