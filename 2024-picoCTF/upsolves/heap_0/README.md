---
title: "heap 0"
points: 50
tags: ["pwn"]
---

heapにinput_data / safe_varが並んで配置されている。

safe_varを書き換えることでフラグゲットとなるので、以下のようにして作ったペイロードを提出して終わり。

`python -c "print('a'*50)""`