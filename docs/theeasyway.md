## The Easy Way

We will use k3sup and kube-vip to install k3s masters and agents with embedded etcd.
Prepare a master node and had the ip of the servers into `/etc/hosts`

```bash
10.0.40.4 k3s-1
10.0.40.5 k3s-2
10.0.40.6 k3s-3
10.0.40.7 k3s-w-1
10.0.40.8 k3s-w-2
```

Install a first master with this command

```bash
k3sup install --ip=10.0.40.4 --user=root --k3s-version=v1.20.4+k3s1 --local-path=$HOME/.kube/config --context default --cluster --tls-san 10.0.40.3 --k3s-extra-args="--write-kubeconfig-mode 644 --disable servicelb --disable traefik --disable coredns --disable metrics-server --disable local-storage --cluster-cidr=10.69.0.0/16 --service-cidr=10.96.0.0/16 --cluster-dns=10.96.0.10 --node-taint node-role.kubernetes.io/master=true:NoSchedule"
```

Connect to the master node k3s-1 and install kube-vip

```bash
curl -s https://kube-vip.io/manifests/rbac.yaml > /var/lib/rancher/k3s/server/manifests/kube-vip-rbac.yaml
export VIP=10.0.40.3
export INTERFACE=eth0
crictl pull docker.io/plndr/kube-vip:0.3.3
alias kube-vip="ctr run --rm --net-host docker.io/plndr/kube-vip:0.3.3 vip /kube-vip"

kube-vip manifest daemonset \
                  --arp \
                  --interface $INTERFACE \
                  --address $VIP \
                  --controlplane \
                  --leaderElection \
                  --taint \
                  --inCluster | tee /var/lib/rancher/k3s/server/manifests/kube-vip.yaml

ping $VIP
```

The VIP should reply to the ping. Then you re good to go. Replace the ip into your kubeconfig file with the VIP IP.

Add other masters

```bash
k3sup join --ip 10.0.40.5 --server-ip 10.0.40.3 --user root --k3s-version v1.20.4+k3s1 --server --k3s-extra-args "--write-kubeconfig-mode 644 --disable servicelb --disable traefik --disable coredns --disable metrics-server --disable local-storage --cluster-cidr 10.69.0.0/16 --service-cidr 10.96.0.0/16 --cluster-dns 10.96.0.10 --node-taint node-role.kubernetes.io/master=true:NoSchedule"

k3sup join --ip 10.0.40.6 --server-ip 10.0.40.3 --user root --k3s-version v1.20.4+k3s1 --server --k3s-extra-args "--write-kubeconfig-mode 644 --disable servicelb --disable traefik --disable coredns --disable metrics-server --disable local-storage --cluster-cidr 10.69.0.0/16 --service-cidr 10.96.0.0/16 --cluster-dns 10.96.0.10 --node-taint node-role.kubernetes.io/master=true:NoSchedule"
```

Add a worker node

```bash
k3sup join --ip 10.0.40.7 --server-ip 10.0.40.3 --user root --k3s-version v1.20.4+k3s1
k3sup join --ip 10.0.40.8 --server-ip 10.0.40.3 --user root --k3s-version v1.20.4+k3s1
```
