Windowsはそのままでは兎角セキュリティが心配な欠陥OSですが、このまえ[Gigazine](gigazine.net)に載っていた方法で簡単に
乗っ取られます。これを防ぐ手立てはあるのでしょうか。
1. BitLockerで暗号化する
BitLockerはWindows XP,Vistaでは利用できません。しかし、7,8/8.1 10ではこの方法で十分防げるでしょう。
しかし、BitLockerもパスワードや鍵さえあれば十分Linuxなどでマウントできてしまいます。
これだけでは普段そのマシンを一般ユーザ権限で利用している人には通用しません。
其の場合はさらに2です。
2. BIOS/UEFIにパスワードを掛けたマシンからのみアクセスできるようにすれば、CD/USBでLinuxなどから起動されることは失くなります。
そしてスタートアップ修復を無効化しましょう。
管理者権限のコマンドプロンプトで
`bcdedit /set {current} recoveryenabled No`
`bcdedit /deletevalue {current} recoverysequence`
と実行してください。
ただしBIOS/UEFI/Windows/BitLockerのパスワードをすべて忘れると回復不能になるのでご注意を。
参照 スタートアップ修復の無効化について - Microsoft TechNet Blog http://blogs.technet.com/b/askcorejp/archive/2013/06/07/3577292.aspx
