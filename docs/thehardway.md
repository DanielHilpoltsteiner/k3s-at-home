
## The Hard Way

### Create a Load Balancer (LB) (only needed for the hard way, kube-vip is used otherwise)

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
            server $IP_MASTER3:6443;
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
