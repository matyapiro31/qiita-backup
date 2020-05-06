for文

```bash
for ((i=0;i<10;i++));do
~
done
```
コマンド出力から特定の文字を含む行数をカウント

`command | grep word | wc -l`

変数の変数(可変変数)

```bash
bar=test
foo=bar
echo ${!foo} #test
```

配列

```bash
array=(a b c)
echo ${array[@]} # a b c
echo ${array[*]} # a b c
echo ${array[0]} # a
```

if文

```bash
if [[ 2 > 1 && $a != foo ]] # >が使え､変数aが空でもok.
then
  :
fi
```
