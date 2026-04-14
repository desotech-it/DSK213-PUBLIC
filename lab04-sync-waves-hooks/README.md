# Lab 04 — Sync Waves and Hooks

Manifests for the `ordered-app`, which demonstrates resource ordering via `argocd.argoproj.io/sync-wave` annotations and a `PreSync` hook.

## `apps/ordered-app/`

### `configmap.yaml`
`ConfigMap` **ordered-app-config** with `sync-wave: "-1"` → applied before everything else. Holds non-sensitive environment keys consumed by the Deployment via `envFrom`.

### `pre-sync-job.yaml`
`Job` **ordered-app-presync** annotated with `hook: PreSync` and `sync-wave: "0"`. Busybox container simulating a short pre-deployment migration (`echo` + `sleep 3`). Runs **before** the main sync phase. `hook-delete-policy: BeforeHookCreation` ensures the previous run is removed before a new execution.

### `deployment.yaml`
nginx 1.27-alpine Deployment with `sync-wave: "1"`. Consumes `ordered-app-config` via `envFrom`. Applied only after the PreSync Job has succeeded.

### `service.yaml`
ClusterIP Service `ordered-app` with `sync-wave: "1"`. Used by the PostSync validation exercise to probe the application over the cluster DNS.

## `argocd/`

### `project-platform-dev.yaml`
Updated version of the **platform-dev** `AppProject` from Lab 02. Adds `batch/Job` to the namespaced resource whitelist so PreSync and PostSync hooks are allowed by project policy. Everything else is unchanged.

### `ordered-app.yaml`
`Application` **ordered-app** pointing to `lab04-sync-waves-hooks/apps/ordered-app`. Automated sync with prune + selfHeal, `CreateNamespace=true`, destination namespace `dev-demo`.

## `exercises/`

### `ex1-postsync/post-sync-job.yaml`
`Job` **ordered-app-postsync** annotated with `hook: PostSync` and `sync-wave: "2"`. Busybox container that retries `wget` against the Service (`ordered-app.dev-demo.svc.cluster.local`) up to 10 times; exits 0 on success. Demonstrates that PostSync hooks run **after** the main wave and can be used for end-to-end validation.

### `ex2-failing-presync/pre-sync-job-failing.yaml`
Alternate version of the PreSync Job that exits with status `1`. Used to demonstrate that when a PreSync hook fails, the main sync phase is aborted and the workload is not advanced (`Phase: Failed`, "one or more synchronization tasks completed unsuccessfully").
