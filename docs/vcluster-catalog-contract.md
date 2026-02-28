# vcluster-catalog Contract

The infra pipeline expects a separate repository mounted as workspace `vcluster-catalog`.

## Required structure

- `channels/<channel>/vcluster.yaml`

Example:
- `channels/stable/vcluster.yaml`

## File format

```yaml
apiVersion: platform.example.io/v1alpha1
kind: VClusterRelease
spec:
  channel: stable
  chart:
    repo: https://charts.loft.sh
    name: vcluster
    version: 0.29.0
  values: {}
```

## Merge behavior

The pipeline merges values as:

1. `vcluster-catalog` defaults: `.spec.values`
2. product overrides: `.spec.vcluster.values`

Product values take precedence.
