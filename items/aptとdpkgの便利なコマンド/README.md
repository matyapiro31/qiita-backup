#色々とあります
apt-*hoge*系は多いです。
よく使うもの:
apt-get
apt-cache
便利なもの:
apt-file aptによってインストールされたファイルについて調べてくれます。
`apt-file search File1`
でファイルFile1がどのパッケージに属するか分かります。これsynapticに加わってくれないかな。
ちなみにdpkgだけだとそんなコマンドはないので
` echo "$(dpkg -S * 2>/dev/null|grep `*`File1`*`)"`
で行けます。.debからのインストールも探す場合はこれ。
apt-rdepends 依存関係、ビルド依存関係を再帰的に調べてくれます。
apt-rdepends --reverse revertが正しい気もしますが、そのパッケージに依存しているパッケージが再帰的に調べられます。かなり強力で、コードのサンプルなどが無い時などに、そのコードを使っているパッケージはあるのか探すのに、非常に役に立ちます。
[このへん](https://wiki.archlinuxjp.org/index.php/Pacman_%E6%AF%94%E8%BC%83%E8%A1%A8)を参考にするといいと思います。Archですが。
