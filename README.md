# sync-files Action

GitHub Action that synchronizes a file or directory from another git repository to the current repository via Pull Request.

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
      - name: Checkout target repository
        uses: actions/checkout@v6
        with:
          fetch-depth: 0

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

      # Syncing from a non-GitHub repository
      - name: Sync from GitLab
        uses: lbussell/sync-files@main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          source_repo: https://gitlab.com/<whatever>
          source_path: shared/config.json
          target_path: config.json
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `github_token` | Yes | - | GitHub token with write access on target repo |
| `source_repo` | Yes | - | Source repository: `owner/repo` for GitHub, or full URL for any git host |
| `source_path` | Yes | - | Path to file or directory in source repository |
| `source_branch` | No | default branch | Branch to sync from in the source repository |
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

**Important**: The caller must checkout the target repository before running this action. For private source repositories, configure git credentials in your workflow (the action inherits the git credential configuration from your checkout step).

## Behavior

- Creates a new branch with name `sync-files/<path>-<hash>` based on the source path and content
- Creates a PR or updates an existing one if the branch already exists
- Skips PR creation if there are no changes (outputs will be empty)
- For directories, syncs all contents including deletions (files removed from source are removed from target)
