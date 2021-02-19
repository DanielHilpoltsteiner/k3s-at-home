# Automatic Upgrade of k3s using system-upgrade-controller

## Deploy system-upgrade-controller

First, you need to deploy the system-upgrade-controller.

```bash
kubectl apply -f https://github.com/rancher/system-upgrade-controller/releases/download/v0.6.2/system-upgrade-controller.yaml
```

You should see the upgrade controller starting in `system-upgrade` namespace.

    NAME                                        READY   STATUS    RESTARTS   AGE
    system-upgrade-controller-b64b5bcb7-7b59s   1/1     Running   2          20d

## Create an upgrade plan for master/worker nodes

The upgrade controller watches automatically for upgrade plans and execute the upgrade on the nodes. For master nodes, the plan `master.yml` is used and for worker nodes, `node.yml`. The plan look for the latest stable release of k3s specified by:

```yml
spec:
    ...
    channel: https://update.k3s.io/v1-release/channels/stable
    ...
```

It will only upgrade the nodes based on labels `k3s-upgrade=true` and use the correct plan depending if the node is labelled as `node-role.kubernetes.io/master=true`.

### Upgrade master nodes

Label the master nodes, one-by-one:

```bash
kubectl taint node kube-01 CriticalAddonsOnly:NoExecute-
kubectl label node kube-01 k3s-upgrade=true
```

Wait for the upgrade to do its work. Once done, remove the upgrade labels

```bash
kubectl label node kube-01 k3s-upgrade-
kubectl taint node kube-01 CriticalAddonsOnly:NoExecute
```

### Upgrade worker nodes

This has to be done after all master nodes are upgraded! Label the worker node with `k3s-upgrade=true`. It will automatically drain the node, cordon it and upgrade k3s.

```bash
kubectl label node kube-worker-01 k3s-upgrade=true
```

Once the update is done. The node should become in a `Ready` state again. You can then remove the label.

```bash
kubectl label node kube-worker-01 k3s-upgrade-
```
