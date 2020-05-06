#Tips一覧
運営に有用かもしれない情報を箇条書していきます。

* .env.productionの設定をいじればDockerからRailsのみ取り出して運用することもできる。Docker内で動かしているPostgreSQLに接続することもできるし、その逆も可能。
* Nginxで`proxy_set_header Accept-Encoding "";`と設定しなければRails側で圧縮してくるので、Brotli圧縮はできない。
* 固定IPが利用できないサーバではDDClientを用いて動的にDNS設定を変更すると良い。
* マストドンはWebSocketを用いる。websocketを通さないよう設定できるCDN(Cloudflareとか)があるので、注意が必要。
* 連合タイムラインには最初に接続できないとその不具合を直してもうまくつながらない。
* Windowsでプログラミングしてコードを変えるときは改行に注意。
* URL短縮を自前で用意すると良いかもしれない。URLは長いと書けないし、既存の短縮は信頼されていない。ただし、コードを公開すること。
* くれぐれもセキュリティには注意すること。直接サーバのIPアドレスを晒してはならない。何らかの手段で隠蔽すること。
* フルHTTPSにできないのであればおそらく立ち上げないほうが良い。
* Webの最初のテーマは暗いのでいじったほうが良い。
* ページと対応するHamlファイル。app/views/以下にある。

| ページ | Haml |
|:----|:---|
|/about/more|/about/more.html.haml|
|/about|/about/show.html.haml|
|/auth/edit|/auth/registrations/edit.html.haml|
|/web/getting-started|/home/index.html.haml|
|/oauth/authorized_applications|/oauth/authorized_applications/index.html.haml|
|/settings/export|/settings/exports/show.html.haml|
|/settings/follower_domains|/settings/follower_domains/show.html.haml|
|/settings/import|/settings/imports/show.html.haml|
|/settings/preferences|/settings/preferences/show.html.haml|
|/settings/profile|/settings/profiles/show.html.haml|
|/settings/two_factor_authentication|/settings/two_factor_authentications/show.html.haml|

* ローカルでページを閲覧するときはcurlではなくwgetを使う。`wget -qO- --no-check-certificate http://127.0.0.1:3000`でローカルのトップページを出力できる。これの何が良いかというと特定のページの複雑な変更が可能になる。Rubyのコードを見ずともAPIを書き換えてしまえる。
* マストドンの`mastodon-web`サービスは再起動にある程度時間がかかるが､`kill -SIGUSR2 (pumaのPID)`すると接続したまま変更を読み込める｡ 
* mastodon-webのところに`ExecReload=/bin/kill $MAINPID`と書き込めば`systemctl reload mastodon-web`が使えるようになる｡
