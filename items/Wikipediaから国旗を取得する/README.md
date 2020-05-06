#xmllintを使ってWikipediaの構造を解析する
Wikipediaで国名を調べて自動的に国旗を取得するプログラムを作りたかったので、ChromeでWikipediaの構造を解析した。
例えばシエラレオネの場合、国旗のimg要素の中のsrc属性で指定されているpngファイルは

```html
<img alt="シエラレオネの国旗" 
src="//upload.wikimedia.org/wikipedia/commons/thumb/
1/17/Flag_of_Sierra_Leone.svg/
125px-Flag_of_Sierra_Leone.svg.png"
...
>
```
となっている。(長いので中略&改行を入れている)
其れに対し、元のSVGファイルのURLはこうなっている。
`https://upload.wikimedia.org/wikipedia/commons/1/17/Flag_of_Sierra_Leone.svg`
これより、`/thumb`と`125px-...`を除けば正しいURLになることが分かる。
また、この要素は\<html>からの配置も固定されている。
xmllintではXML構造を通常のファイル構造と同様に閲覧することができるシェルモードが用意されている。
同じ要素が幾つも存在する場合はdiv[3]のように番号を指定する。属性は@srcのように名前を指定する。
echoを使って対話モードに放り込みます。こんな感じです。

```bash
echo "cat /html/body/div[3]/div[3]/div[4]/dl[1]/dd/table/tr[1]/th[1]/a/img/@src" \
|xmllint --shell $URL
```
あとはURLの文字列をちょいちょいといじってwgetするだけです。
自動でGIMPを起動したり、対話モードで複数ダウンロードしたりするようにしたのがこのスクリプトです。

```bash
#!/bin/bash
function get_flag() {
  country=${1}
  wget -qO- https://ja.wikipedia.org/wiki/"${country}" > /tmp/country.tmp
  array=(`echo "cat /html/body/div[3]/div[3]/div[4]/dl[1]/dd/table/tr[1]/th[1]/a/img/@src"|xmllint --shell /tmp/country.tmp|tail -2|head -1|sed -e 's/\// /g'`)
  rm /tmp/country.tmp

  log=$(LANG=C wget  https://upload.wikimedia.org/wikipedia/commons/${array[5]}/${array[6]}/${array[7]} 2>&1)
  fname=( $(echo "$log"|tail -1) )
  if [ "${fname[5]}" != "Found." ];then
    if [ ${2} != "true" ];then
      gimp "${fname[5]:1:-1}" & >/dev/null
    else
      echo Done.
    fi
  else
    echo Faild to get flag of ${country}.
  fi
}
#option detection.
while getopts "id" opts
do
  if [ ${opts} = "i" ];then
    std_in="true"
  elif [ ${opts} = "d" ];then
    download_only="true"
  fi
done

while [ ${std_in:-false} != "true" -a -n "${1}" ]
do
  get_flag "${1}" ${download_only:-false}
  shift
done

while [ ${std_in:-false} = "true" ]
do
  printf '> '
  read -r country
  if [ "${country}" = "exit" -o "${country}" = "quit" ];then
    exit 0
  fi
  if [ -n "${country}" ];then
    get_flag "${country}" ${download_only:-false}
  fi
done
```

参考:
1. [xmllint で xml をディレクトリみたいにｃｄして辿る - それマグで!](http://takuya-1st.hatenablog.jp/entry/2015/09/30/000000)
2. [ShellScriptでXMLの内容を取り出す | Opentone Labs.](http://labs.opentone.co.jp/?p=5273)
