# workflows <!-- omit in toc -->

***Collection of resusable workflows for DevOps***

<div align="center">

&nbsp;

[![License: MIT](https://img.shields.io/badge/License-Private-yellow.svg)](./LICENSE)
[![Contributor covenant: 2.1](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg)](./CODE_OF_CONDUCT.md)
[![Semantic Versioning: 2.0.0](https://img.shields.io/badge/Semantic--Versioning-2.0.0-a05f79?logo=semantic-release&logoColor=f97ff0)](https://semver.org/)

[![Labeling](https://github.com/mauroalderete/workflows/actions/workflows/labeling.yml/badge.svg)](https://github.com/mauroalderete/workflows/actions/workflows/labeling.yml)
[![Liberation](https://github.com/mauroalderete/workflows/actions/workflows/liberation.yml/badge.svg)](https://github.com/mauroalderete/workflows/actions/workflows/liberation.yml)
[![Project Automations](https://github.com/mauroalderete/workflows/actions/workflows/project-automation.yml/badge.svg)](https://github.com/mauroalderete/workflows/actions/workflows/project-automation.yml)

[Bug Report](./issues/new?assignees=&labels=bug%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=1-bug-report.yml&title=...+is+broken)
⭕
[Feature Request](./issues/new?assignees=&labels=enhancement%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=2-feature-request.yml&title=As+a+%5Btype+of+user%5D%2C+I+want+%5Ba+goal%5D+so+that+%5Bbenefit%5D)
⭕
[Help Wanted](./issues/new?assignees=&labels=help+wanted%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=3-help-wanted.yml&title=I+need+help+with...)

[![Share on X](https://img.shields.io/twitter/url?label=Share%20on%20Twitter&style=social&url=https%3A%2F%2Fgithub.com%2Fatapas%2Fmodel-repo)](https://twitter.com/intent/tweet?text=👋%20Check%20this%20amazing%20repo%20https://github.com/mauroalderete/workflows,%20created%20by%20@_mauroalderete%0A%0A%23DEVCommunity%20%23100DaysOfCode%20%23GitHub%20%23DevOps)

&nbsp;

</div>

- [Introducing `workflows`](#introducing-workflows)
- [How to use this workflows](#how-to-use-this-workflows)
- [Workflows](#workflows)
  - [Labeling](#labeling)
  - [Versioning](#versioning)
  - [Release](#release)
  - [Project Automation](#project-automation)
    - [Entry](#entry)
    - [Progression](#progression)
    - [Termination](#termination)
- [Code of conduct](#code-of-conduct)
- [License](#license)


## Introducing `workflows`

This repository contains a collection of reusable workflows to implement some typical DevOps rules in your repositories:

- `labeling` adds labels in your PR to help you classify them using **Conventional Commits**.
- `versioning` calculate the next version tags and create them based on **Semantic Versioning**.
- `release` creates a release the last tag pushed attaching artifacts if you want.
- `liberation` combine `versioning` and `release` workflows in a simple flow to apply CI in repos that you don't need publish release with artifacts.
- `project-automation` makes it easier to trace your issues in a **GitHub Project Board**, listening to the developer's collaborations.

## How to use this workflows

You need use the workflow that you want to use in a job like that:

```yaml
jobs:
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/labeling.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

You can more info in [Resuing workflows](https://docs.github.com/en/actions/sharing-automations/reusing-workflows) documentation

## Workflows

### Labeling

Implements two steps:

- `Assign labels` based on [action-assign-labels](https://github.com/mauroalderete/action-assign-labels)
- `Verify labels` based on [action-verify-labels](https://github.com/mauroalderete/action-verify-labels)

Scans the conventional commits prefix from last commit tagged to the last commit in the current PR. Calculate the labels to assign the PR according to a predefined system of matches knowledge lik **Conventional Commits Scheme**.

If the verificaction ends successful an approval is attached on the PR.

This workflows is thinking to triggered when a PR is opened, reopened, synchronize, labeled, or unlabeled.

You can give a glance for the code [labeling](.github/workflows/labeling.yml) to known more about how works and known the default rules applieds.

You need pass a GITHUB_TOKEN valid.

Remember set `pull-request: write` permission to allow that workflow creates an approvals on PR.

> [!NOTE] 
> If you want receive the approve need the repo enable `Allow GitHub Actions to Create and approve pull request` in `Setting >> Actions >> General >> Workflow permissions`.

For run:

```yaml
jobs:
  permissions:
    pull-requests: write
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/labeling.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Versioning

Checks conventional commits to calculate the next incremental version based on [ietf-tools/semver-action](https://github.com/ietf-tools/semver-action).

Once time the tag is calculated, create three tags over the last commit and push them:

- Patch tag version `v0.0.0`
- Minor tag version `v0.0`
- Major tag version `v0`

That allows mantein the incremental versioning tracing updated.

You need pass a GITHUB_TOKEN valid.

> [!NOTE]
> If you want that the `versioning` can trigger other workflow, for example, for start a deployment processes... you need pass a TOKEN different the `secrets.GITHUB_TOKEN`. You can learn more about [triggering a workflow](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow#triggering-a-workflow-from-a-workflow).

Remember set `contents: write` permission to allow that workflow push the tags created.

For run with triggering other workflows support:

```yaml
jobs:
  permissions:
    contents: write
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/versioning.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Release

Create and publish a new release based on a tag passed for `inputs`. Allows including the files such as building artifacts, summaries, images or any other file to attach them into the release

Internally powered by the [actions/download-artifact](https://github.com/actions/download-artifact) action.

For use the including files on the release you need realize two steps:

- First, prepare the files to attach using [actions/upload-artifact](https://github.com/actions/upload-artifact).
- Enable the input `include-artifacts: true` to the workflow download the artifacts to attach.

Optionally, you can use the input `artifacts-name: '...'` to determine the name of files that the workflow must to be attach. This is util if you use [actions/upload-artifact](https://github.com/actions/upload-artifact) for other processes that you don't want to be considered by `release` workflow.

If you enable `include-artifacs` and don't pass any `artifacts-name` then the `release` workflow attach all files found for the current run.

`release` workflow only attach files. If you want attach a folder, please, compress the folder before use [actions/upload-artifact](https://github.com/actions/upload-artifact).

> Thats features and limitations are the same that from the [actions/download-artifact](https://github.com/actions/download-artifact).

You need pass a GITHUB_TOKEN valid.

> [!NOTE]
> If you want that the `release` can trigger other workflow, for example, for start a deployment processes... you need pass a TOKEN different the `secrets.GITHUB_TOKEN`. You can learn more about [triggering a workflow](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow#triggering-a-workflow-from-a-workflow).

Remember set `contents: write` permission to allow that workflow create a releae.

For run with triggering other workflows support:

```yaml
jobs:
  permissions:
    contents: write
  call-labeling-workflow:
    uses: mauroalderete/workflows/.github/workflows/release.yml@v0
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      next: 'v1.0.3'
```

### Project Automation

> [!WARNING]
> The documentation for this workflow is extensive... os is WIP yet. Please read the [workflow source file](.github/workflows/project-automation.yml) directly while.

`project-automation` is a reusable workflow to perform an issue status managment on **GitHub Project**.

Is based on a `Kanban Flow` composed by trhee principal sections:

- Entry
- Progression
- Termination

#### Entry

Here you found the new issues created with ISSUE_TEMPLATES. Thats are separate on three statuses:

- Bug
- Enhancement
- Help Wanted

When a new issue is created, the issue template assign can assign a label. `project-automation` is triggered then and scan the labels to known to that status move the issue.

Is recomended that issue templates link other label like `lifecycle/needs-triage` to easily filters.

#### Progression

This sections of status is the mose extensive in the current flow approaching. Consist in some manual and automatical steps. Each one of them are represented for a status, condition and intent in an no exactly linear flow.

- **Triage** [Manual]: Here to go all new issues presented in Entry sections, usually with `lifecycle/needs-triage`, that you start to the evaluation or discussion process. In this step you would remove `lifecycle/needs-triage`.
- **Backlog** [Manual]: The issues that pass the Triage status, and contains all information needed to resolve the issue are moved to Backlog status. Doesn't know when will start resolving or who or his difficult or impact.
- **Sprint** [Automatical]: An issue is moved to this status when it is assigned to somebody. In this moment is consider that the issue is ready to start works.
- **In Progress** [Manual]: Tipically is moved here when the assigned create a new branch to start resolving the issue.
- **In Review** [Automatical]: The issues are placed here when a PR is loaded.

#### Termination

- Done [Automatical]: Classic moves. When a issue is closed like completed. Usually occure when his PR linked is merged, but when GitHub detect some issue reference in some message with prefix Closed or Fixed too.
- Trash [Automatical]: Here to go all issues closed with some discarded type label like `invalid`, `wontfix`, `duplciated`.

> [!NOTE]
> All this labels names, and status columns names are configurables. Checks inputs for the `project-automation` workflow.

> [!WARNING]
> This workflow show only a way of tracing your issues, but is only a way that works in specifical context. This is a WIP so it could changes in any moment. Feel free to propose others perspectives to face new mode of works.

Needs setup `repository-projects: write` in caller job, and you need configure the secret `GH_PROJECT_AUTOMATION` with a **PAT token in classic mode**.

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

You can use this workflow:

```yaml
name: Project Automations

on:
  issues:
    types: 
      - opened
      - closed
      - assigned
  pull_request:
    types: 
      - opened

permissions:
  repository-projects: write

jobs:
  call-project-automation-workflow:
    uses: mauroalderete/workflows/.github/workflows/project-automation.yml@v0
    secrets:
      GH_PROJECT_AUTOMATION: ${{ secrets.GH_PROJECT_AUTOMATION }}
```

## Code of conduct

`/CODE_OF_CONDUCT.md`

This code is based on the covenant code. He is only required to specify an email address to the community to send his messages. Now, this email is alderete.mauro@gmail.com.

## License

This repo is shared with `MIT LICENSE`.
