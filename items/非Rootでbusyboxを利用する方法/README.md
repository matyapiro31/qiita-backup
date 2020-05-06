AndroidではRoot化した後にbusyboxのインストールがほぼ必須です。ですが、busybox自体はroot化せずとも導入が可能です。具体的にはこのbusyboxアプリをインストールし、
https://play.google.com/store/apps/details?id=burrows.apps.busybox
ステップ1をこなしておきます。
Android Terminal Emulatorのシェルはbashではなくmkshなのですが、設定ファイルの.mkshrcは読み込んでくれないので、
いつでもbusyboxが使えるように、Android Terminal Emulatorの設定を変更します。
設定->初期コマンドから初期コマンドを入力します。
`export PATH=$PATH:/data/data/burrows.apps.busybox/app_busybox; export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/data/data/burrows.apps.busybox/app_busybox;clear`
これでいつでもbusyboxが使えます。
**追記**
非rootのAndroidに手前でコンパイルしたものを突っ込もうという勇者は、まずはfakechrootを頑張ってコンパイルすることをおすすめします。
fakechrootを利用すれば、`/`が書き込み可能な位置にあるかのように見せかけられます。もしくはarmelの設定でDebianのクロスコンパイルの方法を参考にして`./configure LDFLAGS="-static"`とでもしてすべてを静的リンクしてください。rootであればDebianのarmelパッケージはそのまま動きます。依存関係は必須ですが。おそらくGCCのマニュアルを読んでクロスコンパイルするのは難易度が高いので、「Debian Cross build」で検索してみましょう。Windowsからはおそらく難しいと思いますので、UbuntuかDebian Unstableをインストールしてみましょう。

更新:Ubuntu/Debianでコマンドをビルドする方法については
[Debianでマルチアーキテクチャビルドを行う](http://qiita.com/matyapiro31/items/763493e390e58b024ea3)
[非RootのAndroidにコマンドをインストールする](http://qiita.com/matyapiro31/items/17d8fec6f32ebf9017a4)
を参考にしてください。
