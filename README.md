# stageproof/renovate-presets

Renovate presets for the Stageproof index badge.

The badge states a chart-defaults report. The text is `no blocking finding (defaults)`, `not checked`, `not indexed`, or the blocking finding. Run Stageproof with your values files before you treat a result as a check of your cluster.

## Badge preset

```json
{
  "extends": ["github>stageproof/renovate-presets"]
}
```

The preset adds one image to pull requests from the `helmv3`, `argocd`, and `flux` managers when the datasource is `helm`:

```text
https://stageproof.github.io/index/badge.svg?chart={{{depName}}}&from={{{currentVersion}}}&registry={{{prBodyDefinitions.stageproofRegistry}}}&to={{{newVersion}}}
```

`chart` is the Helm chart name. On Renovate 39.264.0 that name is `depName` for an HTTP Helm chart. `registry` comes from `prBodyDefinitions`, because that Renovate version rejects `registryUrl` in a template.

`default.json` assigns a repository URL when the chart name appears once in the index catalog of 300 charts on 2026-11-24. These names appear more than once, so they have no repository in the preset: `airflow`, `argo-cd`, `dify`, `keycloak`, `loki`, `minio`, `n8n`, `netbox`, `postgres`, `rancher`, `strimzi-kafka-operator`, `valkey`, `vaultwarden`.

The SVG files are published with the index site. GitHub Pages returns HTTP 404 for `/badge.svg`. The worker in `stageproof/index` answers that query with HTTP 200, and it answers `not indexed` when the pair is absent. That worker is not deployed yet.

## Patch automerge

```json
{
  "extends": [
    "github>stageproof/renovate-presets",
    "github>stageproof/renovate-presets:automerge-patch"
  ]
}
```

`automerge-patch` asks GitHub to auto-merge a pull request when all of these are true:

- the manager is `helmv3`, `argocd`, or `flux`
- the datasource is `helm`
- the update type is `patch`
- the branch's required status checks have passed

`platformAutomerge` uses GitHub auto-merge, so a required Stageproof check can hold the merge. Turn that check on in branch protection. A branch with no required checks can merge a patch as soon as GitHub's own rules pass.

Minor updates, major updates, and OCI Helm charts stay as open pull requests. Renovate classifies an OCI Helm chart as Docker, and this rule matches the Helm datasource only.
