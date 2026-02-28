# Rollout Guide

## 1. Bootstrap Infra App

Apply `gitops-repo/clusters/<product>/infra/argocd-application.yaml` to Argo CD in the host cluster.

Outcome:
- Product namespace exists.
- vcluster is installed/updated by Argo CD.

## 2. Register vcluster in Argo CD

Register the vcluster API as a destination cluster using machine credentials only.
Do not distribute human kubeconfigs.

## 3. Bootstrap Apps App

Apply `gitops-repo/clusters/<product>/apps/argocd-application.yaml`.

Outcome:
- Argo CD deploys generated app releases into the vcluster.

## 4. Pipeline-driven Updates

Run Tekton `vcluster-infra-sync` and `product-app-sync` with:
- `product=<product-name>`
- `builder-image=<your-tooling-image>`
For `vcluster-infra-sync`, bind three input workspaces:
- `cfg-repo` (product config repo)
- `gitops-repo` (target repo to update)
- `vcluster-catalog` (approved/latest vcluster chart channel)
For `product-app-sync`, bind two input workspaces:
- `cfg-repo` (product config repo)
- `gitops-repo` (target repo to update)

Recommended extension:
- Add a final Tekton task to commit and push `gitops-repo` updates.
- Gate merges with mirror-validation success.
- Add a dedicated Vault/ESO bootstrap step before onboarding secret-dependent apps.

## Notes

- The sample includes a Kyverno policy for registry restrictions. Ensure Kyverno exists in vcluster.
- For Vault integration, add External Secrets Operator in vcluster and map per-app service accounts to Vault roles.
