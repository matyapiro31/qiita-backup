#需要
Debianは現時点で一番多くのアーキテクチャをサポートするOSです。alphaやsparcといった、誰が使うか分からないようなアーキテクチャもサポートしています。(sparcはスパコンの京を使える人なら触るかも。)しかしながらこれら向けにクロスコンパイルするのは並大抵のことではありません。コマンドしか用意しない体質に問題があるような気もしますが、とにかく面倒くさかったので書きます。
#導入
必要なパッケージをインストール
`sudo apt-get install schroot debootstrap qemu-user-static`
例えばarmhf(新しい方)でchrootするにはDebianの場合
`qemu-debootstrap --arch=armhf sid /srv/chroot/sid-armhf ftp://ftp.debian.org/debian/`
Ubuntuの場合
`qemu-debootstrap --arch=armhf trusty /srv/chroot/trusty-armhf http://ports.ubuntu.com/ubuntu-ports/`
qemu-debootstrapはqemuでのエミュレートを自動で設定してくれる便利なツールです。
chrootしてrootを切り替えます。
`sudo chroot /srv/chroot/sid-armhf`
`sudo chroot /srv/chroot/trusty-armhf`
`arch`コマンドで確認してください。
しかしこれではapt-getなどの重要なツールが導入されていません。なぜか分かりませんが。
/var/cache/apt/archivesにパッケージがダウンロードだけされていたりします。
強制インストールしても問題ありません。
`dpkg -i --force-all /var/cache/apt/archives/*.deb`
次にaptで使うリポジトリの設定です。
debootstrapが設定した適当なものになっているので、それぞれ
`deb http://ftp.debian.org/debian sid main`
`deb http://ports.ubuntu.com/ubuntu-ports/ trusty main`
に設定してください。
echoコマンドで設定するのがいいかと思います。
認証鍵が無い時があるので、
`W: There is no public key available for the following key IDs:
KEY_NAME`
と出るので、
`apt-key adv --keyserver keyserver.ubuntu.com --recv-keys KEY_NAME`
とコマンドを打って、鍵を追加してください。
次にlocaleとkeyboardを設定します。
/etc/default/localeと/etc/default/keyboardの元のファイルをコピーしてください。
日本語化はDebianは知りませんが、Ubuntuは
`apt-get install language-pack-ja`
で導入できます。
次に通常ユーザの作成です。sudoできるように
`adduser hoge`
`gpasswd -a hoge sudo`
**I have no name!**とかプロンプトに出ていますが、これを直すには
`sh -c 'echo 127.0.1.1 $(hostname) >> /etc/hosts'`
でホスト名を割り当てます。
通常ユーザに変身するには
`sudo -i -u hoge`
です。
これでqemuを使ったDebian/Ubuntu環境が整いました。
#debhelperを使ってアーキテクチャ別にビルドする
通常debianパッケージはdebuildコマンドでビルドできるようになっています。
署名を抜かします。-aオプションの後にはスペースを入れないでアーキテクチャ名を入れてください。
`debuild -us -uc -aarmhf`
autotoolsなどについては「非RootのAndroidにコマンドをインストールする」を参考にしてください。
Debian/Ubuntuはクロスコンパイルツールが在ったりしますが、原因不明の障害が起こったりするのでqemuを素直に使ったほうがいいということです。
#Dockerとの比較
chrootはDockerと似たようなものですが、明らかにDockerより高速であり、ファイルのコピーやダウンロードなども迅速に行えます。その代わりサンドボックスとしてはあまり機能しない他、導入に時間がかかります。debootstrapなのでDebianとUbuntuしか使えないという欠点もあります。qemuが対応するアーキテクチャなら何でも対応できるので今の所amd64しかないDockerよりも柔軟です。
#Dockerと組み合わせる
Dockerのコンテナ内に構築すればクロスビルド用のイメージが作成できます。その場合でも対応するアーキテクチャはamd64のみであることに注意してください。
#補足
手前で作ったベンチマークツールを使ってみた所、大体x86_64とarmhf(armv7l)では47倍程度の差がありました。ラズパイ使ったほうが早いような気もします。
パッケージhelloのコンパイルで試してみた所、6倍の差がありました。これならまだましですね。
#アーキテクチャについて補足
ARMのアーキテクチャはやたらややこしいです。特に任天堂ハードは。
※任天堂の携帯ゲーム機は互換性を持っていますが、基本的に古いCPU+新しいCPUの2つを同時に搭載するという方式を取っています。
ともかくARMはDebianではarmelとarmhfの2つで区別されており、armelのが古く、armhfのコードは実行できません。
ラズパイは2になってarmhfに対応しました。
Debianが対応しているアーキテクチャは他には
powerpc,aarch64,mips,avr32などがあり、それぞれ前世代の据置型ゲーム機やサーバ、携帯電話、マイコンなどでよく使われています。
#出典
http://qiita.com/ogomr/items/89e19829eb8cc08fcebb
https://wiki.debian.org/ArmHardFloatChroot
https://ueponx.wordpress.com/2013/09/23/ubuntu%E3%81%AB%E3%81%A6%E6%96%B0%E8%A6%8F%E3%83%A6%E3%83%BC%E3%82%B6%E3%81%ABsudo%E3%81%AE%E6%A8%A9%E9%99%90%E3%82%92%E3%81%A4%E3%81%91%E3%82%8B/
http://www.nsc.riken.jp/jigyoushiwake/QA2.html
