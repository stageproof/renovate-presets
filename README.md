# stageproof/renovate-presets

Renovate presets for the Stageproof index badge.

The badge states a chart-defaults report. The text is `no blocking finding (defaults)`, `not checked`, `not indexed`, or the blocking finding. Run Stageproof with your values files before you treat a result as a check of your cluster.

The preset files are Apache-2.0. See [LICENSE](LICENSE).

## Badge preset

```json
{
  "extends": ["github>stageproof/renovate-presets"]
}
```

The preset adds one image to pull requests from the `helmv3`, `argocd`, and `flux` managers when the datasource is `helm`:

```text
https://stageproof-index-badges.stageproof.workers.dev/badge.svg?chart={{{depName}}}&from={{{currentVersion}}}&registry={{{prBodyDefinitions.stageproofRegistry}}}&to={{{newVersion}}}
```

`chart` is the Helm chart name. On Renovate 44.263.1 that name is `depName` for an HTTP Helm chart, as it was on 39.264.0. `registry` comes from `prBodyDefinitions`.

`default.json` assigns a repository URL when the chart name and the Helm registry both match an HTTP chart in the index catalog of 300 charts. A trailing slash on the registry is ignored. These names occur in more than one repository, so the registry chooses the URL: `airflow`, `argo-cd`, `dify`, `keycloak`, `loki`, `minio`, `n8n`, `netbox`, `postgres`, `rancher`, `strimzi-kafka-operator`, `valkey`, `vaultwarden`. OCI charts are not assigned here.

The SVG files are published with the index site. GitHub Pages returns HTTP 404 for `/badge.svg`. The worker at `https://stageproof-index-badges.stageproof.workers.dev` answers that query with HTTP 200. It answers `not indexed` when the pair is absent, and also when the nightly site has not published that SVG yet.

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
