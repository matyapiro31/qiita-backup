# 環境変数**PATH**の意味
環境変数である**PATH**はLinuxにおいてコマンドの名前を解決するために使用される環境変数です。ディレクトリのパスが`:`で区切られている文字列が正体です。これらのディレクトリの中にあるファイル名が参照されます。先にかかれているパスが優先されます。

# ARMプロセッサ向けのコンパイラを用意する
ARM向けのコンパイラはAndroid NDKについてくる、**gcc-arm-linux-androideabi**を使うのがいいでしょう。ただしC99のサポートが無いので、C99が必要な人は別のコンパイラを使ってください。
![Android](https://developer.android.com/assets/images/android_logo_ndk.png) NDK Downloads | Android Developershttps://developer.android.com/ndk/downloads/index.html
環境変数PATHに最前位置にgccのあるパスを追加しましょう。gccはarm-linux-androideabi-gccのシンボリックリンクになっています。

コンパイルの際の設定も必要です。
# autotoolsを使っている場合
AutoToolsでプラットフォームを設定することでMakefileをAndroid向けにできます。
`$ ./configure --host=arm-linux-androideabi`
Androidでroot化しない限り`/lib`などの場所に共有ライブラリをインストールできなくなるので、すべて静的リンクしてコンパイルします。
`$ make LDFLAGS=--static`
fileコマンドで静的にビルドされているかどうか確かめましょう。
`$ file `*`hoge`*`
`*`hoge:`*`ELF 32-bit LSB  executable, ARM, EABI5 version 1 (SYSV), statically linked, for GNU/Linux 2.6.16, not stripped
`
ここで重要なのは 「**ELF 32-bit LSB  executable, ARM**」と、 「statically linked」です。この2つが揃っていなければAndroidでは動きません。
#gccで直接コンパイルする場合
PATHを設定した後で
`gcc --static `*`hoge.c`*

#CMakeを利用する場合
静的コンパイルが必要なので、
SET(CMAKE_EXE_LINKER_FLAGS "-static")
などを.cmakeファイルなどに入れる。

#chmodで権限を変更できない!?
既にBusyboxをインストールされている方は、ビルドしたアプリをchmodで実行可能にできないことに気が付くでしょう。何故かは分かりませんが、chmodできるディレクトリとそうでないディレクトリがあるようです。もっともこれは私のLinuxの権限についての知識が不足しているせいです。実行できるディレクトリとは、/data/data以下のBusyboxアプリ用のデータディレクトリです。ここにインストールすればファイルを実行できるようですね。私はbusyboxのディレクトリにすべてのファイルを入れています。
追記:どうもシステムメモリとユーザーメモリというメモリ領域の区分が存在し、システム領域にインストールにしなければ実行権限は付けられないみたいです。
あとどうやらarmel向けにビルドすればいいだけのようです。

関連:[非Rootでbusyboxを利用する方法](http://qiita.com/matyapiro31/items/00caf525ee65f070647f)
