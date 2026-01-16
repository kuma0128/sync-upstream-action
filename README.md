# Sync Upstream Action

Fork リポジトリを upstream と同期する GitHub Action です。

## 特徴

- **PR モード（デフォルト）**: Pull Request を作成。コンフリクトがあっても PR は作成され、手動で解決可能
- **Direct モード**: 直接 target ブランチにプッシュ。コンフリクト時は失敗
- 変更がない場合は何もしない

## 使い方

### PR モード（推奨）

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

### Direct モード

```yaml
      - uses: kuma0128/sync-upstream-action@v1
        with:
          upstream_repo: 'awslabs/ssosync'
          upstream_branch: 'master'
          target_branch: 'master'
          mode: 'direct'  # 直接プッシュ
          token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `upstream_repo` | Yes | - | Upstream リポジトリ (例: `awslabs/ssosync`) |
| `upstream_branch` | No | `main` | 同期元のブランチ |
| `target_branch` | No | `main` | 同期先のブランチ |
| `mode` | No | `pr` | `pr`: PR作成、`direct`: 直接プッシュ |
| `pr_title` | No | `chore: Sync with upstream` | PR のタイトル（PR モードのみ） |
| `pr_body` | No | 自動生成 | PR の本文（PR モードのみ） |
| `pr_labels` | No | `upstream-sync` | PR につけるラベル（PR モードのみ） |
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
