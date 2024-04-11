# インストール

## ステップ 1. リポジトリ設定を構成する

- リポジトリの設定ページへのアクセス

- 「Actions」->「General」に移動する

- 「Workflow permissions」で「Read and write permissions」を選択する

## ステップ 2. GitHub アクションの構成する

これをワークフロー YAML 構成に追加して

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

### ブランチ名をする

- 支店名の形式を変更する場合は、「branch_name_prefix」 と「branch_name_suffix」を変更する

### トリガーアクションを設定する

- アクションがワークフロー変更をトリガーするかを変更する場合は、「on」セクションで設定する
