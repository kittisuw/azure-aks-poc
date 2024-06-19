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
k get node -o wide;
```
# 2. Deploy demo application
```sh
k apply -f hello-abacus-deployment.yaml;
kubectl get deploy;
kubectl get svc;
EXTERNAL_IP=$(kubectl get svc hello-abacus-service --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}")
echo "http://$EXTERNAL_IP/";
```
# 3. Test service http://<external-ip>
# 4. Scale down Cluster from 3 nodes to 1 node
```sh
az aks scale \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 1
```
# 5. Test service http://<external-ip>
# 6. Cleanup
```
az aks delete --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster --yes --no-wait;
kubectl config delete-context abacus-poc-Cluster;
ktx;
```