#rootアプリとは
rootアプリはルート権限が必要な操作をするアプリです｡
当然通常の端末でroot化されているものは少ないので､root向けのAPIなどはありません｡(たぶん)
そのため､Javaの`java.lang.Runtime.exec()`メソッドなどを使います｡
通常のWindows/Unix向けJavaアプリケーションと同様に使えます｡Sunの実装ではないので､微妙に違う仕様の部分もあるかもしれませんが｡
ごく普通にsuコマンドを実行するだけでroot権限の要求ポップアップが出てきて実行できます｡
#実行するコマンド
`su`はrootに変身するコマンドですが､シェルではないので､`su -c command`でコマンドを実行することができます｡
root権限が要らないときは`su -c`は不要です｡rootの取得に失敗した時は多分1が返ってきますので､それで処理ができると思います｡
`java.lang.Runtime.exec()`メソッドは環境変数の指定もできるので､環境変数**PATH**を*/data/data/package_name/files/bin/*に通しておけば､そこにインストールされたLinuxバイナリを実行できます｡Androidのプロジェクトの**res**フォルダに**raw**タイプのディレクトリを追加してください。その下に**bin**ディレクトリを作成してバイナリをインストールしましょう。
多分大概Cで書いてバイナリをそこに置いておくほうが早いです｡私はJava苦手というかほとんどやったことないので。
完全に静的リンクしたarmel向けのバイナリを作成することで､Android向けにLinuxバイナリをコンパイルすることができます｡(静的リンクしなくても問題ないかもしれません。)
一部のプロジェクトなどでは使っているautotoolsやcmakeなどの設定によって､難しい場合があります｡


参考サイト:<a href="http://blog.livedoor.jp/shizuku_kun/archives/51596903.html">AndroidアプリケーションでLinuxコマンドを実行する！</a>(しずくくんのAndroidでゲームプログラミングしてみたいなblog)

#AndroidManifest.xmlに必要な権限
権限は特に必要ではないですが、一部のスーパーユーザーアプリは`android.permission.ACCESS_SUPERUSER`を要求してくる場合があります。
また、権限に拘らずすべてのファイルアクセスが可能になります。そこに十分に注意しましょう。何を行ったのかははっきりと記録に残るので、デバッグに役立てましょう。

#suのバージョン・実装による違い
suはいくつかのバリエーションがあります。以下のコマンドを例にしてみましょう。

```bash
$su -c echo a b
#1. ユーザ 'a' のパスワードエントリがありません
#2. a b
```
1のエラーが返ってくる場合、このようにしなければなりません。

```bash
$su -c 'echo a b'
```
1でも2でもシングルクォートで囲めば同じ結果が返ってきます。

また、オプションの有無の違いがある場合があります。
Debianのsuは-u,-v,--version,-Vが存在しません。
