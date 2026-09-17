# Prettier は .gitignore も無視リストとして読む

`.gitignore` から数行消したら、それまで通っていた `pnpm format` が急に落ちるようになった。

```sh
$ pnpm format
> prettier . --check

Checking formatting...
[warn] .claude/commands/api.md
[warn] .claude/commands/check.md
[warn] CLAUDE.md
[warn] Code style issues found in 8 files.
```

消した行に書いてあったファイルが、そのまま整形対象として現れている。

## なぜか

Prettier 3 は `--ignore-path` の既定値として `.gitignore` と `.prettierignore` の **両方** を読む。

```sh
$ prettier --help
  --ignore-path <path>     Path to a file with patterns describing files to ignore.
                           Multiple values are accepted.
                           Defaults to [.gitignore, .prettierignore].
```

つまり `.gitignore` に書いたファイルは、意図していなくても整形の対象から外れている。手元だけに置きたいファイル（エディタやツールの設定、下書きのメモなど）を `.gitignore` に並べていると、Prettier がそれを避けてくれるのは **副作用であって設定ではない**。

だから `.gitignore` の記載をやめた瞬間に、整形の対象として湧いて出る。Git の無視設定と整形の無視設定が、同じ1枚のファイルに相乗りしていたということ。

## 対処: 追跡しているファイルだけを対象にする

無視パターンを書き足すのではなく、そもそも Git が追跡しているファイルだけを渡す。

```json
{
  "scripts": {
    "format": "git ls-files -z | xargs -0 prettier --check --ignore-unknown",
    "format:fix": "git ls-files -z | xargs -0 prettier --write --ignore-unknown"
  }
}
```

`-z` と `-0` の組み合わせは、ファイル名に空白が入っていても壊れないようにするため。

### --ignore-unknown が要る理由

追跡ファイルには、Prettier がパーサを持たないものも混ざっている。付けないとそこで落ちる。

```sh
$ git ls-files -z | xargs -0 prettier --check
[error] No parser could be inferred for file ".husky/pre-commit".
[error] No parser could be inferred for file "app/favicon.ico".
[error] No parser could be inferred for file "public/sample.png".
Error occurred when checking code style in 7 files.
```

`--ignore-unknown`（短縮形 `-u`）を付けると、この手のファイルを黙って飛ばしてくれる。

## ポイント

- `.prettierignore` は引き続き効く。明示的にパスを渡しても適用されるので、`pnpm-lock.yaml` のような除外はそのまま生きる
- 追跡前の新しいファイルは対象外になる。コミット時に `lint-staged` を通しているなら、そこで拾えるので実害は出にくい
- 手元だけの無視パターンには、公開されない置き場が2つある

| 置き場 | 効く範囲 |
| --- | --- |
| `~/.config/git/ignore` | そのマシンの全リポジトリ |
| `.git/info/exclude` | そのクローンだけ |

リポジトリの `.gitignore` に書くと、無視しているファイル名が公開物に残る。「何を手元に置いているか」を見せたくないなら、この2つに寄せるほうがよい。

## 環境

Prettier 3.9.6 で確認。
