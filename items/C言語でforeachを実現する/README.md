```foreach_def.c
#define foreach(item, array) \
    int __keep,__count,__size;\
    for(__keep = 1, \
            __count = 0,\
            __size = sizeof (array) / sizeof *(array); \
        __keep && __count != __size; \
        __keep = !__keep, __count++) \
      for(item = *((array) + __count); __keep; __keep = !__keep)
```

一次元配列だとこれでOk.
しかし二次元以上にこれを適用することはできない。ポインタから配列の次元の数を特定できないからだ。C言語は静的な言語なのでこれ以上は難しい。
ちなみに元ネタは某Overflow。
