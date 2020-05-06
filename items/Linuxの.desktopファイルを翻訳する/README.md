#前述
LinuxではUTF-8を標準で使用し、オープンソースが一般的であることから、翻訳ができるアプリケーションが非常に多いです。そのため、ソフトウェアの絶対数が少なくても、日本語で利用できるソフトウェアが多いのでしょう。
 さて、デスクトップエントリファイルを編集すると、言語毎に表示するアプリケーションの名前が変えられます。デスクトップエントリファイルを翻訳する方法はいくつかあるので、それぞれ紹介していきます。
#壱. ファイル自体を編集する場合
freedesktop形式のファイルは一般的に改行形式で
*変数*=*文字列*
という書き方が為されています。ここで、一つ重要なことはそれぞれの変数ごとに対応する文字列の文法が異なるということです。例えばMIMEタイプの指定には変数MimeTypeを使い、直接MIMEタイプ文字列を使って記述しているのに対し、実行コマンドの指定には変数Execを使い、おそらく標準のシェルで実行されるコマンドを直接記述しているため、例えば前者と後者では/(スラッシュ)の意味が変わってしまいます。
これに注意するのはおそらく当たり前のことですが、文法エラーはプログラムのコンパイル時にはチェックされないのでくれぐれも文法の分かる部分だけを訳すようにしてください。
さて、翻訳の方法ですが、
*翻訳したい変数*[*LCID*]=*翻訳した文字列*
と、至って簡単です。日本語の場合、[ja]や[ja_JP]もどちらでも使用できます。
また、この場合は*変数*=*文字列*が存在していなくても問題ありません。
#弐. intltool対応の.in形式のファイルの場合
intltoolに対応しているとき、通常.desktop.inファイルを編集することになります。
この場合は必要な操作は、
1. .desktop.inファイルの編集 - .in形式のファイルはintltoolを用いて翻訳データを収集するし、変換するためのものです。この形式も統一性に欠けているという面があり、変換対象のファイルの種類によって指定の仕方が違います。.desktop形式の場合は
*変数*=*文字列*
を_(アンダーバー)を付けた
*_変数*=*文字列*
に編集します。
2. POTFILES.inファイルの編集 - POTFILES.inファイルに一行.desktop.inファイルののパスを挿入します。
3. *言語コード*.poファイルの編集 - PoEditやGtranslatorなどで翻訳してください。
#參 intltoolに対応させたい場合
## 其之一 AutoToolsを使っている場合
Makefile.amに
desktopdir = *hoge*
desktop_in_files = *hoge*.desktop
desktop_DATA = $(desktop_in_files:.desktop.in=.desktop)
@INTLTOOL_DESKTOP_RULE@
を挿入し、.desktopファイルを.desktop.inに名前を変更する。
.desktopファイルが無いとか、configure.acレベルで対応していない場合?知りません。
## 其之二 CMakeを使っている場合
.cmakeファイルの文法について解説はできないので、とりあえずCMakeでやりたい場合は直接intltoolのコマンドを実行する。
`intltool-merge --desktop-style` *`POTFILES.inファイルの在るディレクトリ`* *`hoge`*.desktop.in *`hoge`*`.desktop`
実行のタイミングやターゲット、インストールの設定などはそれぞれ、
[add_custom_target](http://www.cmake.org/cmake/help/v3.2/command/add_custom_target.html)
[install](http://www.cmake.org/cmake/help/v3.2/command/install.html)
などを参考にしてください。
