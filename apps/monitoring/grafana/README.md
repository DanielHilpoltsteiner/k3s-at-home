# Monitoring with Grafana

## Install Grafana repository in Helm

```bash
kubectl create namespace grafana
```

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

## Install Grafana with Helm

Modify `values.yaml` with custom parameters

```yml
ingress:
  enabled: true
  ....
  hosts:
    - grafana.example.com
  ...
  tls:
    - secretName: grafana-example-com
      hosts:
        - grafana.example.com

resources:
 limits:
   cpu: 200m
   memory: 256Mi
 requests:
   cpu: 200m
   memory: 256Mi

persistence:
  type: pvc
  enabled: true
  storageClassName: longhorn
  accessModes:
    - ReadWriteOnce
  size: 5Gi
  # annotations: {}
  finalizers:
    - kubernetes.io/pvc-protection
  # selectorLabels: {}
  # subPath: ""
  existingClaim: longhorn-pvc-grafana

plugins:
  - grafana-clock-panel
  - grafana-worldmap-panel
  - grafana-piechart-panel

...
```

Run

```bash
kubectl apply -f pvc.yml
helm install grafana --namespace grafana grafana/grafana -f values.yaml
```

Getting the admin password created at the installation

```bash
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

## Upgrade Grafana with Helm

Modify `values.yaml` and run

```bash
helm upgrade grafana --namespace prometheus grafana/grafana -f values.yaml
```
