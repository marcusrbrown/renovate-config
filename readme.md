# Renovate Config Presets

<div align='center'>

[![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/marcusrbrown/renovate-config?sort=semver&style=for-the-badge&logo=github&label=release)][release] [![GitHub Workflow Main Status](https://img.shields.io/github/actions/workflow/status/marcusrbrown/renovate-config/main.yaml?branch=main&style=for-the-badge&logo=github%20actions&logoColor=white&label=main)][ci-workflow] [![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/marcusrbrown/renovate-config/badge?style=for-the-badge)][ossf-scorecard]

[release]: https://github.com/marcusrbrown/renovate-config/releases "GitHub release"
[ci-workflow]: https://github.com/marcusrbrown/renovate-config/actions?query=workflow%3Amain "Search for `main` workflow runs"
[ossf-scorecard]: https://securityscorecards.dev/viewer/?uri=github.com/marcusrbrown/renovate-config "View OpenSSF Scorecard"

</div>

Shareable [Renovate](https://docs.renovatebot.com/) configuration presets for my personal GitHub repositories.

## Available Presets

| Preset | Description | Usage |
| --- | --- | --- |
| [`default`](default.json) | Main preset for all `@marcusrbrown` repositories. Extends `bfra-me/renovate-config` with personal customizations, automerge rules, and package grouping. | `extends: ["github>marcusrbrown/renovate-config"]` |
| [`onboarding`](onboarding.json) | Configuration used when onboarding new repositories to Renovate. | `extends: ["github>marcusrbrown/renovate-config:onboarding"]` |
| [`archived-repository`](archived-repository.json) | Preset for archived repositories to disable dependency updates and autoclose dashboards. | `extends: ["github>marcusrbrown/renovate-config:archived-repository"]` |

## Usage

Add one of the following to your repository's `.github/renovate.json5`:

```json5
{
  $schema: 'https://docs.renovatebot.com/renovate-schema.json',
  extends: ['github>marcusrbrown/renovate-config'],
}
```

To pin to a specific major version:

```json5
{
  $schema: 'https://docs.renovatebot.com/renovate-schema.json',
  extends: ['github>marcusrbrown/renovate-config#v5'],
}
```
