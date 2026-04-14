# Lab 02 — Apps & Projects

Manifests introducing the first ArgoCD `AppProject` and `Application`, plus the demo workload they deploy.

## `apps/demo/`

### `deployment.yaml`
nginx 1.27-alpine Deployment, 2 replicas, minimal resource requests/limits. Target workload for the `demo-app` Application.

### `service.yaml`
ClusterIP Service on port 80 targeting the nginx pods.

## `argocd/`

### `project-platform-dev.yaml`
`AppProject` **platform-dev**. Restricts:
- `sourceRepos`: only `DSK213-PUBLIC`
- `destinations`: namespaces matching `dev-*` on the in-cluster Kubernetes
- `clusterResourceWhitelist`: `Namespace` only
- `namespaceResourceWhitelist`: `Deployment`, `Service`, `ConfigMap`
- `orphanedResources.warn: true`

### `demo-app-failing.yaml`
`Application` **demo-app** deliberately pointing to `namespace: prod-demo`. Used to trigger the project destination policy and produce an `InvalidSpecError`. No automated sync.

### `demo-app-fixed.yaml`
Corrected `Application` **demo-app** with `namespace: dev-demo` and `syncPolicy.automated` (prune + selfHeal). Replaces the failing variant and deploys the workload successfully.
