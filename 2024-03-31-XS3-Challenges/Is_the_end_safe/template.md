---
title: "Is the end safe?"
points: 50
tags: ["web"]
---

こちらもServer Side Uploadと構成は変わらず、アップロード用フォームとXSSをサーバー側で発火させるための機能がある。

今回はアップロード用の機能に以下のような制限が含まれる。

```javascript
if (contentType.endsWith('image/png')) return true;
if (contentType.endsWith('image/jpeg')) return true;
if (contentType.endsWith('image/jpg')) return true;
```

endsWithで制限しているので、Content-Typeに `text/html,image/png`を指定することでバイパス可能。