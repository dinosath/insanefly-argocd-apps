# InsaneFly ArgoCD Apps Repo

GitOps repo containing Argo CD Applications, platform services, and apps.

## ArgoCD Deployment Mode

This repository now uses a single Argo CD Application at `argocd/apps/helmfile.yaml`
to render and deploy everything via `helmfile.yaml.gotmpl`.

Requirements:
- Argo CD must have a Config Management Plugin named `helmfile`.
- The plugin must support `HELMFILE_ENVIRONMENT` (defaults to `default`).

Root app:
- `argocd/root.yaml` points to `argocd/apps`.
- `argocd/apps/helmfile.yaml` is the only child Application and drives all platform/app releases.