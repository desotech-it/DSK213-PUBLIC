# Lab 05 — Kustomize

Manifests for the `demo-kustomize` workload, packaged with a Kustomize `base` and two overlays (`dev`, `stage`).

## `apps/demo-kustomize/base/`

### `deployment.yaml`
nginx Deployment (1 replica by default) with basic resource limits. Overlay-agnostic.

### `service.yaml`
ClusterIP Service on port 80 selecting the nginx pods.

### `kustomization.yaml`
Base kustomization listing `deployment.yaml` and `service.yaml` as the only resources. No transformations.

## `apps/demo-kustomize/overlays/dev/`

### `kustomization.yaml`
Dev overlay. Consumes `../../base`, applies:
- `labels.pairs.env=dev` (added to objects, not selectors)
- `images[name=nginx].newTag=1.27-alpine`
- a strategic-merge patch from `replica-patch.yaml` to set `replicas: 1`

### `replica-patch.yaml`
Strategic-merge patch bringing `spec.replicas` to `1`.

## `apps/demo-kustomize/overlays/stage/`

### `kustomization.yaml`
Stage overlay. Same shape as `dev`, but:
- `labels.pairs.env=stage`
- `images[name=nginx].newTag=1.28-alpine`
- patch `replica-patch.yaml` → `replicas: 3`

### `replica-patch.yaml`
Strategic-merge patch bringing `spec.replicas` to `3`.

## `argocd/`

### `demo-kustomize.yaml`
`Application` **demo-kustomize** pointing to `overlays/dev`. Automated sync (prune + selfHeal), destination `dev-demo`.

### `demo-kustomize-stage.yaml`
Alternate `Application` manifest pointing to `overlays/stage`. Used by the exercise that switches the overlay.
