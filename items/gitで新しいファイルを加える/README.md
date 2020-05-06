#Gitにはファイルの新規作成&追加がない?
どうにもこれ↘を一発でやるコマンドがなかったようなので、aliasを設定しておこうというわけです。

```bash
$ touch README.md
$ git add README.md
# ないのでaliasしようとしたけど、2つ要るので関数
function git-add-new () {
    touch $1
    git add $1
}
```
これだけだと味気ないので、第二引数に権限を設定する機能を付けましょう。

```bash
function git-add-new2 () {
    touch $1
    chmod ${2:-660} $1
    git add $1
}
```
