### Install MetalLB

MetalLB provides a network load-balancer implementation. In short, it allows you to create Kubernetes services of type "LoadBalancer" in clusters that don't run on a cloud provider. Installation instructions are [here](https://metallb.universe.tf/installation/).

```bash
scp apps/kube-system/metallb/metallb.yaml root@10.0.40.4:/var/lib/rancher/k3s/server/manifests/
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

You will need to configure the range of IPs that MetalLB controls. For this, create a configMap like below and modify the addresses range to your needs.

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.1.240-192.168.1.250
```

Now when requesting a service of type `LoadBalancer`, MetalLB will assign automatically an external IP to the service.

### Install an Ingress controller

When installing k3s, we disabled traefik which is the default provided ingress controller. The version used in k3s is stable but too old to my taste and I prefer either new versions, like traefik v2 or a "more" supported one.

The default ingress controller used in k8s is `nginx-ingress controller`. This is one of the most used controller out there and there is widely supported/documented. The ingress controller will be needed in order to access the service via domain name from outside the cluster without having a service of type `NodePort` and avoiding to have to put the IP:PORT in the browser bar to access the service.

The ingress controller will deploy a service of type `LoadBalancer` which MetalLB will assign an IP address. You can then forward ports 80 and 443 to this IP to access any service hosted in your cluster. Additionally you can buy/create a domain name that is mapped to your public address. A lot of providers (free or not) are available.

To make things easier, we will use [helm](https://helm.sh/) to install the ingress controller. To install helm on your local machine, follow the installation guide [here](https://helm.sh/docs/intro/install/).

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx -f values.yml
```

The default backend is disabled by default in the helm chart. You can install it by adding `--set defaultBackend.enabled=true`.

<!-- ### Deploy a service

Let's deploy a simple website using nginx docker image. Deploy it by running:

```bash
kubectl apply -f https://raw.githubusercontent.com/ebrianne/kubernetes-stack/gh-pages/example/deploy.yml
```

Modify your local host file `/etc/hosts` mapping the domain example.com to the ingress-controller external IP address. You can have the IP address by running

```bash
kubectl get svc -n ingress-nginx ingress-nginx-controller
```

    NAME                       TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
    ingress-nginx-controller   LoadBalancer   10.43.37.106   10.0.40.9     80:30086/TCP,443:31778/TCP   23d


Open a browser and you should be able to access the nginx welcome page at example.com. Check that the pod is running.

```bash
kubectl get pods -n nginx
```

    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-7bcd6d497b-2gszs   1/1     Running   0          47s

Cleanup the deployement

```bash
kubectl delete -f https://raw.githubusercontent.com/ebrianne/kubernetes-stack/gh-pages/example/deploy.yml
```
It may throw an error as it deletes the namespace first and then tries to delete the other ressources of the namespace that does not exist anymore, but this is fine.

### Get TLS certificates

You can add a layer of security for your services by running them with https. For this, you need to create a TLS certificate for your domain. Fortunately, [Let's Encrypt](https://letsencrypt.org/) can do that for you and for free! You have mainly two options on how to do that. You can do it manually using the [certbot](https://certbot.eff.org/) tool and create kubernetes tls secret from the certificates, or you can use [cert-manager](https://cert-manager.io/docs/).

Cert-manager is very practical as it can request automatically certificates for us and stores them as kubernetes secrets.

First install the CRDs

```bash
kubectl create namespace cert-manager
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.crds.yaml
```

Then we can use helm to install cert-manager.

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install \
 cert-manager jetstack/cert-manager \
 --namespace cert-manager \
 --version v1.1.0 \
 --set 'extraArgs={--dns01-recursive-nameservers=8.8.8.8:53\,1.1.1.1:53}'
```

Verify that it is running

```bash
kubectl get pods --namespace cert-manager
```

    NAME                                            READY   STATUS    RESTARTS   AGE
    cert-manager-6cd569fdfc-s74pc                   1/1     Running   4          5d22h
    cert-manager-webhook-66b555bb5-9r2v2            1/1     Running   1          5d22h
    cert-manager-cainjector-86bc6dc648-lnsgn        1/1     Running   27         5d22h

Before deploying an ingress with TLS, one needs to create a cluster issuer. An example is shown below.

```yml
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: user@example.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-secret-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```

You have two choice of solvers: [http01](https://cert-manager.io/docs/configuration/acme/http01/) and [dns01](https://cert-manager.io/docs/configuration/acme/dns01/). For dns01, your domain provider needs to have an API that supports adding of TXT record to your domain but also needs to be supported by cert-manager. See the documentation for the list of supported providers. Webhooks can be created to support unofficially some providers that are not supported out of the box by cert-manager.

Apply the cluster-issuer

```bash
kubectl apply -f cluster-issuer.yml
```

Then you can deploy the app with tls.

```bash
kubectl apply -f https://raw.githubusercontent.com/ebrianne/kubernetes-stack/gh-pages/example/deploy-tls.yml
```

It will take one or two minutes for cert-manager to present the http01 challenge and get the certificate issued by `letsencrypt-prod`. You can now access the service via https://example.com. You should be able to see a valid Let's Encrypt certificate! (In my case, issued by `DST Root CA X3 -> R3`).

Cleanup the deployement

```bash
kubectl delete -f https://raw.githubusercontent.com/ebrianne/kubernetes-stack/gh-pages/example/deploy-tls.yml
```
It may throw an error as it deletes the namespace first and then tries to delete the other ressources of the namespace that does not exist anymore, but this is fine. -->
