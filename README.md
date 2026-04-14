# DSK213-PUBLIC

Application manifests used during DSK213 workshop, organized by lab.

## Layout

```
lab02-apps-and-projects/
  apps/demo/                    workload used by Lab 02 demo-app
  argocd/                       AppProject + Application CRs for Lab 02

lab04-sync-waves-hooks/
  apps/ordered-app/             workload with sync-wave annotations + PreSync hook
  argocd/                       updated AppProject + Application CR for Lab 04
  exercises/
    ex1-postsync/               extra manifest for the PostSync exercise
    ex2-failing-presync/        extra manifest for the failing-PreSync exercise
```
