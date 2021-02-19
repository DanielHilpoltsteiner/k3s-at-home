# Install Prometheus on Kubernetes with Helm

## Install with Helm

Create a namespace first

```bash
kubectl create namespace prometheus
```

Add the repository to helm and update

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install the helm chart

```bash
kubectl apply -f pv.yml -f pvc.yml
helm install prometheus prometheus-community/prometheus --namespace prometheus -f values.yaml
```

You can modify the `values.yaml` to add exporter to the prometheus configuration.

```yml
extraScrapeConfigs: |
  - job_name: 'exporter1'
    static_configs:
       - targets: ['$IP:$PORT']
```

## Upgrade Prometheus

Modify `values.yaml` and run

```bash
helm upgrade prometheus prometheus-community/prometheus --namespace prometheus -f values.yaml
```
