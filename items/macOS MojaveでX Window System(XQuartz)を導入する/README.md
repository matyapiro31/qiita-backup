#XQuartzについて
Mac OS Xにかつて付属していたソフトウェア。2016年で更新が終わっています。
現在のMojaveにはプリインストールされていないため、
MacでX Window Systemで動くGUIソフトウェアを動かすのに必要だったので導入してみました。
homebrewを使ってインストールすると簡単です。

```
brew update
brew cask install xquartz
```
一応書いておくと、パスワードが要求されます。
このままだと再起動するまで`DISPLAY`変数がセットされていないのでソフトウェアの起動がうまくいかないので、

```
sudo launchctl load -w /Library/LaunchAgents/org.macosforge.xquartz.startx.plist
```
で設定を読み込ませます。
再起動した後はこれは必要ありません。
