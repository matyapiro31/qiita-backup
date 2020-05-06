PowerShellがオープンソース化され、Linux,macOS版がリリースされました。
GitHubで公開されています。https://github.com/PowerShell/PowerShell
#なぜPowerShellがUbuntuで動くのか?
ITMediaの記事にも有る通り、PowerShellは.NET Framework上で動くアプリケーションです。
先だってオープンソース化されLinux,macOS向けにリリースされていた.NET Coreのおかげです。
元々.NET FrameworkはJavaと同じくプラットフォームに依存しないような設計であるため、PowerShell自体もプラットフォーム依存性はそこまで大きくなかったのだと思われます。また、PowerShell自体もフォルダ・ディレクトリの区切りが "\"と "/"の両方をサポートする、コマンドレットの引数の形式など、マルチプラットフォームに対応しやすくなっているのも大きいかもしれません。

#触ってみて実験
1. 改行が変なことになることを発見。 → 端末エミュレータの横幅が狭すぎると折り返しが入るだけだった。
2. typeコマンドがどうなるのか実験 → PowerShellの組み込み(Get-Content)として認識される。Linuxのtypeの方を使うには`bash -c type 引数`
とすれば良い。
3. 日本語入力 → typeコマンドで文字化けしたファイルを表示したりすると、Ibusを使っているときは変な表示になることもある。
4. プロファイルの場所 →  ${HOME}/.config/powershell/Microsoft.PowerShell_profile.ps1
5. Linux側の環境変数の表示 → ``bash -c echo `$LANG`` もしくは`printenv LANG`
6. shebang → `#!/usr/bin/powershell` で大丈夫。WindowsだとPowerShellのスクリプトの実行制限は厳しいですが、Linuxだとそうでもないようです。


#できること、できないこと
.NET Frameworkの機能を使うことは可能ですが、電源管理などの権限の関係で使えないもの、WMIのようにWindows固有のものなどは使えません。
また、`ls`などもエイリアスではなく元のコマンドが使えるようになっており、Windows向けに作ったps1ファイルだとうまく動かない可能性があります。

sshを用いてWindowsのPowerShellを操作することもできるようになっています。
https://github.com/PowerShell/PowerShell/tree/master/demos/SSHRemoting
