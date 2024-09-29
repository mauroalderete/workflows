# workflows

<h4 align="center">Collection of resusable workflows for DevOps</h4>

&nbsp;

<div align="center">

<a href="./LICENSE">
	<img alt="License: MIT" src="https://img.shields.io/badge/License-Private-yellow.svg">
</a>
<a href="./CODE_OF_CONDUCT.md">
	<img alt="Contributor covenant: 2.1" src="https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg">
</a>
<a href="https://semver.org/">
	<img alt="Semantic Versioning: 2.0.0" src="https://img.shields.io/badge/Semantic--Versioning-2.0.0-a05f79?logo=semantic-release&logoColor=f97ff0">
</a>

[![Tests](https://github.com/mauroalderete/coding-projects-template/actions/workflows/tests.yml/badge.svg)](https://github.com/mauroalderete/coding-projects-template/actions/workflows/tests.yml)
[![CodeQL](https://github.com/mauroalderete/coding-projects-template/actions/workflows/codeql-analysis.yml/badge.svg)](https://github.com/mauroalderete/coding-projects-template/actions/workflows/codeql-analysis.yml)

<a href="./issues/new?assignees=&labels=bug%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=1-bug-report.yml&title=...+is+broken">Bug Report</a>
·
<a href="./issues/new?assignees=&labels=enhancement%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=2-feature-request.yml&title=As+a+%5Btype+of+user%5D%2C+I+want+%5Ba+goal%5D+so+that+%5Bbenefit%5D">Feature Request</a>
·
<a href="./issues/new?assignees=&labels=help+wanted%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=3-help-wanted.yml&title=I+need+help+with...">Help Wanted</a>

<a href="https://twitter.com/intent/tweet?text=👋%20Check%20this%20amazing%20repo%20https://github.com/mauroalderete/workflows,%20created%20by%20@_mauroalderete%0A%0A%23DEVCommunity%20%23100DaysOfCode%20%23GitHub%20%23DevOps">
	<img src="https://img.shields.io/twitter/url?label=Share%20on%20Twitter&style=social&url=https%3A%2F%2Fgithub.com%2Fatapas%2Fmodel-repo">
</a>

</div>

&nbsp;
## :wave: Introducing `workflows`

This repository contains a collection of reusable workflows to implement some typical DevOps rules in your repositories:

- `labeling` adds labels in your PR to help you classify them using **Conventional Commits**.
- `versioning` increases, tags, and releases new versions of your repos based on **Semantic Versioning** when an RP is merged.
- `project-automation` makes it easier to trace your issues in a **GitHub Project Board**, listening to the developer's collaborations.

## :fire: How to use this workflows

You need use the workflow that you want to use in a job like that:

```yaml
jobs:
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/labeling.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

You can more info in [Resuing workflows](https://docs.github.com/en/actions/sharing-automations/reusing-workflows) documentation

## Labeling

Implements two steps:

- `Assign labels` based on [action-assign-labels](https://github.com/mauroalderete/action-assign-labels)
- `Verify labels` based on [action-verify-labels](https://github.com/mauroalderete/action-verify-labels)

Runs when a new PR is opened or changed. Scan the conventional commits and assign the labels according to a predefined system of matches. You can give a glance for the code [labeling](.github/workflows/labeling.yml) to known more.

If the verificaction ends successful, a approval is load.

You need pass a GITHUB_TOKEN valid

> [!NOTE] 
> If you want receive the approve need the repo enable `Allow GitHub Actions to Create and approve pull request` in `Setting >> Actions >> General >> Workflow permissions`.

For run:

```yaml
jobs:
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/labeling.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Versioning

Checks conventional commits to calculate the next incremental version based on [ietf-tools/semver-action](https://github.com/ietf-tools/semver-action).

The versioning workflow contains the commands to generate a new release.

You need pass a GITHUB_TOKEN valid.

If you want that the `versioning` can trigger other workflow, for example, on releases... you need pass a TOKEN different the GITHUB_TOKEN. You can learn more about [triggering a workflow](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow#triggering-a-workflow-from-a-workflow).

For run with triggering other workflows support:

```yaml
jobs:
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/versioning.yml@v0
    secrets:
      github-token: ${{ secrets.MY_CUSTOM_PAT }}
```

## Project Automation

> [!WARNING]
> The documentation for this workflow is extensive... os is WIP yet. Please read the [workflow source file](.github/workflows/project-automation.yml) directly while.

This template contains a `Project Automation` workflow to perform an issue status managment on project beta. For this workflows works correctly you need configure the secret `GH_PROJECT_AUTOMATION` with a **PAT token in classic mode**.

The PAT token must have permissions for:

- [x] repo
  - [x] repo:status
  - [x] repo_deployment
  - [x] public_repo
  - [x] repo:invite
  - [x] security_events
- [x] admin:org
  - [x] write:org
  - [x] read:org
  - [x] manage_runners:org
- [x] project
  - [x] read:project

## Code of conduct

`/CODE_OF_CONDUCT.md`

This code is based on the covenant code. He is only required to specify an email address to the community to send his messages. Now, this email is alderete.mauro@gmail.com.

## License

This repo is shared with `MIT LICENSE`.
