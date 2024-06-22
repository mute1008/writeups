---
title: "bb"
points: N/A
tags: ["web"]
---

起動するとこのソースコードが渡されるのみ。

```php
<?php
    error_reporting(0);

    function bye($s, $ptn){
        if(preg_match($ptn, $s)){
            return false;
        }
        return true;
    }

    foreach($_GET["env"] as $k=>$v){
        if(bye($k, "/=/i") && bye($v, "/[a-zA-Z]/i")) {
            putenv("{$k}={$v}");
        }
    }
    system("bash -c 'imdude'");
    
    foreach($_GET["env"] as $k=>$v){
        if(bye($k, "/=/i")) {
            putenv("{$k}");
        }
    }
    highlight_file(__FILE__);
?>
```

攻撃者は環境変数を制御でき、サーバーはbashをnon-interactive shellを起動するのみの処理となっている。

調査してみるとshellshockの記事がたくさん出てくるが、これは今回の環境においては刺さらない。

さらに調査を進めると、`BASH_ENV` に設定したものがnon-interactive shellの起動時に評価されるらしい。

これで任意コード実行にいけそうな気がするけど、`"/[a-zA-Z]/i"`という制限があるのでそのままペイロードを投げつけられない。

ANSI C Quotingという仕様があって、これをみると、8進数でコマンドを入力できることがわかる。

ref. https://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html

ペイロードをこれを使って変換することで終わり。