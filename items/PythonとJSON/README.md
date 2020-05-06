最近JSONを始めまして、Qiitaに投稿していらっしゃる諸兄には及びませんが、JSONについていろいろ書いてみます。

#JSONはJavaScriptの一変数である
JSONはJavaScriptの変数のデータ構造をそのままファイルに書いただけです。
そのためJSONにはコメントなどの構文がありませんが、JavaScriptで簡単に読み込めます。

#JSONはPythonと文法がほとんど一致している
まずはディクショナリ型を見てみましょう。Pythonは
` #Pythonの構文
a = {"foo":'bar',}
`
次にJavaScript/JSON
`{"hello":"world"}`
最後にカンマを付けられるのが、PythonですがJSONではそれができません。またJSONはシングルクォーテーションでは囲めません。
次にリスト型を見てみましょう。Pythonは
`a=[2,3,4,"abc",3.14e+2,]`
次にJSONでは
`[1,2,"def",2.71828182846e+5]`
同じ表現でできます。
エスケープ文字について見てみましょう。
Pythonの方が多いですが、JSONにあるものはそのまま通用します。大体エスケープシーケンスが全く違うのはVBかXMLぐらいです。Cの流れを汲んでいる言語はほぼ同じです。
次に変数の型についてです。
整数型や浮動小数点型は同じようにサポートされていますが、真理値型だけは違います。
JSONではtrue/falseと小文字ですが、PythonではTrue,Falseと大文字です。
不定値についてはJSONはnullですが、PythonはNoneです。

#まとめ
true,false,nullを使わなければJSONはそのままPythonのデータとして通用します。先頭行に
`#!/usr/bin/python
``#coding:utf-8
true = True;false = False;null = None;
hoge = \`
などと加えれば立派なPythonです。
