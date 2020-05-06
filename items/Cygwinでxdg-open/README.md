xdg-openコマンドはLinuxの既定ののシェルでディレクトリを開くコマンドである｡
Cygwinになかったので作った｡(x64)
Gistに投稿したので使ってみてほしい｡
https://gist.github.com/matyapiro31/3cfc14b7e6b0b392afcb
プロセスを分離するには最後に &をつけよう。

他にもCygwin用のツールを作ってみた。
xcd ---cygpathでWindowsのパスを作るだけでなく、cdも出来るようにした。当然使用の便宜上シェル関数になるのでsourceコマンドが必要。
.bashrcなどに書くことで使える。
https://gist.github.com/matyapiro31/93ed729d5d546c352918
