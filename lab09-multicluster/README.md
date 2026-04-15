# Lab 09 — Multi-Cluster GitOps

Single-cluster implementation of the multi-environment pattern using an `ApplicationSet` with a `list` generator. Reuses the demo workload from Lab 02.

## `argocd/`

### `project-platform-fleet.yaml`
`AppProject` **platform-fleet** dedicated to multi-environment workloads. Allows destinations `dev-*`, `stage-*`, and `prod-*` on the in-cluster Kubernetes, plus the standard `Namespace` / `Deployment` / `Service` / `ConfigMap` whitelist.

### `applicationset-fleet.yaml`
`ApplicationSet` **demo-fleet** with a `list` generator producing three Applications that deploy the same workload (`lab02-apps-and-projects/apps/demo`) into three different namespaces:

- `dev-demo-fleet` → `dev-fleet`
- `stage-demo-fleet` → `stage-fleet`
- `prod-demo-fleet` → `prod-fleet`

Uses `goTemplate: true`, automated sync with prune + selfHeal, and `CreateNamespace=true`.

## Mode B — second cluster

Not included in this pilot: the lab environment is a single-node kind cluster. To test the `clusters` generator you would need to register a second cluster in ArgoCD with `argocd cluster add <context>` and swap the `list` generator for a `clusters` generator that selects it via labels.
