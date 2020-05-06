この記事は[Docker Advent Calendar 2016](http://qiita.com/advent-calendar/2016/docker) の14日目です。
Dockerは最近インストールできなかったりして試せなかったのですが、一昨日にようやく復活させられました。

注意: 以下は個人で検証した内容です。あんまり正しいという保証はできません。

* 検証はUbuntu 16.0.4.1 LTSで行いました。そのためCentOSやUpstartを使う14.04などは違うコマンド・ファイルの参照が必要かもしれません。
* Dockerのバージョンは1.12.3です。

#dockerdをいじるために一旦止める
dockerのサービスを止めます。
`sudo service docker stop`

# 1. Dockerをtcpで動かし、インターネット上からアクセスする
<u><code>-H</code> オプション</u> : 接続するソケットを指定する
-Hオプションはunixドメインソケット、systemdソケット及びtcpソケットに対応しています。
リモートでの接続を行う場合、tcpを用います。tcpを用いる場合、dockerは2375番で平文、2376番ポートでTLSを用いて通信するのが慣例です。
ただし、既定のDockerデーモンの設定には暗号化機能はありません。そのため、暗号化通信を行うにはCA証明書を自前で用意することが必要です。
`sudo dockerd -H tcp://0.0.0.0:2375` - 2375番ポートで通信する場合
この場合、dockerクライアントは
`docker -H tcp://0.0.0.0:2375`で接続できます。
# 2. デーモンを止めたままでもコンテナを実行可能にする
`--live-restore` - デーモンを停止してもコンテナが実行されるようになる
dockerのコンテナは連続稼働が求められることが多いですが、アップデートの際などはどうしてもデーモンを再起動させなければいけません。
そんなときのためにデーモンを止めたままでもコンテナを稼働させるオプションが`--live-restore`です。
このオプションはバージョン 1.12からサポートされていますが、なんかやってみても止まったりしました。(異常終了だと効果がない?)

# 3. イメージの同時ダウンロード/アップロード数を増やす
`--max-concurrent-downloads=3` - pull ごとの最大同時ダウンロード数を指定
`--max-concurrent-uploads=5` - push ごとの最大同時アップロード数を指定
archlinuxのイメージで試してみた結果、どうも同時ダウンロード数は少ない方が単独のダウンロードでは速いという結果でした。

# 4. 使用するストレージドライバの選択
<code>-s <em>driver</em></code> ストレージドライバ(イメージのレイヤ表現のためのドライバ)
Linuxのストレージドライバを選択することができます｡基本的にはどのOSにも1つしかストレージドライバは使われていないようですが､
あなたがDockerを使っているということは多分ストレージドライバを一つしか持っていないか､Dockerデーモンを設定した経験があるかのどちらかであると思います｡なぜなら､ストレージドライバが2つ以上あるときにはDockerデーモンはこのオプションを設定しなければ動かないからです｡多分これは仕様ですが､欠陥ですね｡
使用できるドライバは設定していないならば一種類しかないと思いますが､調べ方は
`docker info`コマンドで三行目に
`Storage Driver: devicemapper`
という感じで表示されます｡
サポートされているのはaufs, devicemapper, btrfs, zfs, overlay, overlay2 です｡vfsもデバッグ用途では使えるそうな｡
Dockerとしてはoverlay2が一番パフォーマンスを出せるようですが､Linuxカーネル4.0以上でしかサポートされない上､まだ不安定であるとしています｡zfs, btrfsはそれぞれのファイルシステムでしか使えないので､aufs, devicemapper, overlayの3つを使いこなすのが良いのではないでしょうか｡
##devicemapper

* RHEL/CentOS/Fedora
* Ubuntu 12.04
* Ubuntu 14.04
* Debian

において既定のドライバであるため､導入は不要です｡
欠点としては次の2点が挙げられています｡
1. 32KiB以下のファイルへの書き込みパフォーマンスとしてはaufsに劣る
2. メモリの使用効率は最悪

##aufs
aufsの導入方法は書かれていませんでしたが､導入されているかどうかは
`grep aufs /proc/filesystems`
で調べることができます｡
aufsには次の利点と欠点があるそうです｡

1. 書き込み速度が遅い｡(特に大きいファイル) これは実感しました｡
2. データサイズの最小化および起動時間の短縮｡一番データサイズが最小化するのはaufsだそうです｡

##overlay/overlay2
btrfs,aufs overlay,overlay2,zfs,eCryptfsを用いたファイルシステムでは使えないそうです｡つまり､extとxfs専用ですね｡
overlayはカーネル3.18以上でサポートされています｡しかし､使えるかどうかの条件はいまいち不明なので､実験はできませんでした｡

#tlsで使う証明書を指定する
クライアント側でも`--tlsverify`を用いる必要があります｡
デーモンでは
`--tls` - TLSを使用、--tlsverify を含む
`--tlscacert="/etc/docker/ca.pem"` - この CA で署名された証明書のみ信頼
`--tlscert="/etc/docker/cert.pem"` - TLS 証明書ファイルのパス
`--tlskey="/etcdocker/key.pem"` - TLS 鍵ファイルのパス
の3つを使用し､`-H`オプションでtcpを使用することでtlsによる暗号化通信ができるようになります｡
CA証明書の保存場所は/etc/dockerが良いでしょう｡(dockerdの権限は完全なrootではないため)

#ユーザ名前空間を使う
`--user-remap="nobody:docker"` ユーザをnobodyに､グループをdockerに設定する
Dockerの1.12までは名前空間のサポートがないため､rootユーザ以外の一般ユーザが`sudo`なしで使用することは事実上重大な脆弱性となり得るため､推奨されていませんでした｡しかし､このユーザ名前空間のサポートにより､コンテナ内のuid/gidをホストから分離することが可能になりました｡ただし､これによって`--privileged`を付けてコンテナを動作させることができなくなり､`mount`などのコマンドがコンテナ内で使用不可になったりします｡

#設定をファイルに保存する
dockerデーモンの設定は/etc/docker/daemon.jsonファイルに書き込むことで常時反映させることができます｡
/etc/docker/daemon.jsonのテンプレートはhttps://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file にあります｡
参考:

1. [dockerd](//docs.docker.com/engine/reference/commandline/dockerd/#/storage-driver-options)
1. [Device Mapper ストレージ・ドライバの使用](//docs.docker.jp/engine/userguide/storagedriver/device-mapper-driver.html)
1. [ストレージ・ドライバの選択](//docs.docker.jp/engine/userguide/storagedriver/selectadriver.html)
