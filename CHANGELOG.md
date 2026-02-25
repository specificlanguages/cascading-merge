# 2.0.1

## Fixed

- Creating a new PR would fail if the GitHub actor was not eligible to be added as a reviewer or assignee. This could
  happen, e.g., for a PR merged with Renovate's scheduled non-platform automerge functionality. The reviewer and 
  assignee are now added to the new PR in a separate command.

# 2.0.0

- `specificlanguages/cascading-merge` is now an action rather than a workflow, to improve composability.
- `update_from_base` default changed to `false`.

# 1.1.0

- Updating the merge branch from the base branch is now controlled by `update_from_base` parameter. The default is to
  update, for backward compatibility.

# 1.0.0

- Initial version.
