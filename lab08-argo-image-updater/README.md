# Lab 08 — Argo Image Updater

Minimal setup to test Argo Image Updater (AIU) against a Kustomize-based workload. AIU is a component separate from ArgoCD core and must be installed explicitly.

## `apps/demo-iu/`

### `deployment.yaml`
nginx Deployment starting from an intentionally old tag (`nginx:1.25-alpine`) so that AIU can discover a newer candidate and update it.

### `service.yaml`
ClusterIP Service targeting the demo-iu pods.

### `kustomization.yaml`
Kustomize base listing `deployment.yaml` + `service.yaml`. Using Kustomize is important: with a plain directory source, AIU requires `write-back-method: git` and a writable repo. With Kustomize, AIU can patch `spec.source.kustomize.images` directly on the live Application using `write-back-method: argocd` (no git credentials needed).

## `argocd/`

### `demo-iu.yaml`
`Application` **demo-iu** pointing to `apps/demo-iu` and annotated for AIU:

- `image-list: web=nginx` — alias `web` maps to container image `nginx` on Docker Hub
- `web.update-strategy: semver` — pick the highest valid semver tag
- `web.allow-tags: regexp:^1\.(2[5-9]|3[0-9])-alpine$` — restricts AIU to alpine tags in a sensible range
- `web.kustomize.image-name: nginx` — tells AIU which kustomize image entry to patch
- `write-back-method: argocd` — AIU updates the live Application, no git commit

Destination `dev-iu`, automated sync with prune + selfHeal.

## Installing Argo Image Updater

Not included as a local manifest — install from upstream:

```bash
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/v0.15.2/manifests/install.yaml
```

Then verify:

```bash
kubectl get deploy -n argocd argocd-image-updater
kubectl logs -n argocd deploy/argocd-image-updater -f
```

AIU polls registries at a default interval of 2 minutes. Be patient.
