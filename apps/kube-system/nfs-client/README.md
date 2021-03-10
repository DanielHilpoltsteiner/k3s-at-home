# Persistance of Pod data using nfs

The major problem with data that is generated inside a pod is that once the pod is destroyed the data is gone! Kubernetes provides tools called [storage-drivers](https://kubernetes-csi.github.io/docs/drivers.html) that enables to store pod data outside.

You can then destroy the pod, the data is still there and can be picked up by another pod. This is for example essential with databases.

## Data persistance using NFS storage

By default, k3s only come with a local-data storage driver, [local-path-provisioner](https://github.com/rancher/local-path-provisioner). This driver enables to store data locally on a node using the `hostPath` volumes. However, if data needs to be shared across pods or if a pod is scheduled on another node, the data is therefore not accessible.

This can be solved by having an nfs-server to store the data such as [TrueNAS](https://www.truenas.com/) or any machine with a nfs-server. You can even spin up an nfs-server in kubernetes on a dedicated node if you want. See [nfs-server-provisionner](https://github.com/kubernetes-sigs/nfs-ganesha-server-and-external-provisioner) for more details.

If you have already an NFS server. You ll need the [nfs-client-provisionner](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner).

### Install the nfs-client-provisionner

Modify the `value.yml` file with the settings to your nfs server:

```yml
nfs:
  server: 10.0.40.2
  path: /exports
  mountOptions:
```

Install the helm chart

```bash
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner -n kube-system -f values.yaml
```

### Create a Persistant Volume (PV) and Persistant Volume Claim (PVC)

The Persistant Volume or PV defines the information about the storage volume. Change the `storageClassName` to nfs-client if you want to use nfs storage driver. Important, the path needs to exist before creating the PV.

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv-app
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  storageClassName: nfs-client
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /export/app
    server: 10.0.40.2
```

The PV definition is only optional and one can just create a PVC. The storage driver (if supported) will dynamically create a folder in the storage block. The nfs-client will create a dynamic volume named `{namespace}-${pvcName}-${pvName}` in the nfs path. A PVC yaml file looks like this:

```yml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc-app
  namespace: default
spec:
  storageClassName: nfs-client
  accessModes:
  - ReadWriteMany #ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

It will allocate a 1Gi storage for the app in the `default` namespace. Be sure that the namespace of the deployement and the PVC is the same. If everything is fine, you should see it in a `Bounded` state.

```bash
kubectl get pvc -n default
```

    NAME               STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    nfs-pvc-app        Bound    pvc-6586305b-f43f-47d4-8c7c-bbbea9e5bbda   1Gi        RWX            nfs-client     6s

The PV is automatically created

    NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                             STORAGECLASS   REASON   AGE
    pvc-6586305b-f43f-47d4-8c7c-bbbea9e5bbda   1Gi        RWX            Retain           Bound      default/nfs-pvc-app               nfs-client              105s

The Reclaim Policy can be `Retain`, `Recycle`, and `Delete`. The default behavior is `Delete` unless configured in either the PV with `persistentVolumeReclaimPolicy` or in the storageClass default behavior, in the helm chart with `storageClass.reclaimPolicy`. PV can also be archived on delete with `storageClass.archiveOnDelete`

You can delete the PVC with

```bash
kubectl delete pvc -n default nfs-pvc-app
```

The PV shoud then be in a `Released` state if the default Reclaim Policy is not `Delete`.

    NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                             STORAGECLASS   REASON   AGE
    pvc-6586305b-f43f-47d4-8c7c-bbbea9e5bbda   1Gi        RWX            Retain           Released   default/nfs-pvc-app               nfs-client              105s

You can delete the PV

```bash
kubectl delete pv pvc-6586305b-f43f-47d4-8c7c-bbbea9e5bbda
```

If the default policy is `Delete`, the PV is automatically deleted with the PVC. If `archiveOnDelete` is set to true, before the PV deletion, it will archive the volume in the form `archived-{namespace}-${pvcName}-${pvName}`.
