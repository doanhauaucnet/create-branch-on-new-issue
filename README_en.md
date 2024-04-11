# Installation

## Configure GitHub Action

Add this to your workflow YAML configuration:

```yaml
name: Create Branch on New Issue
on:
  # [opened, edited, deleted, transferred, pinned, unpinned, closed, reopened, assigned, unassigned]
  issues:
    types: [ opened, assigned ]
  # [created, edited, deleted]
  issue_comment:
    types: [ created ]
  # [opened, closed, synchronize]
  pull_request:
    types: [ opened, closed ]

permissions:
  contents: write

jobs:
  create_branch_job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      - name: Create Branch
        env:
          PAT: ${{ secrets.GITHUB_TOKEN }}
          branch_name_prefix: "issue-"
          branch_name_suffix: ""
        run: |
          issue_title=$(jq -r '.issue.title' $GITHUB_EVENT_PATH)
          branch_name="${branch_name_prefix}$(echo $issue_title | tr '[:upper:]' '[:lower:]' | tr ' ' '-')${branch_name_suffix}"
          git checkout -b $branch_name
          git push origin $branch_name
```

### Configure branch name

- For changing branch name format, change in "branch_name_prefix" and "branch_name_suffix"

### Configure trigger action

- For changing which action will trigger workflow change "on" section
