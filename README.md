# madison-infrastructre
Home for controlled versions of documents used in the Madison Healthcare sandboxes.

# Provision AKS cluster

Parameters for ARM deployment at [`cluster-arm-template/`](./cluster-arm-template/).

# Configure nginx ingress

Based on: https://kubernetes.github.io/ingress-nginx/deploy/#quick-start

```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

Then follow https://serverfault.com/a/1110814 to

```
kubectl -n ingress-nginx edit service ingress-nginx-controller
# ensure that the following annotation is present
# service.beta.kubernetes.io/azure-load-balancer-health-probe-request-path: /healthz

```

# Configure Cert Manager

Based on: https://cert-manager.io/docs/installation/

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.9.1/cert-manager.crds.yaml
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.9.1/cert-manager.yaml
kubectl create --edit -f https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/production-issuer.yaml
```

In this final `--edit` session:

* change `Issuer` to `ClusterIssuer`
* change email

# Set up public IP for cluster

Based on: https://docs.microsoft.com/en-us/azure/aks/ingress-tls?tabs=azure-cli



```
IP={{external IP from `kubectl  -n ingress-nginx get service`}}
# Name to associate with public IP address
DNSNAME=argo-cluster-2022

PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
```

# Set up Service Principal for GH Actions deploymnets

Based on: https://github.com/marketplace/actions/azure-login#configure-a-service-principal-with-a-secret
