# Demo AKS Support Scaling, HA, DR
## 0. Login
```shell
az login
```
## 1. Create Cluster
```shellell
az aks create \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 3 \
  --node-vm-size Standard_D2s_v3 \
  --zones 1 2 3 \
  --generate-ssh-keys;
az aks get-credentials --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster;
k get node -o wide;
```
## 2. Deploy demo application
```shell
k apply -f hello-abacus-deployment.yaml;
kubectl get deploy;
kubectl get svc;
sleep 5;
EXTERNAL_IP=$(kubectl get svc hello-abacus-service --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}")
echo "http://$EXTERNAL_IP/";
```
## 3. Test service http://\<external-ip\>
## 4. Scale down Cluster from 3 nodes to 1 node
> prove HA and DR
```shell
az aks scale \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 1
```
## 5. Test service http://\<external-ip\>
## 6. Scale up Cluster from 1 node to 3 node Back
> Prove Infra Scaling
```shell
az aks scale \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 3
```
## 7. Test service http://\<external-ip\>
## 8. Cleanup
```
az aks delete --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster --yes --no-wait && \
kubectl config delete-context abacus-poc-Cluster && \
kubectl config delete-cluster abacus-poc-Cluster
```