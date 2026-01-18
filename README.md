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
    permissions:
      contents: write
      pull-requests: write
    steps:

      - name: Sync file from another repo
        uses: lbussell/sync-files@main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          source_repo: org-name/repo-name
          source_path: configs/.eslintrc.json
          target_path: .eslintrc.json

      - name: Sync directory from another repo
        uses: lbussell/sync-files@main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          source_repo: org-name/repo-name
          source_path: some-directory/
          target_path: destination-directory/
          target_branch: dev
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `github_token` | Yes | - | GitHub token with read access on source repo and write access on target |
| `source_repo` | Yes | - | Source repository in format `owner/repo` |
| `source_path` | Yes | - | Path to file or directory in source repository |
| `target_path` | No | `source_path` | Destination path in current repository |
| `target_branch` | No | `main` | Target branch for the PR |

## Outputs

| Output | Description |
|--------|-------------|
| `pr_url` | URL of the created PR (empty if no changes) |
| `pr_number` | Number of the created PR (empty if no changes) |

## Required Permissions

```yaml
permissions:
  contents: write       # Create branches and push commits
  pull-requests: write  # Create and update PRs
```

If the source repository is private, the `github_token` must also have read access to that repository.

## Behavior

- Creates a new branch with name `sync-files/<path>-<hash>` based on the source path and content
- Creates a PR or updates an existing one if the branch already exists
- Skips PR creation if there are no changes (outputs will be empty)
- For directories, syncs all contents including deletions (files removed from source are removed from target)
