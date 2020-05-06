#なぜかCygwin
最初はコマンドプロンプトを使ってショートカットを作ろうと思ったが､なぜかコマンドプロンプトの実行引数をショートカットから指定できなかった｡そこでCygwinを使いました｡別にコマンドプロンプトでもいいです｡
![property.png](https://qiita-image-store.s3.amazonaws.com/0/44129/d16b07e5-e469-ef5d-8c2a-3c91069c7583.png)
こんな感じで､作業フォルダにadbの実行ファイルがあるフォルダを指定してください｡
Cygwinのショートカットの後ろに`./adb -d shell`と追加すればadbのシェルを指定できます｡

#デバイスを選択できるようにする
zenityを使いました｡<a href="http://www.placella.com/software/zenity/">このサイト</a>からダウンロードできます｡
zenityはWin/Mac/Linux全てで使えるので､便利です｡
zenityは初期設定位置にインストールされているとします｡
するとこのスクリプトでデバイスの選択画面が出せます｡

```bash:selector.sh
#!/bin/bash
function selector() {
export PATH=$PATH:/usr/bin
./adb start-server
#10個だけ表示｡多いと遅くなる｡
for i in `seq 10 -1 1`
do
  line="$(./adb devices|tail -n +$i|head -n 1|sed -e 's/\r//g')"
 if [ ! -z "${line}" ]
 then
  if [ "${line:0:4}" = "List" ]
  then
   break
  else
   array=($line)
   dev_name=${array[0]}
   dev_model="$(./adb -s ${array[0]} shell cat /system/build.prop | grep ro\.product\.model | awk -F"=" 'NR==1 {print $2}' | sed -e 's/ /_/g'| awk -v RS='\r\n' '{print $1}')"
   echo FALSE "${dev_name}" "${dev_model}" " "
  fi
fi
done
}

function run_zenity() {
export PATH=$PATH:"/cygdrive/c/Program Files (x86)/zenity/bin":/usr/bin
zenity --list --radiolist --title="Select Devices..." --column=" " --column="デバイス名" --column="モデル名" \
`selector|tac`
}

selected_device="$(run_zenity|sed -e 's/\r//g')"
./adb -s "${selected_device}" shell

```
※改行が変なのはほっといてください｡あとはスペースが入るところがアンダーバーに置き換わってますが､単に技術力の限界です｡
\rを削除するコードは多分環境によって変わると思います｡MacやLinuxだと必要ないでしょう｡
するとこんな感じで表示できます｡ ※一つ目のデバイスは消してます｡
![zenity.png](https://qiita-image-store.s3.amazonaws.com/0/44129/bf9d9b5d-c3ee-6db4-4649-0771fd3b7bd9.png)
選択してOKを押せば自動的にadbのシェルに入ります｡
