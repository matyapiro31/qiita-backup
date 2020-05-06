NativeScriptでエミュレータを起動しようとしたら結構大変だったのでそのメモです。
Androidエミュレータをコマンドラインだけでインストールして起動するまでの手順です。
#Android SDKのインストール
Homebrewでインストールします。

`brew cask install android-sdk`
パスを環境変数に登録すると便利です。
`echo export ANDROID_HOME=/usr/local/share/android-sdk >> ~/.bash_profile`
#必要なパッケージのインストール
API 25のビルドツールやライブラリ、システムイメージなどをインストールします。
インストールできるパッケージは
`ANDROID_HOME/tools/bin/sdkmanager --list`
で確認できます。

```bash
$ANDROID_HOME/tools/bin/sdkmanager --install ¥
  "tools" "platform-tools" "platforms;android-25" ¥
  "build-tools;25.0.2" "extras;android;m2repository" ¥
  "extras;google;m2repository" "system-images;android-25;google_apis;x86"
```
#AVDの作成
AVDを作成するには

1. デバイス名
2. 使うシステムイメージ
3. デバイスのプロファイル

が必要です。ここでは先ほどインストールしたパッケージを使います。
SDカードのサイズの指定をしておくといいらしいのでしておきます。

```bash
$ANDROID_HOME/tools/bin/avdmanager create avd ¥
  --name "nexus6p" --device "Nexus 6P" --abi "google_apis/x86" ¥
  -k "system-images;android-25;google_apis;x86" --sdcard 512M
```
#エミュレータの起動
起動には先ほど作ったデバイス名が必要です。

```bash
$ANDROID_HOME/emulator/emulator -avd "nexus6p"
```
#エミュレータの設定
ホームディレクトリに`.android`というディレクトリが作られ、そこにAVDと設定があるのでそれを編集する。ストレージを増やしたいなら`disk.dataPartition.size=1024MB`とか。

```bash
nano ~/.android/avd/nexus6p.ini
```
#Dockから起動できるようにする
起動用のAppleScriptを作ります。

```bash
cd /Applications
echo | osacompile -o Android¥ Emulator.app
cd Android¥ Emulator.app/Contents/Resources/Scripts/
open main.scpt
```
スクリプトに

```applescript
try
	do shell script "/usr/local/share/android-sdk/emulator/emulator -avd nexus6p"
end try
```
と書けばLauncherに登録され、起動できるようになります。
#参考資料
https://docs.nativescript.org/start/ns-setup-os-x
https://stackoverflow.com/questions/42792947/how-to-create-android-virtual-device-with-command-line-and-avdmanager
https://qiita.com/mattintosh4/items/83e1540c31c803c3fd5e
