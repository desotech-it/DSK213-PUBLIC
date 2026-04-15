# Lab 07 — App-of-Apps and ApplicationSet

Two ArgoCD patterns for managing many applications at once, built on the same workloads so their trade-offs can be compared.

## `apps/`

Two minimal nginx workloads used as children.

### `child-a/deployment.yaml` + `service.yaml`
nginx 1.27-alpine Deployment (1 replica) + ClusterIP Service. Distinctive label `child: a`.

### `child-b/deployment.yaml` + `service.yaml`
nginx 1.28-alpine Deployment (1 replica) + ClusterIP Service. Distinctive label `child: b`.

## `argocd/`

### `project-platform-meta.yaml`
`AppProject` **platform-meta** dedicated to the app-of-apps and ApplicationSet patterns. Unlike `platform-dev`, it also allows:
- destination namespace `argocd` (where root Applications and ApplicationSets live)
- `argoproj.io/Application` and `argoproj.io/ApplicationSet` in the namespaced resource whitelist

## `root/`

### `app-of-apps.yaml`
Root `Application` **app-of-apps**. Destination namespace `argocd`. `directory.recurse: false` so only the top-level files in `root/children/` are rendered. Automated sync + selfHeal: the root recreates any child `Application` that is deleted out of band.

### `root/children/child-a.yaml` / `child-b.yaml`
Child `Application` manifests. They target `lab07-.../apps/child-a` and `child-b` and deploy into `dev-child-a` and `dev-child-b`.

## `applicationset/`

### `demo.yaml`
`ApplicationSet` **set-demo** with a `list` generator producing two Applications:
- `set-child-a` → `apps/child-a`, namespace `dev-set-child-a`
- `set-child-b` → `apps/child-b`, namespace `dev-set-child-b`

Uses `goTemplate: true` and references each list element via `.name`, `.path`, `.namespace` in the Application template. The generated Applications share the same spec shape, only metadata and destination change.
