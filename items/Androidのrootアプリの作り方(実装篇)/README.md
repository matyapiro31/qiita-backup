#rootアプリを実際に書いてみる
基本的にJavaはできないので、あんまりAndroidは触れたくなかったのですか、こういうのは面白そうなのでやってみました。
まず、こういうアプリはBusyboxが入っているのが前提なので、テスト環境にもBusyboxを入れる必要があります。Busyboxなしでも大丈夫ですが、そうするとアプリの容量が大きくなってしまうという問題が発生します。
android向けにコマンドをビルドする方法については、[非RootのAndroidにコマンドをインストールする
](http://qiita.com/matyapiro31/items/17d8fec6f32ebf9017a4)を参考にしてください。
PIE対応か否かで動いたり、動かなかったりするそうです。
参照:http://dsas.blog.klab.org/archives/52211448.html

#こういうのを作る
chattrコマンドを使った、ファイルをロックするアプリを作って見たいと思います。
chattrはext2,3,4などの拡張ファイル属性を扱うコマンドです。
参考:【chattr】ファイルの属性を変更する IT Pro,2014/01/21,中島 能和
http://itpro.nikkeibp.co.jp/article/COLUMN/20140115/529923/

このコマンドにはファイルを削除不能にするという機能があります。この状態を解除できるのもこのコマンドだけなので、ファイルシステムごとフォーマットしない限り、
ファイルを削除されたり、暗号化されたりすることは失くなります。例え相手がrootでも、です。

欠点:extファイルシステムでないと使えません。大抵のユーザーはSDカードをNTFSやらFAT32やらでフォーマットしているので、外部SDカードには使えないという欠点があります。なので、ほんとにテスト用です。

#使用するJavaのクラス
1. java.lang.Runtime
2. java.lang.ProcessBuilder
2. java.lang.Process

RuntimeクラスまたはProcessBuilderクラスでコマンドを実行→Processクラスで標準入出力や同期を操作という流れで行います。
(注:Unixプロセス間通信をしたい時、標準のJavaではサポート外ですが、Androidでは実装されています。)
コマンドの実行を実現するには、2つの方法があります。
1. `su -c`でコマンドごとにrootを取得する。
2. `su`を実行し、標準入力からコマンドを実行していく。スクリプトを実行できます。一部、こちらでしか動かないコマンドもあります。(例:mount)

##1. su -cでコマンドごとに実行
この方法の利点と欠点を見てみましょう。
利点. コードが簡潔になる。2の方法では、`su`を実行しその標準入力にコマンドを入力する必要があるので、エラー処理を含め複雑になります。ちょっとだけ書くにはこちらがいいです。また、プロセスの終了=コマンドの終了なので、`su`にexitコマンドを投げ忘れていつまでも終了しない、という不安から解放されます。
欠点. コマンド実行ごとにスーパーユーザーリクエストをするので、毎回確認したいユーザーにとっては不便です。 
Javaのコードはこんな感じです。

```java
//プロセスを作成
String[] cmdarray = new String[] {"su", "-c", "echo Hello root!"};
Runtime runtime = Runtime.getRuntime();
Process process = null;
String output;
try {
    process = runtime.exec(cmdarray);
    String str_stdout = "";
    //標準入力をセット。
    BufferedReader read_stdout = new BufferedReader(new InputStreamReader(process.getInputStream()));
    while ((String line=read_stdout.readLine())!=null) {
        str_stdout +=line + "\n";
    }
    read_stdout.close();
} catch (IOException e) {
    e.printStackTrace();
}

```

##2. suの標準入力に送信
こちらは`su`のプロセスを一つ立てて置けばそのまま再利用が可能になります。exitコマンドを投げ忘れると大変ですが。
コードはこんな感じです。この辺はAndroidというよりデスクトップのJavaで良くやることなので、コードも豊富にあると思います。
コマンドをsuの標準入力に入れて実行するごとに改行するだけです。

```java
String[] cmdarray = new String[] {"su"};
//processを取るところまでは同じなので省略。
String[] commands = new String[] {"echo a", "exit"};
DataOutputStream d_out = new DataOutputStream(process.getOutputStream());
for (String command:commands
    ) {
    d_out.writeBytes(command + "\n");
}
```
#非同期で実行
`Runtime#exec()`を使っているときは元のプロセスが同期されてしまうので、コマンドの実行が終了するまで待機してしまい、フリーズします。
そこで、非同期で処理を行いたい時は、`ProcessBuilder#start()`を使いましょう。

```java
process = new ProcessBuilder(Arrays.asList(new String[] {"su", "-c", "command"})).start();
```
Activityを停止させたい処理は多分そんなに無いと思うので、こちらを使うといいと思います。
(おそらく`Runtime#exec()`はC言語の`system`関数、`ProcessBuilder#start()`は`fork`関数のラッパーだと思われます。) 

#実際に作ってみたアプリ
root権限でファイルをロックするだけの簡単なアプリです。
<a href="https://github.com/matyapiro31/Stopfiledelete">ここ</a>で公開しています。
自作のShellクラスです。環境変数の設定辺りはまだ書いてません。(要らないけど。)

```java:Shell.java
package com.example.www;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;

public class Shell {
    public static String exec_log="";
    public static Process execShell(String[] cmdarray) {
        Runtime runtime = Runtime.getRuntime();
        Process process = null;
        String output;
        try {
            process = runtime.exec(cmdarray);
        } catch (IOException e) {
            e.printStackTrace();
        }
        java.util.Date date= new java.util.Date();
        exec_log += "shell command `" + cmdarray[0] + "` was executed. (" + date.toString() + ")\n";
        exec_log += "arguments are";
        exec_log += " \'";
        for (String arg:cmdarray
                ) {
            exec_log += arg.equals(cmdarray[0])?"":" " + arg;
        } exec_log += "\'\n";
        exec_log += "process is " + process.toString() +"\n\n";

        return process;
    }

    public static String getProcessStdOut(java.lang.Process pr) {
        String str_stdout="";
        try {
            String line;
            BufferedReader read_stdout = new BufferedReader(new InputStreamReader(pr.getInputStream()));

            while ((line=read_stdout.readLine())!=null) {
                str_stdout +=line + "\n";
            }
            read_stdout.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return str_stdout;
    }

    public static String getProcessStdErr(java.lang.Process pr) {
        String str_stderr="";
        try {
            String line;
            BufferedReader read_stderr = new BufferedReader(new InputStreamReader(pr.getErrorStream()));

            while ((line=read_stderr.readLine())!=null) {
                str_stderr +=line + "\n";
            }
            read_stderr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return str_stderr;
    }

    public static boolean setProcessStdIn(java.lang.Process pr,String[] text) {
        try {
            DataOutputStream d_out = new DataOutputStream(pr.getOutputStream());
            for (String word:text
                    ) {
                d_out.writeBytes(word+"\n");
            }

        } catch (IOException e) {
            e.printStackTrace();
            return false;
        }
        java.util.Date date= new java.util.Date();
        exec_log += "Standard input for " + pr.toString() + " received text. (" + date.toString() + ")\n";
        for (String word:text
                ) {
            exec_log += word + "\n";
        }

        return true;
    }

    public static int exitProcess(java.lang.Process pr) {
        int ret;
        try {
            pr.waitFor();
            ret = pr.exitValue();
            if (ret == -1) {
                ret = 0x10;
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            pr.destroy();
            ret = -1;
        }
        exec_log += "process " + pr.toString() + " is exited. (" + new java.util.Date().toString() + ")\n\n";
        return ret;
    }
}

```
##シェル関数を使う
シェル関数はシェルスクリプトのコマンドを纏めるために非常に役に立ちます。
いちいちこれをJavaで書いていたら色々と大変なので、アプリ専用のディレクトリにシェル関数を展開しました。
まず、mkshrcというファイルを作成し、res/rawに配置しましょう。
こんな感じで。

```bash
function fullname() {
        if [ ! -e $1 ];then
            echo No such file or directory. >&2
            return 1
        else
            if [ -f $1 ];then
                echo $(cd $(dirname $1)&&pwd)/$(basename $1)| \
                sed -e 's/\/\{2,\}/\//g'
            elif [ -d $1 ];then
                echo $(cd $1 &&pwd)| \
                sed -e 's/\/\{2,\}/\//g'
            fi
            return 0
        fi
}
function list_files() {
    if [ $(fullname $1) != "/" ];then
        echo ../
    fi
    busybox ls -1ap $1|tail -n +3
}
```
今回はroot権限でないと見られないディレクトリの中身を覗くために使用しました。
このファイルを展開するには、

```java
 InputStream is = this.getResources().openRawResource(R.raw.mkshrc);
//ファイルのサイズまたはそれ以上の数値を設定。
 byte[] binary = new byte[2048];
 try {
     is.read(binary);
     openFileOutput(".mkshrc", MODE_PRIVATE).write(binary);
} catch (IOException e) {
    e.printStackTrace();
}
```
とこのようにやりました。これでリソースからファイルを出力できます。
シェル関数を使うには同一プロセス内で先に`source`コマンドを実行して関数を読み込んでおく必要があります。
#おまけ - その1 chrootコマンドで普通のLinux環境を作る
Androidではアプリは`/data/data/`以下の専用のディレクトリが与えられています。
ここにchrootのための専用のディレクトリを作成しましょう。いくつかのディレクトリをマウントする必要があります。

```bash:一例
mkdir chroot
cd chroot
mkdir system
mkdir dev
mkdir proc
mount -o bind /system ./system
mount -o bind /dev ./dev
mount -o bind /proc ./proc
chroot . /system/bin/sh
```
chrootすることでいちいち`/system`や`/`を書き込み可能なようにマウントし直す必要が失くなります。また、アンインストールした後にゴミが残ることがなくなります。
chroot環境はdebootstrapで構築するのがおすすめです。
prootやfakechrootを使えば非rootでもchroot出来ます。
参考: <a href="https://wiki.debian.org/ChrootOnAndroid">ChrootOnAndroid</a> Debian Wiki
prootのARM Android向けのバイナリはDebian no rootのデータの中にあります。

#おまけ - その2 ケーパビリティを使ってアクセス権限を制限する (実験中)
ケーパビリティはややこしいですが、少しだけ特殊な権限を与えるというものです。
<a href="http://www.usupi.org/sysad/183.html">参考</a>
ケーパビリティも拡張属性の一種と言えます。
`setcap`コマンドを使って一度*`CAP_SYS_CHROOT`*をchrootコマンドに当てれば次回からroot無しでchroot出来ます。

```bash
 su -c "setcap cap_sys_chroot=ep /system/sbin/chroot"
#su無しでchroot出来てしまう
chroot
```
他にもlsに*`CAP_DAC_READ_SEARCH`*を与えることでどんなディレクトリでも閲覧できるようになるという恐ろしいこともできます。
しかし、カーネルのバージョンによってはケーパビリティがサポートされていない場合があります。
便利ですが、なんかやってみてダメだったので、もしかしたらAndroidだとうまく行かないかもしれません。

#おまけ - その3 他のアプリが実行したコマンドの詳細を調べる
root権限があれば、/proc/\<process id\>/cmdlineを閲覧することによって他のアプリがどのような操作をしているのかを知ることが出来ます。
プロセス番号を調べるには`ps`コマンドを使いましょう。
アプリのパッケージ名を調べておき、そのパッケージ名のPIDが親プロセスID(PPID)となっているプロセスを調べるのがいいでしょう。
コマンドの引数の区切り文字は\0ですが、Androidの通常のsedでは\0を置換できないのでちょっとシェルスクリプトでは難しいと思います。
