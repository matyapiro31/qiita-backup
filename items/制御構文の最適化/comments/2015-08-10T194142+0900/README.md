要するに入力の先頭の文字を見て変換先の通貨と交換レートを決めるというところだけ処理が異なるわけですから、このようにするのがよさげだと思います。

```c
int main (void) {
    char orig[255];
    char exch[255];
    float rate;
    char toCurrency;
    int tmp;

    scanf("%s",orig);

    switch (orig[0]) {
        case '\\':
            toCurrency = '$';
            rate = 0.0081;
            break;
        case '$':
            toCurrency = '\\';
            rate = 124.21;
            break;
        default: 
            printf("Error:input incorrect.\n");
            return 1;
    }

    tmp = atoi(orig+1);
    tmp = rate * tmp;
    sprintf(exch, "%c%d", toCurrency, tmp);

    printf("%s\n", exch);

    return 0;
}
```
