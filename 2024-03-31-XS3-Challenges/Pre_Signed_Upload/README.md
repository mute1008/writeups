---
title: "Pre Signed Upload"
points: 20
tags: ["web"]
---

基本構成はServer Side Uploadと同じ。

違いとしては、ファイルアップロードのためのPre Signed URL発行エンドポイントに制限があること。

画像ファイルのみが許可されているので、image/pngを指定してPre Signed URLを取得。

Pre Signed URLを使用したアップロード時にContent-Typeをtext/htmlにしてアップロードする。

アップロードしたファイルの表示時にtext/htmlとしてレスポンスされるので、そのURLをサーバーに投げてflag取得。