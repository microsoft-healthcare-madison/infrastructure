https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm#create-a-service-account

https://docs.microsoft.com/en-us/azure/aks/ingress-tls


```
BAD:
  --version v0.8.0

GOOD
  --version v0.8.1
```

```
az ad sp create-for-rbac --name ArgoSubscriptionsDeploy
./linux-amd64/helm install nginx-ingress charts/stable/nginx-ingress --namespace subscriptions-ri --set controller.replicaCount=2
kubectl apply -f  ui.service.yaml
kubectl apply -f  ui.deployment.yaml
az ad sp credential reset  --name  ArgoSubscriptionsDeploy
```
