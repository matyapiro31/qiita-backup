#どうして囲む必要があるのか
私も今までその必要はないと思っていました。実際多くの場合は問題ありません。
しかし、悪意のあるURLを扱う可能性があれば必ず必要になります。
例えばこんなのとか↓""で囲まずにうっかりroot権限でサーバの端末で打っちゃうと大変なことになります。

```bash
#悪意しかないURL(入力するな(再起動します))
wget https://example.com/?a=1&reboot
``` 
何故かというとbashでは`&`を"や'で囲まずに書くとそこでコマンドの引数が終了したとみなし、バックグラウンド実行させ、&以降を次のコマンドと見なすからです。他にも`;`や`||`とかあります。
割とこういうことは常識ですが、Qiitaではやっていない方が多かったというか、自分も今まで気が付かなかったので書くべきかな〜と思った次第です。
幸いコマンド引数を取らせる方法は思いつかなかったので、多分引数を取る必要のあるコマンドは無理ですが、それでも大変なことになると思います。

#対処方法
wgetやcurlコマンドを使う時、というかURLを引数に取る時は常に''で囲みましょう。""より''の方が安全です。まあコピペコード入力する時以外にはこんなコード打たないと思うので大丈夫だとは思いますけど。
