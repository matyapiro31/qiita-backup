apt-getだと<a href="http://qiita.com/white_aspara25/items/723ae4ebf0bfefe2115c?utm_content=buffer4bc67&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer">[Ubuntu] apt-get まとめ</a>というのがあったので、他のコマンドをまとめてみました。

#それぞれのコマンドの使い方

<table><tr>
<th>コマンド</th><th>内容</th></tr>
<tr><td>apt-cache showpkg|showsrc</td><td>パッケージとソースの情報を表示する</td></tr>
<tr><td>apt-cache stats</td><td>パッケージの統計情報を表示する</td></tr>
<tr><td>apt-cache dump</td><td>全てのファイルに対して簡単な情報を表示する</td></tr>
<tr><td>apt-cache dumpavail</td><td>存在しているファイルの情報を表示する</td></tr>
<tr><td>apt-cache unmet</td><td>解決されていない依存関係を表示する</td></tr>
<tr><td>apt-cache show</td><td>パッケージ情報を表示する</td></tr>
<tr><td>apt-cache depends</td><td>パッケージの依存関係を表示する</td></tr>
<tr><td>apt-cache rdepends</td><td>パッケージの再帰的な依存関係を表示する</td></tr>
<tr><td>apt-cache pkgname</td><td>インストールされているパッケージ名すべてを表示する</td></tr>
<tr><td>apt-key add|del</td><td>リポジトリの署名鍵を追加|削除する</td></tr>
<tr><td>apt-key adv</td><td>gpgにオプションを渡して実行する</td></tr>
<tr><td>apt-file search</td><td>ファイルが所属しているパッケージ情報を表示する</td></tr>
<tr><td>apt-rdepends -b</td><td>パッケージのビルド依存関係を再帰的に表示する</td></tr>
<tr><td>apt-rdepends -r</td><td>パッケージの逆依存関係を再帰的に表示する</td></tr>
<tr><td>apt-build </td><td>パッケージをソースからビルドする</td></tr>
<tr><td>apt-offline</td><td>ネットワーク接続のないPCでaptを使ってアップデートする</td></tr>
<tr><td>apt-ftparchive</td><td>aptのリポジトリを作成する</td></tr>
</table>
