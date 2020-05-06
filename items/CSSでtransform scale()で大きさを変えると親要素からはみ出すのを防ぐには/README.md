#CSSで画像などの大きさを変える
要素のサイズを変えるにはCSSの`transform`プロパティの`scale()`関数を使います。
これは`width`, `height`, `padding`を等倍で縮小しますが、`margin`, `border`は多分縮小されません。Chrome,Firefoxだと縮小されませんでした。

##親要素からの認識の違い
width,height,paddingは長く/短くなりますがが親要素からは元の幅として認識されるので、はみ出したり余計な空白ができたりします。
そこでこれを修正するにはmarginを利用します。marginはpaddingと違い負の数値が指定できるので小さくする場合にも有効です。
