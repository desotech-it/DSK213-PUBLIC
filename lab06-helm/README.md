# Lab 06 — Helm

Helm chart `demo-app` and its ArgoCD `Application`, used to demonstrate how ArgoCD renders and syncs Helm charts via `valueFiles` and `parameters`.

## `charts/demo-app/`

### `Chart.yaml`
Minimal chart metadata (`apiVersion: v2`, `type: application`, `version: 0.1.0`, `appVersion: "1.27"`).

### `values.yaml`
Default values. Deploys nginx 1.27, 1 replica, ClusterIP on port 80, `labels.env=default`, standard resource requests/limits.

### `values-dev.yaml`
Dev overrides: 2 replicas, image tag `1.27-alpine`, `labels.env=dev`. Only the fields that differ from the defaults are listed — Helm merges them over `values.yaml`.

### `templates/_helpers.tpl`
Template helpers: `demo-app.name`, `demo-app.labels` (full label set including user-provided `labels` map), `demo-app.selectorLabels` (minimal selector labels).

### `templates/deployment.yaml`
nginx Deployment templated with values for replicas, image, resources and labels. Uses `.Values.image.repository:.Values.image.tag` for the image reference.

### `templates/service.yaml`
Service templated with values for `type` and `port`. Targets the `http` named port on the pods.

## `argocd/`

### `demo-helm.yaml`
`Application` **demo-helm** pointing to `lab06-helm/charts/demo-app` with `source.helm.valueFiles: [values-dev.yaml]`. Automated sync + prune + selfHeal, destination namespace `dev-helm` (matches the `dev-*` contract from the `platform-dev` AppProject).
