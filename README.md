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

## Local Testing (helm + kubeconform + argocd)

This project uses [mise](https://mise.jdx.dev) to manage tools and run validation tasks.

```bash
# Install mise (one-time)
curl https://mise.run | sh
eval "$(mise activate bash)"   # add this to ~/.bashrc

# Install tools (helm, argocd, python)
cd insanefly-argocd-apps
mise install
mise run setup               # also downloads kubeconform

# Run validation
mise run validate            # helm lint + kubeconform on all manifests
mise run helm-lint           # lint Helm charts only
mise run kubeconform         # K8s schema validation (ArgoCD apps + Helm output)
mise run validate-argocd     # validate ArgoCD Application YAMLs only
mise tasks                   # list all tasks
```

**Validation tools:**

| Tool | Purpose | Install |
|------|---------|---------|
| `helm` | Chart linting & template rendering | `mise install` |
| `kubeconform` | K8s + CRD schema validation | `mise install` |
| `argocd` | CLI for ArgoCD operations | `mise install` |

**What's checked by `mise run kubeconform`:**
1. All `argocd/apps/*.yaml` + `argocd/root.yaml` — validated against ArgoCD CRD schemas
2. `helm template helm/platform-resources` output — validated against K8s + CRD schemas

## CI / GitHub Actions

Every push to `main` and every PR runs validation via `.github/workflows/validate.yml`.

| Job | Task | What it checks |
|-----|------|----------------|
| Helm lint | `helm-lint` | `helm lint ./helm/platform-resources` |
| K8s schema | `kubeconform` | ArgoCD apps + rendered Helm templates against K8s/CRD schemas |
