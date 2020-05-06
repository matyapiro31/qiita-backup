#sensorsコマンドの導入
[この辺](http://gihyo.jp/admin/serial/01/ubuntu-recipe/0183)を参考にしてください｡
apt,yumともに標準でパッケージに入っています｡

#コマンドから表示される内容を更新する
繰り返し文を利用します｡

```
while [ 1 ];do
 sensors
#表示の更新間隔
 sleep 5s
done
```
これで5秒間隔でCPU情報が更新されます｡もし止めたい時は^Cしてください｡
