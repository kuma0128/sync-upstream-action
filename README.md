# Sync Upstream PR Action

Fork リポジトリを upstream と同期し、Pull Request を作成する GitHub Action です。

## 特徴

- upstream の変更を検出して PR を自動作成
- コンフリクトがあっても PR は作成される（手動で解決可能）
- 変更がない場合は何もしない

## 使い方

```yaml
name: Sync from Upstream

on:
  schedule:
    - cron: '0 0 * * 0'  # 毎週日曜
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
          fetch-depth: 0

      - uses: kuma0128/sync-upstream-action@v1
        with:
          upstream_repo: 'awslabs/ssosync'
          upstream_branch: 'master'
          target_branch: 'master'
          token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `upstream_repo` | Yes | - | Upstream リポジトリ (例: `awslabs/ssosync`) |
| `upstream_branch` | No | `main` | 同期元のブランチ |
| `target_branch` | No | `main` | 同期先のブランチ |
| `pr_title` | No | `chore: Sync with upstream` | PR のタイトル |
| `pr_body` | No | 自動生成 | PR の本文 |
| `pr_labels` | No | `upstream-sync` | PR につけるラベル |
| `token` | Yes | - | GitHub token (`contents: write`, `pull-requests: write` 権限が必要) |

## Outputs

| Name | Description |
|------|-------------|
| `has_changes` | 同期する変更があるか (`true`/`false`) |
| `commits` | upstream から遅れているコミット数 |
| `pr_number` | 作成された PR の番号 |
| `pr_url` | 作成された PR の URL |

## License

MIT
