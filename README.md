# インストール

## アクションの構成する

これをワークフロー YAML 構成に追加して

```yaml
name: Create Branch on New Issue
on:
  issues:
    types: [opened, assigned]
  issue_comment:
    types: [created]
  pull_request:
    types: [opened, closed]

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
          MAX_TITLE_LENGTH: 30 # Set the maximum title length here
        run: |
          if [[ $GITHUB_EVENT_NAME == "issues" ]]; then
            title=$(jq -r '.issue.title' $GITHUB_EVENT_PATH)
          elif [[ $GITHUB_EVENT_NAME == "issue_comment" ]]; then
            title=$(jq -r '.issue.title' $GITHUB_EVENT_PATH)
          elif [[ $GITHUB_EVENT_NAME == "pull_request" ]]; then
            title=$(jq -r '.pull_request.title' $GITHUB_EVENT_PATH)
          else
            echo "Unsupported event type: $GITHUB_EVENT_NAME"
            exit 1
          fi
          
          event_name=$(echo $GITHUB_EVENT_NAME | tr '[:upper:]' '[:lower:]')
          event_type=$(jq -r '.action' $GITHUB_EVENT_PATH | tr '[:upper:]' '[:lower:]')
          truncated_title=$(echo "$title" | cut -c 1-${MAX_TITLE_LENGTH})
          sanitized_title=$(echo "$truncated_title" | tr -cd '[:alnum:]\n' | tr '[:space:]' '_')
          sanitized_title=$(echo "$sanitized_title" | sed 's/_*$//')
          branch_name="${event_name}-${sanitized_title}-${event_type}"
          echo "Branch Name: $branch_name"
          git checkout -b $branch_name
          git push origin $branch_name

```

### ブランチ名をする

- 支店名の形式を変更する場合は、「branch_name_prefix」 と「branch_name_suffix」を変更する

### トリガーアクションを設定する

- アクションがワークフロー変更をトリガーするかを変更する場合は、「on」セクションで設定する
