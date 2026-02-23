## GitHub のリポジトリ名を変更

`gh repo rename sample-api --yes`

- 実行場所: 既存リポジトリのローカルディレクトリ内
- これで GitHub 側の username/sample-app が username/sample-api に変わる。

## ローカルのディレクトリ名を変更

`mv /Users/username/sample-app /Users/username/sample-api`

## リモート設定を確認

`cd /Users/username/sample-api`
`git remote -v`

## もし origin が古いままなら手動更新

`git remote set-url origin git@github.com:username/sample-api.git`

## 最終確認

`git status -sb`
`git ls-remote --get-url origin`
