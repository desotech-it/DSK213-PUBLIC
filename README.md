# DSK213-PUBLIC

Application manifests used during DSK213 workshop, organized by lab.

## Layout

```
lab01-installing-argocd/        ArgoCD v3.3.0 upstream install manifest
lab02-apps-and-projects/        AppProject, Applications, demo workload
lab04-sync-waves-hooks/         ordered-app (waves, PreSync/PostSync hooks) + exercises
lab05-kustomize/                demo-kustomize (base + dev/stage overlays)
lab06-helm/                     demo-app Helm chart with values files
lab07-app-of-apps-and-applicationset/  both patterns sharing the same workloads
lab08-argo-image-updater/       demo-iu kustomize app with AIU annotations
```

Each lab folder has its own `README.md` describing what every manifest does.
