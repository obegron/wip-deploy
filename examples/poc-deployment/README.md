# POC Deployment Example

This directory shows one end-to-end deployment shape for `example-product`.

## Files

- `01-vcluster-infra-pipelinerun.yaml`: runs infra generation/validation.
- `02-product-app-pipelinerun.yaml`: runs app generation/validation.
- `10-argocd-infra-app.yaml`: Argo CD Application for host-cluster infra.
- `11-argocd-apps-app.yaml`: Argo CD Application for vcluster workloads.
- `20-argocd-vcluster-cluster-secret.template.yaml`: template for Argo CD cluster registration.

## Order

1. Run `01-vcluster-infra-pipelinerun.yaml`.
2. Commit/push generated `gitops-repo` changes.
3. Apply `10-argocd-infra-app.yaml`.
4. Create Argo cluster secret from `20-argocd-vcluster-cluster-secret.template.yaml`.
5. Apply `11-argocd-apps-app.yaml`.
6. Run `02-product-app-pipelinerun.yaml` for app updates, then commit/push.

## Required substitutions

Replace placeholders:

- `<builder-image>`
- `<cfg-repo-pvc>`
- `<gitops-repo-pvc>`
- `<vcluster-catalog-pvc>`
- `<argocd-token>`
- `<vcluster-ca-b64>`
- `<gitops-repo-url>`
- `<gitops-branch>`

## Optional product config

`cfg-repo/products/<product>/product.yaml` can include `spec.secretSyncPull` to
generate and deploy a namespaced `secret-sync-controller` in pull mode.
