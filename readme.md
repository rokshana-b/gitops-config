## Kind cluster config

```yaml
---
kind: Cluster
name: oidc
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

## GitHub secret

```bash
kubectl create namespace dex
kubectl create secret generic github-client \
  --namespace=dex \
  --from-literal=client-id=${GITHUB_CLIENT_ID} \
  --from-literal=client-secret=${GITHUB_CLIENT_SECRET}
```

## OIDC Seret

`clientID` and `clientSecret` are defined in
`clusters/kind/dex/helm_release.yaml`.

```bash
kubectl create namespace flux-system
kubectl create secret generic oidc-auth \
  --namespace flux-system \
  --from-literal=issuerURL=https://dex.gitops.efertone.me \
  --from-literal=clientID=weave-gitops \
  --from-literal=clientSecret=AiAImuXKhoI5ApvKWF988txjZ+6rG3S7o6X5En \
  --from-literal=redirectURL=https://ui.gitops.efertone.me/oauth2/callback
```

## Bootstrap flux

```bash
❯ flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=gitops-config \
  --branch=main \
  --path=./clusters/kind \
  --personal
```

## ClusterIssuer

(can't be created before cert manager is installed)

```bash
kubectl apply -f clusters/kind/cert-manager/cluster_issuer.yaml
```

## GitHub OAuth Application

![image](https://user-images.githubusercontent.com/183191/165118739-4eae08d6-cf4b-4f76-bd78-8475f741127e.png)

