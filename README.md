# abacus-aks-poc
# 0. Login
```sh
az login
```
# 1. Create Cluster
```sh
az aks create \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 3 \
  --node-vm-size Standard_D2s_v3 \
  --generate-ssh-keys;
az aks get-credentials --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster;
```
# 2. Deploy demo application
```
k apply -f hello-abacus-deployment.yaml
kubectl get deploy
kubectl get svc
```
# 2. Scale down Cluster
```sh
az aks scale \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 2
```
# 2. Cleanup
```
az aks delete --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster --yes --no-wait;
kubectl config delete-context abacus-poc-Cluster;
ktx;
```