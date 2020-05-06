#素直な方法
数値の場合はベンダプレフィックスを利用する。
calc()関数を利用する。
例:

```css
padding-top: -moz-calc(4px);
padding-top: -webkit-calc(3px);
padding-top: -ms-calc(2px);
```
ベンダプレフィックス付きでも使えるのは結構最近。

#font:などの場合は?
簡単にできる方法はないです。変数が実装されてきたら
-webkit-var()などでできるようになるはずです。
