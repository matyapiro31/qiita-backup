#あるいはtelnet
netcatはtcp/ipで通信できるコマンドです。(他にも色々出来ますが。)
これを使ってwgetみたいなことをやってみます。
HTTP 1.1ではHost:の記述が必須だそうです。ちなみにtelnetでもnetcatと同じようなことができます。

```bash
netcat example.com 80 <<!
>GET / HTTP/1.1
>Host: example.com
>
>!
```
<h2>解説</h2>
HTTPはポート80です。
`>GET / HTTP/1.1` <b>http://example.com/</b>をダウンロードします。

```bash
>
>!
```
空改行を挟む必要があります。ちなみに!はHTTPではなくbashの方の終了記号です。
パイプを使う場合、echoより、printfのほうが便利です。また、改行は\nではなく\r\nです。

#学び方
`curl url --trace FILE`
でファイル*FILE*にcurlのHTTP接続情報が記録されます。これでHTTPリクエストやレスポンスを覚えたら簡易サーバをたてられます。

#HTTPSに対応する
`openssl s_client`コマンドで同じことができます。こちらはもっとオプションなどが複雑です。httpsでの通信ができるので、そういった安全性が確保する必要のあるネットサービスサービスに対する簡易クライアントになります。
httpsではSSL/TLSのバージョンを指定することができます。
HTTP 1.1/TLS 1.2を強制することもできますし、HTTPと同じ感じでできます。
