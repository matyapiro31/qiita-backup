#やりたかったこと
中古で300円で買えた低解像度カメラを監視カメラにして、Webで中継可能に。

#準備
ラズベリーパイ2を用いました。OSはRaspbianではなくUbuntuです。理由は2つです。

1. Raspbianはarmelなので古い命令セットのみなのでラズベリーパイ2の性能を出しきれない
2. Ubuntuのほうがドライバやパッケージが豊富にある

UVCカメラのドライバモジュールがカーネルに組み込まれていれば動作します。
それぞれの製品と対応するドライバは<a href="https://wiki.archlinuxjp.org/index.php/%E3%82%A6%E3%82%A7%E3%83%96%E3%82%AB%E3%83%A1%E3%83%A9%E8%A8%AD%E5%AE%9A">Arch Wiki</a>などで確認できます。
おそらくほとんど何も設定なしで動作するはずです。
今回は`motion`を使います。このプログラムは画面の変化を検知して自動的に保存します。
参考にしたサイトは<a>http://divide-et-impera.org/archives/1098</a>と<a>https://www.raspberrypi.org/forums/viewtopic.php?t=18314</a>
それと、motionのホームページのhttp://www.lavrsen.dk/foswiki/bin/view/Motion/WebHome です。
安全な通信を確保することが大前提なのでファイアウォールとSSLも設定します。
ファイアウォールは`ufw`を使い、SSLはCloudFlareかLet's Encryptを使えます。
サーバーはCGIが簡単なApacheです。
Motionを直接SSLにする方法はいまいち見つかりませんでした。

#インストール
motion,apache,curlをインストールします。

```bash
$ sudo apt-get install motion apache2-bin motion curl
```

#Apacheの設定
CGIを有効にしてください。なぜGUIとかで一発設定できないのか...

```bash
#Apacheを初期状態から一発でCGIを有効にするコマンド
 sudo sed -e 's/#AddHandler cgi-script/AddHandler cgi-script/g' -i /etc/apache2/mods-enabled/mime.conf
echo "<Directory "/var/www/html">
    AllowOverride All
    Options +ExecCGI
    Require all granted
</Directory>" |sudo tee -a /etc/apache2/sites-enabled/000-default.conf >/dev/null
a2enmod cgi 
sudo service apache2 restart
```
設定するCGIはこんな感じで。Motionの映像は8081番のポートに出力されますので、それを80/443番に飛ばします。これによってApacheのSSLを用いることができます。

```bash:motion.cgi
#!/bin/bash
printf "Content-Type: multipart/x-mixed-replace; \n\n"
curl http://127.0.1.1:8081

```

HTMLで用いるには`img`要素を利用します。motion.cgiはhttps://example.com/motion.cgi にポイントされているとします。

```html
<img src="https://example.com/motion.cgi">
```

#ファイアウォールの設定
`ufw`を有効にしましょう。基本的に80や443以外は開けなくても問題ないです。ただ8081はMotion用なので開けておきましょう。あとは22などのssh用のポートも開けておかないとsshできなくなります。
`sudo ufw enable
 sudo ufw default reject
 sudo ufw allow 80
 sudo ufw allow 443
 sudo ufw allow 22
 sudo ufw allow 8081`

#カメラの設定
## カメラの動作の確認
カメラが動作しているかどうかは/dev/video0が存在しているかどうかでわかります。
ホストがGUIのあるLinuxで、sshで接続できるのなら、sftpでファイルシステムをマウントして調べるのもいい手だと思います。
さらに、通常起動しているWebカメラは必ずLEDが光っているはずです。

## Motionの設定
daemonとして起動できるようにするには**/etc/default/motion**の変更が必要です。
noをyesに変えるだけです。
その他、重要な設定があります。**/etc/motion/motion.conf**を変更しましょう。
11行目はoffからonに。
複数のカメラがある場合、29行目でカメラを選択できます。
282行目で動画としてエンコードする形式を変えられます。ただし、flvとswf以外はどうもうまく行きません。
400行目でポートを変えられます。複数のカメラの内容を同時に監視したい場合、立ち上げるdaemonごとにポートを変更する必要があります。
403行目は画質です。特に落とす必要は無いと思いますので、100にしておきましょう。
最後にdaemonを立ち上げます
`sudo service motion start`

この状態でWebサイトで上の要素を含んだページを作ると、Gifのようなリアルタイムの映像が表示されます。

#注釈
1: **❝motion は v4l2 デバイスしか処理できないため v4l1 ドライバーしかないカメラを使う時は前述の通りに v4l1compat.so をプリロードする必要があります。そうしないと motion が適切なパレットを見つけられないというエラーが起こります。❞**
2: **❝いくつものカメラを同時に扱う場合、Motionで使うポート、カメラデバイスのファイル名を変え、非daemonモードで`-c`オプションを付け、それぞれの設定を反映させたサービスを起動する必要があります。さらにApacheの側のCGIも同じだけ用意するか、引数処理などが必要です。❞**
3: **❝Content-Type: multipart/x-mixed-replaceはInternet Explorer及びMS Edgeではサポートされません。サーバ側で処理してCanvasなどを使う必要があります。❞**
