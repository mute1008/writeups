---
title: "memo-drive"
points: N/A
tags: ["web"]
---

コードを読むと、メモの作成と取得ができるアプリケーションであることがわかる。メモはファイルシステムに保存されていることがわかる。

フラグは`/memo/flag`にあるので、メモの取得機能を利用してフラグを読み取るのが目標であることがわかる。

メモの取得コード部分には以下のようなバリデーションが実装されている。

```python
if '&' in request.url.query or '.' in request.url.query or '.' in unquote(request.query_params[clientId]):
```

ファイルの取得パスの作成は以下のようになっていて、 filenameは攻撃者によって制御不可能となっている。

```python
path = './memo/' + "".join(request.query_params.keys()) + '/' + filename
```

そのため、上記のバリデーションを突破しつつ、任意のファイルを読み出すことが目標となる。

`&`の代わりに`;`が使えるので、複数キーを入力できるので、`;`を使って後ろにペイロードを入力することで終わり。