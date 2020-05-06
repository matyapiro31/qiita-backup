# Xamarin Android Playerを知る
Xamarin Android PlayerはintelのVT-Xテクノロジーを利用してx86の仮想化を支援することで高速な仮想マシンを実現していますが、Hyper-Vとは排他的な関係にあり、一応同時利用はできますが、そこまで早くなりません。導入などは[Xamarin Android Player のインストール方法 ( Google Apps & Google Play Services 含む )](http://yutawatanabe.hatenablog.com/entry/xamarin-android-player-preview-install)[yutawatanabe.hatenablog.com]を参考にしてください。
Androidの物理キーの操作は
___
音量  大|Ctrl+Up<br/>
音量 小|Ctrl+Down (おそらく)<br/>
90度回転 |Ctrl+Left,Right<br/>
電源  |Escape<br/>
___

# コマンドラインから起動する
`/* Mac OS Xの場合 */`

*`/path/to/Xamarin Android Player`*` --name "Device Name"`

`/* Windowsの場合 */`

*`Path\to\AndroidPlayer.exe`*` --name "Device Name"`

アンロックするにはadbのシェルから

`adb shell input keyevent 82`

としてください。

出典:

1. [How to start Xamarin-Android-Player from command line?](http://stackoverflow.com/questions/27523348/how-to-start-xamarin-android-player-from-command-line)[stackoverflow.com]

2. [Xamarin Android Player](http://developer.xamarin.com/guides/android/getting_started/installation/android-player/)[xamarin.com]
