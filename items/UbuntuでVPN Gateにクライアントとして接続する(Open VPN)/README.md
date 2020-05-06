Ubuntuでは既定のネットワーク接続としてハードウェアネットワーク、仮想ネットワークなどが扱えますが、
VPN(Virtual Private Network)の中で対応しているのは、Microsoftと互換性の在る[PPTP](https://technet.microsoft.com/ja-jp/library/gg983536.aspx)サーバだけです。
PPTPでの接続は私の思い違いでなければ[VPN Gate](http://www.vpngate.net/)は未対応なので、ここではUbuntuで追加導入できる4つのVPNについて紹介します。まずは**SSL-VPN**(SoftEther VPN)です。今回うまくいったのは結局OpenVPNだけなので、成功したときに加筆しますが、専用クライアント必須であるため、コマンドラインスキルがあるか、別途Windows版のGUIクライアントとSSH、もしくはLAN内では安全性が確保できるのであればTelnetがあればUbuntuからでもリモートで設定できますが、うまく行きませんでした。次にL2TP/IPsecです。Windowsではクライアント不要だったり設定が簡単だったりしますが、Ubuntuでは面倒です。L2TP/IPSecについては[[Ubuntu][VPN][自分用メモ] Ubuntu 14.04&14.10 のL2TP/IPsec クライアントツール、「Network Manager L2TP plugin」。](http://kometchtech.blog.fc2.com/blog-entry-1742.html)あたりを参考にしてください。
さて、OpenVPNはVPN Gateではほとんど全てのサーバが対応します。インストールは
`sudo apt-get install network-manager-openvpn`
です。サーバ接続の設定はタスクバーのネットワークアイコンから「VPN接続(V)」→「VPNを設定(C)...」です。「追加」から「保存したVPN設定をインポートする..」を選んで、VPN Gateからダウンロードした.ovpnファイルを開いてください。
設定は「Authentication」のType:を"Password with Certificates (TLS)"に設定し、User Name: Password Private Key Password:をすべてvpnにしてください。そしてテキストエディタで.ovpnファイルを開き、<ca>~</ca>、<cert>~</cert>、<key>~</key>をコピーしてそれぞれhoge-ca.key,hoge-cert.key,hoge-key.keyなどという名前で保存します。そして設定のCA Certificate:、User Cerficate:、Private Key:にそれぞれ指定してください。これで確実にサーバに接続できます。
ちなみにいちいちファイルに分割するのが面倒な方は僕の作った[export2file.py](https://gist.githubusercontent.com/matyapiro31/b2fedbd241ca9de7a9de/raw/2353453a4e8db4f8dca6d34448a3e76c38f22e80/export2file.py)(gist)を使ってみてください。
