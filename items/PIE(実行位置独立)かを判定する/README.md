Windowsを覗くOSの共有ライブラリは大抵位置独立型です。
Wikipediaによれば固定アドレスでなくても動くということらしい。Windowsが再起動が必要なのはこの辺が関係しているのだろうか。

#PIEの重要性
Android 5.0以降はPIEでないと動きません。
そこでPIEをどうやって判定するのかよく分からなかったので、調べた結果を備忘録として書いておく。

```bash:detect_pic.sh
LANG=C readelf -l <file> | grep Elf -
```
