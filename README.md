# Demo AKS Support Scaling, HA, DR
##  Prerequisite 
#### 0.1 Create Cluster by Fix 3 Worker nodes
```shell
az login
az aks create \
  --resource-group abacus-poc-jib-rg \
  --name abacus-poc-Cluster \
  --node-count 3 \
  --node-vm-size Standard_B2s \
  --zones 1 2 3 \
  --generate-ssh-keys;
az aks get-credentials --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster;
k get node -o wide;
k top node;
```
#### 0.2 Deploy Demo Application
```shell
k apply -f hello-abacus-deployment.yaml;
k get deploy;
sleep 10;
EXTERNAL_IP=$(kubectl get svc hello-abacus-service --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}")
k get svc;
echo "http://$EXTERNAL_IP/"
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
#Scale Cluster Auto Scaller min 1  max 3
az aks nodepool update \
  --resource-group abacus-poc-jib-rg \
  --cluster-name abacus-poc-Cluster \
  --name "nodepool1" \
  --min-count 1 \
  --max-count 3 \
  --enable-cluster-autoscaler
# Genarate workload

```
and Test service http://\<external-ip\>
## 8. Cleanup
```
az aks delete --resource-group abacus-poc-jib-rg --name abacus-poc-Cluster --yes --no-wait && \
kubectl config delete-context abacus-poc-Cluster && \
kubectl config delete-cluster abacus-poc-Cluster
```