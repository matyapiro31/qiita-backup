このうちValaについては知らない人も多いと思いますが、「C言語に」コンパイルするオブジェクト指向というかほぼC#みたいな言語です。
文字通りコンパイル先がC言語であり、MonoのライブラリのGtk#は元々Valaのためのものですから、そういう意味でXamarinとは親和性が高いと言えます。
ベンチマークの方法としてはフィボナッチ数列を利用しました。できる限り似たような方法で行っていますが、C言語だけ何故か実行時間の数値が微妙です。どれも実行時間がばらつくのはUnix Timeを使っているからですね。そのため最大1秒からほぼ0秒まで実行時間がばらけますが、同じようなコードということで多めに見てください。まずはコードを見てみましょう。殆ど同じコードです。特に変数の宣言やdo~whileの中などですね。配列にしていないのは配列の要素のアクセスとか宣言は微妙に異なるからでしょうか。
#Vala

```bench.vala
using GLib;

public class benchmark {
    public static int main(string args[]) {
        long c_0=0;
        long c_1=1;
        long c_2=c_1;
        ulong counter=0;
        time_t start,end;
        start = time_t();
        end = start;
        do {
            c_0=c_1+c_2;
            c_1=c_2+c_0;
            c_2=c_0+c_1;
            end = time_t();
            counter++;
        }while((end-start)<1);
        print("%lu\n",counter);
        return 0;
    }
}
```
#Java

```bench.java
import java.util.Calendar;

public class bench{
    public static void main(String args[]){
        long c_0=0L;
        long c_1=1L;
        long c_2=c_1;
        long counter=0L;
        Calendar start,end;
        int s,e;
        start = Calendar.getInstance();
        s = start.get(Calendar.SECOND);
        end = start;
        do {
            c_0=c_1+c_2;
            c_1=c_2+c_0;
            c_2=c_0+c_1;
            end = Calendar.getInstance();
            e = end.get(Calendar.SECOND);
            counter++;
        }while(e-s<1);
        System.out.println(counter);
    }
}
```
#C/C++

```bench.c
#include <stdio.h>
#include <time.h>

int main(int argc,char **argv) {
    long c_0=0;
    long c_1=1;
    long c_2=c_1;
    unsigned long counter=0;
    time_t start,end;
    start = time(NULL);
    end = start;
    do {
        c_0=c_1+c_2;
        c_1=c_2+c_0;
        c_2=c_0+c_1;
        end = time(NULL);
        counter++;
    }while((end-start)<1);
    printf("%lu\n",counter);
    return 0;
}
```
#<no>C#</no>

```bench.cs
using System;

public class A
{
    public static int Main(String[] args)
    {
        long c_0=0;
        long c_1=1;
        long c_2=c_1;
        ulong counter=0;
        DateTime start,end;
        end = start = DateTime.Now;
        do {
            c_0=c_1+c_2;
            c_1=c_2+c_0;
            c_2=c_0+c_1;
            end = DateTime.Now;
            counter++;
        }while((end-start).TotalSeconds<1);
        Console.WriteLine(counter);
        return 0;
    }
}
```

# 実行結果
統計的にやるのはフィボナッチの増加の割合O(x)を考えないといけないので、できるだけ一秒に近いものを書いておきます。
なお、C#はMonoを用いてきちっとAOTコンパイルしています。Javaはしていません。AndroidではJITが使われているためです。全部をAndroidのVMで動かすことは~~難しい~~面倒くさいのでそのままOracle JDKですが、案の定一番悪い結果になりました。


**一位**
C/C++
118522356回 per second
当たり前といえば当たり前ですが、一番速いです。速すぎます。
**二位**
Vala
85473580回 per second
C/C++と殆ど変わりません。後続の2つを大きく引き離しています。
**三位**
C#
3856898回 per second
C/C++からは実に29倍、Valaでも21倍の差があります。もう一度言いますがAOTコンパイルしています。
四位
Java
3020728回 per second
C#が約1.2倍速いです。やはりネイティブで無い点、分は悪いですね。ちなみにC/C++の約40分の一です。

#結論
XamarinのC#はネイティブコードです。しかしC/C++と比べてみて遅いのは違いありません。
C++ではなくC#を使いたいが遅いのを何とかしたいという貴方にはValaをオススメします。ただしC#からは使いづらいですよ。速度が必要な処理の際に呼び出してみてもいいでしょう。
