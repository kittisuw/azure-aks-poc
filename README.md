# Demo AKS Support Scaling, HA, DR
##  Prerequisite 
#### 0.1 Create Cluster
```shell
az login
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
#### 0.2 Deploy demo application
```shell
k apply -f hello-abacus-deployment.yaml;
kubectl get deploy;
kubectl get svc;
sleep 10;
EXTERNAL_IP=$(kubectl get svc hello-abacus-service --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}")
echo "http://$EXTERNAL_IP/";
```
and test service http://\<external-ip\>
## 1. Scale down Cluster from 3 nodes to 1 node
> prove HA and DR
```shell
k get node -o wide
k drain <node> --ignore-daemonsets --delete-local-data
k cordon <node>
```
and Test service http://\<external-ip\>
## 2.1 Manual Scale out node from 1 to 3
> Prove Infra Scaling
```shell
az aks scale \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 3
```
and Test service http://\<external-ip\>

## 2.2 Auto Scale node by using Cluster Auto Scaller
```shell
#Scale Down to 1 node Back
set min 1 max 3
# Genarate workload

```
and Test service http://\<external-ip\>
## 8. Cleanup
```
az aks delete --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster --yes --no-wait && \
kubectl config delete-context abacus-poc-Cluster && \
kubectl config delete-cluster abacus-poc-Cluster
```