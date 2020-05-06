#関数とコマンド
bashではほとんどライブラリのような概念がありませんが、関数は存在します。
多分ライブラリが無いのは個々のコマンドの整合性が取れないことが多いからだと思いますが、ともあれ関数とコマンドは殆ど同じように使えます。

```bash:example_func1.sh
function hello() {
    echo "Hello,World!"
    return 0
}
```

```bash:hello1.sh
#!/bin/bash
echo "Hello,World!"
exit 0
```
この2つはほとんど同じです。

```bash
source example_func1.sh
hello
#Hello,World!
bash ./hello1.sh
#Hello,World!
```

しかし、

```bash:example_func2.sh
function hello() {
    echo "Hello,World!"
    echo "$0"
    return 0
}
```

```bash:hello2.sh
#!/bin/bash
echo "Hello,World!
echo "$0"
exit 0
```

この2つは結果が異なります。
関数の場合は$0は`bash`コマンドの場合は`hello2`と言うコマンド名になります。
関数名は`${FUNCNAME[0]}`で取得出来ます。
他にも戻り値をセットするのに関数は**return**キーワード、コマンドは**exit**コマンドを使います。
#getopts
getoptsは関数では使用できません。

何か違いが他にもあれば教えて下さい。
