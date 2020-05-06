> どれも実行時間がばらつくのはUnix Timeを使っているからですね。そのため最大1秒からほぼ0秒まで実行時間がばらけます

秒の値が変わった次のタイミングから計測を開始すればそれほど酷いことないのでは?

```text:
$ cat -n bench.c
     1	#include <stdio.h>
     2	#include <time.h>
     3	
     4	int main(int argc,char **argv) {
     5	    long c_0=0;
     6	    long c_1=1;
     7	    long c_2=c_1;
     8	    unsigned long counter=0;
     9	    time_t start,end;
    10	    start = time(NULL);
    11	    do {
    12	        end = time(NULL);
    13	    }while((end-start)<1);
    14	    start = end;
    15	    do {
    16	        c_0=c_1+c_2;
    17	        c_1=c_2+c_0;
    18	        c_2=c_0+c_1;
    19	        end = time(NULL);
    20	        counter++;
    21	    }while((end-start)<1);
    22	    printf("%lu\n",counter);
    23	    return 0;
    24	}
$ gcc -O2 bench.c
$ ./a.out
298632271
$ ./a.out
339357502
$ ./a.out
303019226
$ ./a.out
313525543
$ ./a.out
298815923
$ ./a.out
308339255
$ ./a.out
332987488
$ ./a.out
326837526
$ ./a.out
298127574
$ ./a.out
305514003
$ 
```

つーか

> ベンチマークの方法としてはフィボナッチ数列を利用しました。

main() の中で計算した変数 c_0, c_1, c_2 の値をどこにも出力していないので当たり前にコンパイラによる最適化で処理は削除されますね。 

```text:
$ gcc -O2 -S bench.c -o -
～ 略 ～
        .globl  main
        .type   main, @function
main:
～ 略 ～
.L3:
        xorl    %edi, %edi              # time の引数 NULL を %edi に設定
        addq    $1, %rbp                # counter の値をインクリメント
        call    time@PLT                # time() 呼び出し
        subq    %rbx, %rax              # time() の戻り値 %rax から 変数 start の値を減算
        testq   %rax, %rax              # ↑の結果を判定(無駄)
        jle     .L3                     # 0 以下であれば .L3 へ分岐
～ 略 ～
$ 
```

結果「time() の値が変わるまで time() が何回呼び出せたか」にしかなっていませんが、time() のような言語により機能や実装が違うものを比べたところでベンチマークとして凡そ意味はないのでは?
