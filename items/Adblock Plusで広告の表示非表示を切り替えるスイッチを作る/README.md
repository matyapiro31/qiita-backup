#Adblock PlusのCSS
Adblock PlusではCSSで指定した要素､擬似要素に対して非表示にさせる仕組みが備わっています｡
例えばこのような書式です
`example.com##div h1`
#CSS3の:target擬似クラス
:targetはあまり知られていない擬似クラスです｡CSS3で追加されました｡
これはわかりやすく言えば`id`属性を持つ要素に対して内部リンクされている時のスタイルを指定するものです｡
例えばこんなユーザースタイルシートを用意したとします｡そしてStylishなどの拡張機能や開発者モードなどを使って適用してください｡

```css
/* Qiitaで表示が切り替わるユーザースタイルシート */
#main:target {
    transform: scale(1.5,1.5);
    transform-origin: top center;
}
```
これでこの[リンク](#main)を踏むと､メイン部分が拡大されます｡

#これらを組み合わせる
`qiita.com###main:target + .footer div`
このようなフィルターを作るとリンクを踏んだ時だけQiitaのフッターが非表示になります｡
