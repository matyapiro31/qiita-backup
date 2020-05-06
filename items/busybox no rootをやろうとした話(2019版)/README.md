#Busyboxとは
Busyboxとは、いくつかのよく使うコマンドを一つのコマンドにまとめた便利つーる(スイスアーミーナイフ)です。これをAndroid Terminal Emulatorに久しぶりに入れてみようとした話です。
#Androidのセキュリティ
AndroidはLinuxと違い、一般ストレージにおいたファイルに実行権限をつけたりすることはできません。（スクリプトなどは動かせますが。）
そのため、elfなファイルを動かしたい場合、いくつか制約があります。

1. root権限や1000以下のポートを占拠することができない
2. いくつかのディレクトリが読み取り専用
3. フォルダ構造その他Androidはカスタムされているのでうまく動かない

そのため、うまくコマンドを実行させるにはビルドから行って、さらに設定なども細かく変える必要があります。
Busyboxはそこまでは必要ありません。すでにバイナリが用意されているからです。
https://forum.xda-developers.com/showthread.php?t=1929852
ここからAndroid armv7l用のバイナリを入手できます。
これをAndroid Terminal Emulatorのデータ領域にコピーします。

```bash
mkdir /data/user/0/jackpal.androidterm/files
cp /storage/emulated/0/Downloads/busybox /data/user/0/jackpal.androidterm/files
chmod +x /data/user/0/jackpal.androidterm/files/busybox
```
他にないと思いますが、`uname -a`コマンドでデバイスのアーキテクチャを調べられます。
最後に環境変数PATHにこれを追加して、最初に実行するコマンドに入れれば終わりです。
`export PATH=$PATH:/data/user/0/jackpal.androidterm/files;clear`
しかし、何かおかしいことにbusybox自体は成功するものの、busybox内の他のコマンドは呼び出そうとすると、　`Bad system call`と言われるので、うまくいきませんでした。
