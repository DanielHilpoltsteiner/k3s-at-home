# Deploy cert-manager in a Kubernetes cluster

## Install cert-manager using Helm

Create a namespace for cert-manager

```bash
kubectl create namespace cert-manager
```

Create the CRDs for cert-manager

```bash
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.2.0/cert-manager.crds.yaml
```

Install cert-manager with Helm

```bash
helm install \
 cert-manager jetstack/cert-manager \
 --namespace cert-manager \
 --version v1.2.0 \
 --set 'extraArgs={--dns01-recursive-nameservers=8.8.8.8:53\,1.1.1.1:53}'
```
## Install cert-manager duckdns webhook

I developed a webhook that goes along cert-manager for duckdns domains (cert-manager-webhook-duckdns). cert-manager does not support dns01 with duckdns despite that duckdns provides and API for TXT records. A helm chart is available for it [here](https://github.com/ebrianne/helm-charts/tree/master/charts/cert-manager-webhook-duckdns).

Add the helm repo

```bash
helm repo add ebrianne.github.io https://ebrianne.github.io/helm-charts
helm repo update
```

Install the helm chart for cert-manager-webhook-duckdns

```bash
helm install cert-manager-webhook-duckdns \
--namespace cert-manager \
--set duckdns.domain='<domain>' \
--set duckdns.token='<token>' \
--set clusterIssuer.production.create=true \
--set clusterIssuer.staging.create=true \
--set clusterIssuer.email=<email> \
--set logLevel=2 \
ebrianne.github.io/cert-manager-webhook-duckdns
```

It will automatically deploy the webhook in Kubernetes and create two cluster issuers for staging (cert-manager-webhook-duckdns-staging) and production (cert-manager-webhook-duckdns-production).

You can then create certificate for domain with a yaml file as below

```yml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: example.duckdns.org
  namespace: default
spec:
  dnsNames:
  - 'example.duckdns.org'
  issuerRef:
    name: cert-manager-webhook-duckdns-production #can be cert-manager-webhook-duckdns-staging
    kind: ClusterIssuer
  secretName: example-duckdns-org-tls
```
