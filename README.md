# Simple File Sync

Automatically sync files into your repo with a pull request.

## Usage

```yaml
- name: Sync file from another repo
  uses: lbussell/sync-files@v1.0.0
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    # owner/repo for GitHub, or full URL for other hosts.
    source_repo: owner/repo
    # Path to file/directory you want to be copied.
    source_path: path/in/source/repo
    # (optional) Where the file/directory will end up in your repo.
    # Defaults to the source_path if not provided.
    target_path: path/in/target/repo
    # (optional) Branch to copy the source file/directory from.
    source_branch: dev
    # (optional) A pull request will be submitted to this branch on your
    # repository. Defaults to 'main'
    target_branch: main
```

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
- Skips PR creation if there are no changes
- For directories, syncs all contents including deletions (files removed from source are removed from target)

## Full Example Usage

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
          # owner/repo for GitHub, or full URL for other hosts.
          source_repo: owner/repo
          # Path to file/directory you want to be copied.
          source_path: path/in/source/repo
          # (optional) Where the file/directory will end up in your repo.
          # Defaults to the source_path if not provided.
          target_path: path/in/target/repo
          # (optional) Branch to copy the source file/directory from.
          source_branch: dev
          # (optional) A pull request will be submitted to this branch on your
          # repository. Defaults to 'main'
          target_branch: main
```
