# Overview

A reusable GitHub Actions [workflow](.github/workflows/workflow.yml) for automating cascading merges of MPS projects.

The workflow is expected to be called on a push event to a branch specific for a certain MPS version. It will create
a PR to merge the current maintenance branch into the next maintenance branch (in alphabetic order), or the default
branch if there is no next maintenance branch.

If a pull request for the current branch exists already, the workflow will succeed without creating a new branch.

The user that pushed the changes is added as an assignee and a reviewer to the PR.

The workflow creates an intermediate "merge" branch in order to have the PR branch be up to date with the target
branch.

# Parameters

See the [workflow file](.github/workflows/workflow.yml) for a list of parameters.

# Example usage

```yaml
name: PR to merge into the next version

on:
  push:
    branches:
      - 'maintenance/MPS-*'

jobs:
  create-pr:
    uses: specificlanguages/cascading-merge/.github/workflows/workflow.yml@v1
    with:
      branch_prefix: maintenance/MPS-
      exclude_branch_prefix: maintenance/MPS-3
      merge_prefix: merge/MPS-
      update_from_base: false
```

Given the above workflow, when a user pushes to a branch called `maintenance/MPS-2021.3`, a PR will be automatically
created to merge `maintenance/MPS-2021.3` into `maintenance/MPS-2022.2`, if the latter exists.

In order to create the PR, a branch called `merge/MPS-2021.3` will be created at first. The branch starts at
`maintenance/MPS-2021.3` and the workflow will merge `maintenance/MPS-2022.2` into it. If the attempt fails, the PR is
still created but the merge has to be performed manually.
