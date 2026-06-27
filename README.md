# InsaneFly ArgoCD Apps Repo

GitOps repo containing Argo CD Applications, platform services, and apps.

## ArgoCD Deployment Mode

This repository uses pure ArgoCD with native Helm support — no Config Management Plugins required.

Each upstream chart is managed by its own `Application` resource in `argocd/apps/`:

| Tier | Prefix | Applications |
|------|--------|--------------|
| Operators | `01-*` | cnpg-operator, keda, vault-operator, external-secrets |
| Platform | `02-*` | clickhouse, platform-resources |
| Services | `03-*` | vector, pgadmin, coder, ferriskey |

Sync ordering is achieved via alphabetical file naming — ArgoCD processes `01-*` before `02-*` before `03-*`.

Root app:
- `argocd/root.yaml` points to `argocd/apps` and auto-discovers all child Applications.