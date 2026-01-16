# Sync Upstream PR

A GitHub Action to sync your fork with an upstream repository.

## Features

- **PR mode (default)**: Creates a Pull Request. Even if conflicts occur, the PR is still created for manual resolution
- **Direct mode**: Pushes directly to the target branch. Fails if conflicts occur
- Does nothing if already up to date

## Usage

### PR Mode (Recommended)

```yaml
name: Sync from Upstream

on:
  schedule:
    - cron: '0 0 * * 0'  # Every Sunday
  workflow_dispatch:

jobs:
  sync:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required: full git history is needed to compare with upstream

      - uses: kuma0128/sync-upstream-action@v1
        with:
          upstream_repo: 'awslabs/ssosync'
          upstream_branch: 'master'
          target_branch: 'master'
          token: ${{ secrets.GITHUB_TOKEN }}
```

### Direct Mode

```yaml
      - uses: kuma0128/sync-upstream-action@v1
        with:
          upstream_repo: 'awslabs/ssosync'
          upstream_branch: 'master'
          target_branch: 'master'
          mode: 'direct'  # Push directly
          token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `upstream_repo` | Yes | - | Upstream repository (e.g., `awslabs/ssosync`) |
| `upstream_branch` | No | `main` | Branch to sync from |
| `target_branch` | No | `main` | Branch to sync to |
| `mode` | No | `pr` | `pr`: Create PR, `direct`: Push directly |
| `pr_title` | No | `chore: Sync with upstream` | PR title (PR mode only) |
| `pr_body` | No | Auto-generated | PR body (PR mode only) |
| `pr_labels` | No | `upstream-sync` | Labels for the PR (PR mode only) |
| `token` | Yes | - | GitHub token (requires `contents: write`, `pull-requests: write`) |

## Outputs

| Name | Description |
|------|-------------|
| `has_changes` | Whether there are changes to sync (`true`/`false`) |
| `commits` | Number of commits behind upstream |
| `pr_number` | Created PR number (if any) |
| `pr_url` | Created PR URL (if any) |

## License

MIT
