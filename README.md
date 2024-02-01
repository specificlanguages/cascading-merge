# Overview

Automates cascading merges of MPS projects.

The action is expected to be called from an on-push workflow for a branch specific to a certain MPS version. It will
create a PR to merge the current maintenance branch into the next maintenance branch (in alphabetic order), or the
default branch if there is no next maintenance branch.

If a pull request for the current branch exists already, the action will succeed without creating a new branch.

The user that pushed the changes is added as an assignee and a reviewer to the PR.

The action creates a "merge" branch from the original branch where any merge conflicts can be fixed, or further
adjustments, such as migrations, can be performed before the branch is merged.

# Parameters

See the [action description file](action.yml) for a list of parameters.

# Example usage

```yaml
name: PR to merge into the next version

on:
  push:
    branches:
      - 'maintenance/mps*'

jobs:
  create-pr:
    runs-on: ubuntu-latest
    steps:
      - name: Create PR
        uses: specificlanguages/cascading-merge@v2
        with:
          branch_prefix: maintenance/MPS-
          exclude_branch_prefix: maintenance/MPS-3
          merge_prefix: merge/MPS-
          default_merge_prefix: merge/
          update_from_base: true
          token: ${{ secrets.MY_TOKEN }}
```

Given the above workflow, when a user pushes to a branch called `maintenance/MPS-2021.3`, a PR will be automatically
created to merge `maintenance/MPS-2021.3` into `maintenance/MPS-2022.2`, if the latter exists.

In order to create the PR, a branch called `merge/MPS-2022.2` will be created at first (i.e. the name is based on the
destination branch of the PR). The branch starts at `maintenance/MPS-2021.3`. If the branch already exists, it will be
updated with the latest state of `maintenance/MPS-2021.3` (fast-forwarded if possible, merged otherwise).

If it is not possible to update the merge branch to contain all the new changes, the action will fail.

Otherwise, the action will optionally merge `maintenance/MPS-2022.2` into the merge branch, if `update_from_base` is set
to `true`.

When merging to the default branch, `default_merge_prefix` is used instead of `merge_prefix` to construct the branch
name.

The user that triggered the action (`github.actor`) will be added as both the assignee and the reviewer of the pull
request. This is going to be the user that pushed the original change or performed the preceding cascading merge.

The action succeeds if the PR was created and could be updated from the original branch, even if some intermediate steps
(such as updating the branch from base or adding the reviewer) fail.

# Outputs

The action outputs the PR number (`pr_number`) and PR URL (`pr_url`).

# Custom GitHub token

If `token` is provided, it is used for repository operations. Otherwise, the repository token (`github.token`) is used.
Any actions or API requests performed with the repository token do not trigger workflows, so, for example, PRs created
by this action will not have PR checks triggered for them.

Therefore, if you rely on GitHub Workflows for your PR checks, be sure to provide a custom token. A fine-grained token
can be used, the required permissions are read/write access to code (repository content) and pull requests.
