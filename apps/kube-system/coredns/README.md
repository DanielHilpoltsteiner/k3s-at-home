helm install -n kube-system coredns coredns/coredns --set service.clusterIP=10.96.0.10
