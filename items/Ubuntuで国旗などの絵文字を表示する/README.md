#絵文字を表示するには
通常の絵文字の表示は`ttf-ancient-fonts`パッケージのインストールで解決します。

しかし、国旗の絵文字などはUbuntuの標準リポジトリには含まれていません。そこでサードパーティリポジトリをインストールする必要があります。

```bash
sudo apt-add-repository ppa:eosrei/fonts
sudo apt-get update
# Choose one!
sudo apt-get install fonts-emojione-svginot
# Or
sudo apt-get install fonts-twemoji-svginot
```

参考: http://askubuntu.com/questions/639552/how-to-get-apple-color-emojis-to-work-on-ubuntu/740649#740649
