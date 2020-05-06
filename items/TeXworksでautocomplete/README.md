#TeXworksでIntellisenseみたいなことがしたい
TeXworksってどうも旧いソフトなので入力補完とかないのかな、と思ったらありました。
入力データ用のファイルを用意する必要があります。
#入力データファイルの形式と文法
入力データファイルの書き方について説明します。
1. 文字コードはUTF-8(BOMなし)
2. 改行はLF(他の改行でも行けるかどうかは不明)
3. ファイル名は\*.txt
4. 最初の行に<b>`%%!TEX encoding = UTF-8 Unicode`</b>という文言を入れる。shebangの一種?
という形式で書いてください。
#ファイルの保存場所
ここが問題です。
Ubuntuだと`~/.TeXworks/completion/`以下になりますが、このディレクトリがどこになっているのかは明確にTeXworksのドキュメントには述べられていません。(`<resources folder>/completion`という書き方をされています。)
このフォルダを開くにはTeXworksのメニューから「スクリプト」→「TeXworksスクリプト」→「スクリプトフォルダを開く」で`<resouces folder>/scripts`を開いて一つ上のフォルダを参照し、
> **completion  configuration  scripts  translations**

とある中の**`completion`**を選んでください。

#ファイルの書き方
まず、特殊文字について説明します。
1. \#RET\# - 改行を挿入します。
2. \#INS\# - 入力位置をここに設定します。ない場合、最後方になります。
3. • - bullet。日本語キーボードの/のところの文字(中黒)とは違います。Qiitaだとリストのところにあるやつです。Ctrl+Tabで移動できる栞みたいな機能を提供します。印刷されません。印刷したいときは数式モードを使ってください。
区切り文字は改行(LF)です。

##1. 置換型
書かれた文字列を置換します。

```tex
bequ
```
と入力し、TABキーを入力すると、`bequ`が

```tex
\begin{equation}

\end{equation}•
```
に置き換わります。
構文は

```tex
xph:=\phi
bfrm:=\begin{frame}#RET##INS##RET#\end{frame}
```
このように`キーワード:=置換される内容`という構文です。
##2. 挿入型
内容を追記します。

```tex
\column
```
と入力すると、TABキーの入力で

```tex
\column{}
```
と{}が追加されます。さらに入力位置も{}の中になります。
構文は

```tex
\column{#INS#}
\column[•]{#INS#}
{column}{#INS#}#RET##RET#\end{column}
{column}[•]{#INS#}#RET##RET#\end{column}
{columns}#RET##INS##RET#\end{columns}
{columns}[•]#RET##INS##RET#\end{columns}
```
のようになります。
#入力の仕方
3文字以上を入力してTABキーを入力すると当て嵌まる候補が出ます。TABキーで次の候補に切り替えられます。
