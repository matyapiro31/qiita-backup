#途中まで背景を塗りたい
背景の色を決める時には`background-color`を使うことが多いですが、要素の一部分のみに色を塗りたい時はありませんか?
まあ`box-shadow`を使えば全て解決なんですが、別の解決方法を考えました。

##`background-image`と`linear-gradient`を使う
という解決方法です。ちなみにTwitterの投票の棒グラフでは2つの要素を重ねあわせて無理やり実現しています。
PureCSSで解決できないなんてCSS使いの風上にもおけない。
`linear-gradient`は複数の色を合わせてグラデーションを作り出す要素ですが、画像扱いなので`background-color`ではなく、`background-image`に指定します。`background-image`は縦横のサイズが決まっています。そのため、`background-position`を使えば部分的に色を塗ることができます。
`background`要素は複数の背景を設定できるので、これを使えば理論上ドット絵を描くことも可能なはずです。

例： 幅500pxの要素に対して2:3に塗り分ける方法。

```css
background: linear-gradient(#1f2,#1f2) -300px center no-repeat,
            linear-gradient(#ada,#ada) right center no-repeat;
```

jsfiddleにサンプルを置いています。
https://jsfiddle.net/matyapiro31/ts53cfcq/4/
