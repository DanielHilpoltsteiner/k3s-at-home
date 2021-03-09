# Kubernetes configuration for Homelab

[![k3s](https://img.shields.io/badge/k3s-v1.20.4-orange?style=for-the-badge)](https://k3s.io/)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white&style=for-the-badge)](https://github.com/pre-commit/pre-commit)
[![renovate](https://img.shields.io/badge/renovate-enabled-brightgreen?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjUgNSAzNzAgMzcwIj48Y2lyY2xlIGN4PSIxODkiIGN5PSIxOTAiIHI9IjE4NCIgZmlsbD0iI2ZlMiIvPjxwYXRoIGZpbGw9IiM4YmIiIGQ9Ik0yNTEgMjU2bC0zOC0zOGExNyAxNyAwIDAxMC0yNGw1Ni01NmMyLTIgMi02IDAtN2wtMjAtMjFhNSA1IDAgMDAtNyAwbC0xMyAxMi05LTggMTMtMTNhMTcgMTcgMCAwMTI0IDBsMjEgMjFjNyA3IDcgMTcgMCAyNGwtNTYgNTdhNSA1IDAgMDAwIDdsMzggMzh6Ii8+PHBhdGggZmlsbD0iI2Q1MSIgZD0iTTMwMCAyODhsLTggOGMtNCA0LTExIDQtMTYgMGwtNDYtNDZjLTUtNS01LTEyIDAtMTZsOC04YzQtNCAxMS00IDE1IDBsNDcgNDdjNCA0IDQgMTEgMCAxNXoiLz48cGF0aCBmaWxsPSIjYjMwIiBkPSJNMjg1IDI1OGw3IDdjNCA0IDQgMTEgMCAxNWwtOCA4Yy00IDQtMTEgNC0xNiAwbC02LTdjNCA1IDExIDUgMTUgMGw4LTdjNC01IDQtMTIgMC0xNnoiLz48cGF0aCBmaWxsPSIjYTMwIiBkPSJNMjkxIDI2NGw4IDhjNCA0IDQgMTEgMCAxNmwtOCA3Yy00IDUtMTEgNS0xNSAwbC05LThjNSA1IDEyIDUgMTYgMGw4LThjNC00IDQtMTEgMC0xNXoiLz48cGF0aCBmaWxsPSIjZTYyIiBkPSJNMjYwIDIzM2wtNC00Yy02LTYtMTctNi0yMyAwLTcgNy03IDE3IDAgMjRsNCA0Yy00LTUtNC0xMSAwLTE2bDgtOGM0LTQgMTEtNCAxNSAweiIvPjxwYXRoIGZpbGw9IiNiNDAiIGQ9Ik0yODQgMzA0Yy00IDAtOC0xLTExLTRsLTQ3LTQ3Yy02LTYtNi0xNiAwLTIybDgtOGM2LTYgMTYtNiAyMiAwbDQ3IDQ2YzYgNyA2IDE3IDAgMjNsLTggOGMtMyAzLTcgNC0xMSA0em0tMzktNzZjLTEgMC0zIDAtNCAybC04IDdjLTIgMy0yIDcgMCA5bDQ3IDQ3YTYgNiAwIDAwOSAwbDctOGMzLTIgMy02IDAtOWwtNDYtNDZjLTItMi0zLTItNS0yeiIvPjxwYXRoIGZpbGw9IiMxY2MiIGQ9Ik0xNTIgMTEzbDE4LTE4IDE4IDE4LTE4IDE4em0xLTM1bDE4LTE4IDE4IDE4LTE4IDE4em0tOTAgODlsMTgtMTggMTggMTgtMTggMTh6bTM1LTM2bDE4LTE4IDE4IDE4LTE4IDE4eiIvPjxwYXRoIGZpbGw9IiMxZGQiIGQ9Ik0xMzQgMTMxbDE4LTE4IDE4IDE4LTE4IDE4em0tMzUgMzZsMTgtMTggMTggMTgtMTggMTh6Ii8+PHBhdGggZmlsbD0iIzJiYiIgZD0iTTExNiAxNDlsMTgtMTggMTggMTgtMTggMTh6bTU0LTU0bDE4LTE4IDE4IDE4LTE4IDE4em0tODkgOTBsMTgtMTggMTggMTgtMTggMTh6bTEzOS04NWwyMyAyM2M0IDQgNCAxMSAwIDE2TDE0MiAyNDBjLTQgNC0xMSA0LTE1IDBsLTI0LTI0Yy00LTQtNC0xMSAwLTE1bDEwMS0xMDFjNS01IDEyLTUgMTYgMHoiLz48cGF0aCBmaWxsPSIjM2VlIiBkPSJNMTM0IDk1bDE4LTE4IDE4IDE4LTE4IDE4em0tNTQgMThsMTgtMTcgMTggMTctMTggMTh6bTU1LTUzbDE4LTE4IDE4IDE4LTE4IDE4em05MyA0OGwtOC04Yy00LTUtMTEtNS0xNiAwTDEwMyAyMDFjLTQgNC00IDExIDAgMTVsOCA4Yy00LTQtNC0xMSAwLTE1bDEwMS0xMDFjNS00IDEyLTQgMTYgMHoiLz48cGF0aCBmaWxsPSIjOWVlIiBkPSJNMjcgMTMxbDE4LTE4IDE4IDE4LTE4IDE4em01NC01M2wxOC0xOCAxOCAxOC0xOCAxOHoiLz48cGF0aCBmaWxsPSIjMGFhIiBkPSJNMjMwIDExMGwxMyAxM2M0IDQgNCAxMSAwIDE2TDE0MiAyNDBjLTQgNC0xMSA0LTE1IDBsLTEzLTEzYzQgNCAxMSA0IDE1IDBsMTAxLTEwMWM1LTUgNS0xMSAwLTE2eiIvPjxwYXRoIGZpbGw9IiMxYWIiIGQ9Ik0xMzQgMjQ4Yy00IDAtOC0yLTExLTVsLTIzLTIzYTE2IDE2IDAgMDEwLTIzTDIwMSA5NmExNiAxNiAwIDAxMjIgMGwyNCAyNGM2IDYgNiAxNiAwIDIyTDE0NiAyNDNjLTMgMy03IDUtMTIgNXptNzgtMTQ3bC00IDItMTAxIDEwMWE2IDYgMCAwMDAgOWwyMyAyM2E2IDYgMCAwMDkgMGwxMDEtMTAxYTYgNiAwIDAwMC05bC0yNC0yMy00LTJ6Ii8+PC9zdmc+)](https://github.com/renovatebot/renovate)

## Installation of Kubernetes using k3s

The distribution of choice is [k3s](https://k3s.io/) as it is lightweight and contains all of the features that are used for a homelab case.
I chose to do a [HA installation](https://rancher.com/docs/k3s/latest/en/installation/ha/) with 2 master nodes and 3 worker nodes. I use proxmox as the hypervisor layer and Ubuntu Server 20.04.

### Install a Load Balancer (LB)

The setup of a load balancer is on the long run very useful, it allows to add more master nodes with time. You can chose any distribution you like that allow for TCP/UDP load balancing: [nginx](https://www.nginx.com/), [traefik](https://doc.traefik.io/traefik/), [haproxy](http://www.haproxy.org/)

I decided to go with nginx as it is easy and fast to spin up. You can either use docker to setup this or you can use a VM. I go for the latter as I also setup the external DB on it.

Install nginx on ubuntu 20.04:

```bash
sudo apt install nginx
```

Edit the `nginx.conf` in the stream section with:

    stream {
        upstream k3s_servers {
            server $IP_MASTER1:6443;
            server $IP_MASTER2:6443;
        }

        server {
            listen 6443;
            proxy_pass k3s_servers;
        }
    }

### Setup an external DB (external datastore for k3s)

### MariaDB

k3s provides a datastore out of the box (sqlite) if you wish to skip this step but generally this is not suggested for production environments. k3s supports [several datastores](https://rancher.com/docs/k3s/latest/en/installation/datastore/). To specify to k3s the datastore, a flag `--datastore-endpoint` needs to be passed to k3s as the following for a mysql database:

```bash
curl -sfL https://get.k3s.io | sh -s - server --datastore-endpoint="mysql://username:password@tcp(hostname:3306)/database-name"
```

I chose MariaDB as it is easy to setup. First, install mariadb on ubuntu with

```bash
sudo apt install mariadb-server
```

Finish the database installation by running `sudo mysql_secure_installation`.
Once done, some things need to be setup. Modify `/etc/mysql/mariadb.conf.d/50-server.cnf`.You can change the port if you want for more security. If you use `bind-address = 127.0.0.1`, only localhost will be able to connect. It works fine if the LB is on the same machine and configured to forward the TCP request. Use `bind-address = 0.0.0.0` to listen on all interfaces.

```
[mysqld]
#socket                  = /run/mysqld/mysqld.sock
port                     = 3306
#bind-address            = 127.0.0.1
bind-address             = 0.0.0.0
```

You will need to create a kubernetes database and a user restricted to it.

```bash
mysql -u root -p
```

```sql
CREATE DATABASE `k3s`;
GRANT ALL PRIVILEGES ON `k3s`.* TO 'k3s'@'%' IDENTIFIED BY '<password>';
FLUSH PRIVILEGES;
```

Restart mariadb

```bash
sudo systemctl restart mariadb.service
```

With your local machine, try to connect to the DB using the k3s user. You can specify `--port <port>` if you use a different port.

```
mysql -u k3s -p -h $DB_IP
```

If you can connect, then everything is setup fine. Otherwise, check the password used and the bind-address of mariadb.

### Postgres DB

It is similar as mariadb.

```bash
curl -sfL https://get.k3s.io | sh -s - server --datastore-endpoint="postgres://username:password@10.0.40.3:5432/database?sslmode=disable"
```

Install and start postgres. Modify the file in `/etc/postgresql/postgresql.conf` under "CONNECTIONS AND AUTHENTICATION" with

```
listen_addresses = '*'          # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)
port = 5432                             # (change requires restart)
max_connections = 100                   # (change requires restart)
#superuser_reserved_connections = 3     # (change requires restart)
unix_socket_directories = '/run/postgresql,/tmp'        # comma-separated list of directories
```

Modify the connection rights in the file `/etc/postgresql/pg_hba.conf` by adding a line

```
host  database  username  $NETWORK_CIDR  trust
```

Restart postgres.

```bash
sudo systemctl restart postgresql.service
```

You will need to create a kubernetes database and a user restricted to it.

```bash
psql -U postgres
```

```sql
CREATE DATABASE `k3s`;
CREATE USER k3s WITH PASSWORD '<password>';
GRANT ALL ON DATABASE k3s TO k3s;
```
### ETCD

Create a etcd user

```bash
addgroup -S etcd 2>/dev/null
adduser -S -D -H -h /dev/null -s /sbin/nologin -G etcd -g etcd etcd 2>/dev/null
```

Get the latest etcd binaries and extract the archive

```bash
wget https://github.com/etcd-io/etcd/releases/download/v3.4.15/etcd-v3.4.15-linux-amd64.tar
tar xvf etcd-v3.4.15-linux-amd64.tar
mv etcd-v3.4.15-linux-amd64/etcd etcd-v3.4.15-linux-amd64/etcdctl /usr/bin
rm -rf etcd-v3.4.15-linux-amd64
rm -rf etcd-v3.4.15-linux-amd64.tar
```

Check the etcd version

```bash
etcd --version

etcd Version: 3.4.15
Git SHA: aa7126864
Go Version: go1.12.17
Go OS/Arch: linux/amd64
```

Configure etcd and init.d using the files in `hacks/etcd-alpine` folder. Once configure you can start the service and add it on boot.

```bash
rc-service etcd start
rc-update add etcd

etcdctl member list -w table
+------------------+---------+--------+------------------------+------------------------+------------+
|        ID        | STATUS  |  NAME  |       PEER ADDRS       |      CLIENT ADDRS      | IS LEARNER |
+------------------+---------+--------+------------------------+------------------------+------------+
| 1790ea0adceb47ac | started | etcd-2 | http://10.0.40.11:2380 | http://10.0.40.11:2379 |      false |
| 2258fe5a2cf07b76 | started | etcd-1 | http://10.0.40.10:2380 | http://10.0.40.10:2379 |      false |
| a2fc059f31344bbd | started | etcd-3 | http://10.0.40.12:2380 | http://10.0.40.12:2379 |      false |
+------------------+---------+--------+------------------------+------------------------+------------+
```

### Pre-setup on Alpine linux

A little bit of preparation before starting k3s.

```bash 
apk add iptables cni-plugins
rc-service cgroups start
rc-update add cgroups boot
```

Add this file in every node for sysctl `10-kube.conf`

```bash
# SWAP settings
vm.swappiness=0
vm.overcommit_memory=1

# Have a larger connection range available
net.ipv4.ip_local_port_range=1024 65000

# Increase max connection
net.core.somaxconn = 10000

# Reuse closed sockets faster
net.ipv4.tcp_tw_reuse=1
net.ipv4.tcp_fin_timeout=15

# The maximum number of "backlogged sockets".  Default is 128.
net.core.netdev_max_backlog=4096

# 16MB per socket - which sounds like a lot,
# but will virtually never consume that much.
net.core.rmem_max=16777216
net.core.wmem_max=16777216

# Various network tunables
net.ipv4.tcp_max_syn_backlog=20480
net.ipv4.tcp_max_tw_buckets=400000
net.ipv4.tcp_no_metrics_save=1
net.ipv4.tcp_rmem=4096 87380 16777216
net.ipv4.tcp_syn_retries=2
net.ipv4.tcp_synack_retries=2
net.ipv4.tcp_wmem=4096 65536 16777216

# ip_forward and tcp keepalive for iptables
net.ipv4.tcp_keepalive_time=600
net.ipv4.ip_forward=1

# monitor file system events
fs.inotify.max_user_instances=8192
fs.inotify.max_user_watches=1048576
```

Reboot.

### Install k3s server

On each master node ($IP_MASTER1 and $IP_MASTER2) to install k3s using containerd as container manager.

With MySQL:

```bash
export K3S_DATASTORE_ENDPOINT='mysql://username:password@tcp(database_ip_or_hostname:port)/database'
curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --write-kubeconfig-mode 644 --disable traefik --disable servicelb --disable coredns --disable metrics-server --disable local-storage --tls-san $LB_IP --flannel-backend=none --disable-network-policy --cluster-cidr=10.69.0.0/16 --service-cidr=10.96.0.0/16 --cluster-dns=10.96.0.10
```

or with Postgres:

```bash
export K3S_DATASTORE_ENDPOINT='postgres://username:password@10.0.40.3:5432/database?sslmode=disable'
curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --write-kubeconfig-mode 644 --disable traefik --disable servicelb --disable coredns --disable metrics-server --disable local-storage --tls-san $LB_IP --flannel-backend=none --disable-network-policy --cluster-cidr=10.69.0.0/16 --service-cidr=10.96.0.0/16 --cluster-dns=10.96.0.10
```

or with etcd cluster:

```bash
export K3S_DATASTORE_ENDPOINT='http://etcd-1:2379,http://etcd-2:2379,http://etcd-3:2379'
curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --write-kubeconfig-mode 644 --disable traefik --disable servicelb --disable coredns --disable metrics-server --disable local-storage --tls-san $LB_IP --flannel-backend=none --disable-network-policy --cluster-cidr=10.69.0.0/16 --service-cidr=10.96.0.0/16 --cluster-dns=10.96.0.10

```

You can also use [docker](https://docs.docker.com/) by passing the flag `--docker`. However, docker as been deprecated in the recent kubernetes release and will be removed in the future.

It will install k3s in server mode without traefik, klipperlb, metrics-server, local-storage and coredns installed. No network backend also is set by `--flannel-backend=none`. You can then install [Canal/Calico](https://thenewstack.io/tutorial-configure-cloud-native-edge-infrastructure-with-k3s-calico-portworx/) as cni-network plugin. The node will not be in a ready state until you install a network backend. You should be able to run

```bash
kubectl get nodes -o wide
```

    NAME            STATUS   ROLES                  AGE   VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
    k3s-master-02   Ready    control-plane,master   27m   v1.20.2+k3s1   10.0.40.5     <none>        Alpine Linux v3.13   5.10.12-0-lts    containerd://1.4.3-k3s1
    k3s-master-01   Ready    control-plane,master   28m   v1.20.2+k3s1   10.0.40.4     <none>        Alpine Linux v3.13   5.10.12-0-lts    containerd://1.4.3-k3s1

If you want to access the cluster from your local machine. Create a folder ~/.kube and add a `config` file with the contents from `sudo cat /etc/rancher/k3s/k3s.yaml`. Be cure to modify the IP address of the cluster `server:` to the Load Balancer IP address.

### Install k3s agent

You can create worker nodes easily and add them to your cluster with k3s. You need first the token from one master node. It is located in `/var/lib/rancher/k3s/server/node-token`

```bash
curl -sfL https://get.k3s.io | K3S_URL=https://$IP_LB:6443 K3S_TOKEN=<token> sh -
```

    NAME            STATUS   ROLES                  AGE     VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
    k3s-master-02   Ready    control-plane,master   37m     v1.20.2+k3s1   10.0.40.5     <none>        Alpine Linux v3.13   5.10.12-0-lts    containerd://1.4.3-k3s1
    k3s-worker-01   Ready    <none>                 7m19s   v1.20.2+k3s1   10.0.40.6     <none>        Alpine Linux v3.13   5.10.12-0-lts    containerd://1.4.3-k3s1
    k3s-master-01   Ready    control-plane,master   38m     v1.20.2+k3s1   10.0.40.4     <none>        Alpine Linux v3.13   5.10.12-0-lts    containerd://1.4.3-k3s1

### Install MetalLB

MetalLB provides a network load-balancer implementation. In short, it allows you to create Kubernetes services of type "LoadBalancer" in clusters that don't run on a cloud provider. Installation instructions are [here](https://metallb.universe.tf/installation/).

```kubectl
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/metallb.yaml
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

### Deploy a service

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
It may throw an error as it deletes the namespace first and then tries to delete the other ressources of the namespace that does not exist anymore, but this is fine.
