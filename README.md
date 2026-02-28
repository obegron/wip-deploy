# vcluster + Kustomize + Argo CD Scaffold

This repository provides a starter implementation for deploying third-party Helm charts into an isolated `vcluster` using GitOps.

## Goals

- One `vcluster` per product.
- No manual kubectl/control-plane tinkering.
- Argo CD is the only reconciler.
- Third-party charts are deployed through Kustomize (`helmCharts`) with chart/version pinning.
- Images are rewritten to internal mirrors and validated before commit.

## Layout

- `cfg-repo/`: user intent and product configuration.
- `gitops-repo/`: generated desired state for Argo CD.
- `vcluster-catalog/`: channel-based source of approved vcluster chart versions/defaults.
- `tekton/`: pipeline and task templates.
- `docs/`: rollout and operations notes.

## Flow

1. User updates `cfg-repo/products/<product>/product.yaml`.
2. Infra Tekton pipeline reads `vcluster-catalog` channel and generates/validates `infra` state.
3. App Tekton pipeline generates/validates `apps` state and image registry compliance.
4. Pipelines write changes to `gitops-repo/clusters/<product>/...`.
5. Argo CD reconciles infra and app Applications.

## Builder Image Contract

Pipelines require a `builder-image` param. That image must already contain:
- `bash`
- `git`
- `yq` (mikefarah, v4+)
- `kustomize` (v5+)
- `helm` (v3+)

## Pipelines

- `tekton/pipeline-vcluster-infra-sync.yaml`: vcluster infra generation/validation from cfg + catalog inputs.
- `tekton/pipeline-app-sync.yaml`: app release generation and mirror-image validation.

See `docs/rollout.md` for the bootstrap sequence.
See `docs/vcluster-catalog-contract.md` for the catalog repo format.
