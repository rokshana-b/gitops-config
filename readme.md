Kind cluster config:

```yaml
---
kind: Cluster
name: oidc-test
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30000
    hostPort: 80
    listenAddress: "0.0.0.0"
    protocol: tcp
  - containerPort: 30001
    hostPort: 443
    listenAddress: "0.0.0.0"
    protocol: tcp
- role: worker
```

```bash
kind create cluster --config kind-cluster-config.yaml
```