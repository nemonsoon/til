# mise で使っていないバージョンを削除する

`mise list` を見ると、同じツールの複数バージョンが溜まっていることがある。

```sh
$ mise list
Tool  Version  Source                      Requested
go    1.25.6
go    1.25.7
go    1.26.0   ~/.config/mise/config.toml  latest
node  24.11.0
node  25.5.0
node  25.6.0
node  25.6.1   ~/.config/mise/mise.toml    latest
pnpm  10.28.2
pnpm  10.29.1
pnpm  10.29.2
pnpm  10.30.0  ~/.config/mise/config.toml  latest
uv    0.9.27
uv    0.10.0
uv    0.10.4   ~/.config/mise/config.toml  latest
yarn  4.12.0
```

`Source` 列が空 = 設定ファイルから参照されていない古いバージョン。これをまとめて消すには `mise prune` を使う。

```sh
# 削除対象の確認
mise ls --prunable

# プレビュー（実際には消さない）
mise prune --dry-run

# 削除実行
mise prune
```

## ポイント

- "未使用" の判定は `~/.local/state/mise/tracked-configs` に記録された設定ファイルを参照しており、そこで最新として要求されていないバージョンが対象になる
- `mise list` で `Requested` 列が空のもの（例: `yarn 4.12.0`）は設定で要求されていない状態なので、残したければ `mise.toml` に明示しておく

## 用途別オプション

```sh
# 特定ツールだけ対象にする
mise prune node go

# ツールのバージョンだけ削除（設定リンクは残す）
mise prune --tools

# 壊れた設定リンクだけ掃除する
mise prune --configs
```
