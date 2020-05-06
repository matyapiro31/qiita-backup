# 制御構文(statement)とは何ぞや#
制御構文とは処理を制御するための文法構造のことです。そのまま言っただけですが、制御構文をうまく利用することがプログラミングの上達に深く関わってきます。しかし、自然言語とは異なる文法を持つためにか、非常にわかりにくい書き方をする人もいます。過不足無く制御構文を書くやり方を紹介します。
#失敗例1#
C言語による簡単な通貨換算プログラムを例に採ってみます。

```exchange.c
#include <stdio.h>

int main (void) {
    char orig[255];
    char exch[255];
    int tmp;
    /*円ドル換算*/
    scanf("%s",orig);
    if(orig[0]=='\\') {
        exch[0] = '$';
        tmp = atoi(orig+1);
        tmp = 0.0081 * tmp;
        sprintf(exch+1,"%d",tmp);
        printf("%s\n",exch);
    }
    else if(orig[0]=='$') {
        exch[0] = '\\';
        tmp = atoi(orig+1);
        tmp = 124.21 * tmp;
        sprintf(exch+1,"%d",tmp);
        printf("%s\n",exch);
    }
    else {
        printf("Error:input incorrect.\n");
    }
    return 0;
}
```

これは効率が悪いです。同じ処理を別々の条件の中に書いてしまっています。(それ以外にもベタに初心者くさいのもわざとです。)
`else {~}`では同じ処理ができないので仕方ないじゃん!と主張する人もいます。ではこれをどう改良するのか?次の例のような書き方をする人もいます。
#失敗例2#

``` exchange2.c
#include <stdio.h>

int main(void) {
    char orig[255];
    char exch[255];
    int tmp;
    /*円ドル換算*/
    scanf("%s",orig);
    if(orig[0]=='\\'||orig[0]=='$') {
        exch[0] = orig[0]=='\\'?'$':'\\';
        tmp = atoi(orig+1);
        tmp = orig[0]=='\\'?0.0081 * tmp:124.21 * tmp;
        sprintf(exch+1,"%d",tmp);
        printf("%s\n",exch);
    } else {
        printf("Error:input incorrect.\n");
    }
    return 0;
}
```
なんとなくワイド文字にしてみましたが、意味はありません。先ほどと違って2つの処理をきちんとまとめています。しかし、これでもまだ失敗です。
`else {~}`の部分がまだ残っています。あまりにも`{}`が増えすぎると何がどこの括弧の中なのかさっぱりわからないだけでなくインデントが増えて大変見にくくなります。

#自分が精一杯に簡潔に書いた例#

```exchange3.c
#include <stdio.h>
#define X '$' ^ '\\'
int main(void) {
    char orig[255];
    int exc;
    /*円ドル換算*/
    scanf("%s",orig);
    if (orig[0]!='\\'&&orig[0]!='$') {
        printf("Error:input incorrect.\n");
        return 1;
    }
    /*それぞれの変換レートで変換*/
    exc = (orig[0]=='\\'?0.0081:124.21) * atoi(orig+1);
    printf("%c%d\n",orig[0] ^ X,exc);
    return 0;
}
```
元の`else {~}`の処理は後にコードが無いのでreturn文を書いて終了しています。これで処理されるのは条件が合致している場合だけです。
おかげで制御構造が一つ減っています。
当たり前なんですが、ほとんど実行速度に差はありませんでした。
実際もっと短くできるけど見づらいと意味ないのでこれも失敗例としてあげときます｡
#失敗例3#
```exchange4.c
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    char orig[255];
    scanf("%s",orig);
    if (orig[0]!='\\'&&orig[0]!='$') {
        abort();
    }
    printf("%c%d\n",orig[0] ^ '$' ^ '\\',(orig[0]=='\\'?0.0081:124.21) * atoi(orig+1));
    return 0;
}
```
こう書くと元の半分以下になりますが､分かりづらい上に実際大して上の3つと変わりません｡極力一回しか使わなくてもマクロか変数宣言はしたほうが何をしているのか分かりやすくなります｡
