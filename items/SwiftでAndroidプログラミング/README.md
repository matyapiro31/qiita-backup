#NDKを使えばSwiftから書ける!
android ndkはそれぞれのアーキテクチャ別のLinux共有ライブラリを利用しているだけなので、別にAndroid.mkを書く必要はありません。やるべきことは、ARMなどのCPUに合わせる、PICをつける、android_main関数をつけるというだけです。
さて、Apple Swiftにも勿論C言語から使える.soを作れます。
`@_silgen_name`を使えばC言語の関数として宣言することが出来ます。
nmコマンドによるシンボルとSwiftのメソッド宣言?の対応はこちらです。
(Swift知りません。)
<table><tr><th>シンボル</th><th>Swift</th></tr><tr><td>t (ローカルテキストシンボル)</td><td>internal func</td></tr><tr><td>T (グローバルテキストシンボル)</td><td>func</td></tr><tr><td>B/b (グローバル/ローカルbssシンボル)</td><td>おそらく外部クラスの参照</td></tr><tr><td>U (外部ライブラリの参照)</td><td>外部の共有ライブラリで実装されたCの関数を参照</td></tr></table>
しかし、現在まだSwiftでこういったことをするプロジェクトはありません。なので、Swiftで書こうと思えば、C言語の内容の実装をひたすらやらないといけないので、ちょっと大変かと思われます。
