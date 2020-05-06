何を以て簡潔とおっしゃってるのかわかりませんが、数値のエラーチェック等これ以上追加しないのであれば

```c:
#include <stdio.h>
#include <stdlib.h>

int main (void) {
    char orig[255];
    /*円ドル換算*/
    scanf("%s",orig);
    if(orig[0]=='\\') {
        printf("$%.f\n", 0.0081 * atoi(orig+1));
    }
    else if(orig[0]=='$') {
        printf("\\%.f\n", 124.21 * atoi(orig+1));
    }
    else {
        printf("Error:input incorrect.\n");
    }
    return 0;
}
```
とかでいいのでは?
