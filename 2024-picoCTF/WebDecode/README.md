---
title: "WebDecode"
points: 50
tags: ["web"]
---

ページを探索してみると以下のようなHTMLが見つかる。
```
<section class="about" notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDJjZGNiNTl9">
   <h1>
    Try inspecting the page!! You might find it there
   </h1>
   <!-- .about-container -->
  </section>
```

base64デコードして終わり。

- `echo cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDJjZGNiNTl9 | base64 -d`