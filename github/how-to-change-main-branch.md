# デフォルトブランチを修正する方法

## なにがしたいか？

- 間違えて別ブランチが本流になってしまった
- 本流を`main`ブランチに戻したい
- ローカルCLIで実現したい。

## 手順

以下コマンドで、デフォルトブランチ（本流）をmainに変更できる。

`gh repo edit --default-branch main`

以下コマンドで、origin側の本流もmainに直す。’

`git remote set-head origin -a`
'origin/HEAD' has changed from 'feature/add-packages' and now points to 'main'

以下コマンドで、確認。

`git branch -a -vv`

```sh

feature/add-packages 6afb560 [origin/feature/add-packages>

- main 6afb560 [origin/main] Initialize pro>
  remotes/origin/HEAD -> origin/main
  remotes/origin/feature/add-packages 6afb560 Initialize project with pack>
  remotes/origin/main 6afb560 Initialize project with pack>
  lines 1-5/5 (END)
```
