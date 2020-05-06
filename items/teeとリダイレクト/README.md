#リダイレクトとして利用できるコマンドtee
teeはリダイレクトの代用として利用できます。

```bash:tee_test.sh
#1 上書き
echo test>file
echo test|tee file
#2 追加書き込み
echo test>>file
echo test|tee file -a
#3 そのまま標準出力に出力
echo test
#二重になる
echo test|tee -
#正解
echo test|tee /dev/null
```
##teeを使うポイント
1. teeなら標準出力とファイルの両方に同時出力できる。
2. 標準エラー出力をフィルタできる。
3. 標準機能ではなくコマンドなのでエイリアスなどが可能。Windowsだと出力文字コードを変換するなど。
4. echoのようなbash組み込み関数に`sudo`コマンドでroot権限を取れる。
5. コマンドなので無いかもしれないし、遅い。

```bash:tee_vs_redirect.sh
#2 hogeというファイルはないとする
cat hoge 1>log
cat hoge|tee log
#3 Shift-JISに変換する。リダイレクトは無理。
alias tee='nkf -s|tee'
echo "ほげ"|tee
#4 /etc/apt/sources.listに書き込み
sudo bash -c "echo \"deb http://example.com/ latest main\">>/etc/apt/sources.list"
echo "deb http://example.com/ latest main"|sudo tee -a /etc/apt/sources.list
#5 例えばAndroidとか
tee
#エラーになる
```
どれだけ遅いのか試してみると、teeだと8秒、リダイレクトだと3秒でした。
二倍以上違うので、そうやたらと使ったり、容量の大きいファイルの処理には向かないかもしれません。
