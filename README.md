# sync-files Action

GitHub Action that synchronizes a file or directory from another GitHub repository to the current repository via Pull Request.

## Usage

```yaml
name: Sync Files

on:
  schedule:
    - cron: '0 0 * * *' # daily at midnight
  workflow_dispatch:

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:

      - name: Sync file from another repo
        uses: lbussell/sync-files
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          source_repo: org-name/repo-name
          source_path: configs/.eslintrc.json
          target_path: .eslintrc.json
          target_branch: main

      - name: Sync directory from another repo
        uses: lbussell/sync-files
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          source_repo: org-name/repo-name
          source_path: some-directory/
          target_path: destination-directory/
          target_branch: dev
```

### Outputs

| Output | Description |
|--------|-------------|
| `pr_url` | URL of the created PR |
| `pr_number` | Number of the created PR |
