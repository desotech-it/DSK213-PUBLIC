# Lab 01 — Installing ArgoCD

Manifest used to bootstrap ArgoCD on a fresh Kubernetes cluster.

## Files

### `argocd-install-v3.3.0.yaml`

Full ArgoCD **v3.3.0** install manifest, fetched verbatim from upstream:

```
https://raw.githubusercontent.com/argoproj/argo-cd/v3.3.0/manifests/install.yaml
```

It contains all the CRDs, ServiceAccounts, RBAC, ConfigMaps, Services, Deployments and the StatefulSet required by the ArgoCD control plane:

- `argocd-server` — API + UI
- `argocd-repo-server` — renders manifests from Git / Helm / Kustomize
- `argocd-application-controller` — reconciles desired vs live state
- `argocd-applicationset-controller` — generates multiple `Application` resources
- `argocd-dex-server` — SSO / OIDC
- `argocd-redis` — state cache
- `argocd-notifications-controller` — notifications engine

This file is a snapshot — applying it with `kubectl apply --server-side` is equivalent to applying the upstream URL directly.
