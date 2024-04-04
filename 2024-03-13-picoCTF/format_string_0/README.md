---
title: "format string 0"
points: 50
tags: ["pwn"]
---

配布されているコードを見ると、sigsegvにハンドラーが用意されているので、とりあえずクラッシュさせるのが目的だとわかる。

```c
void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}
```

この辺りのコードを読んでみると、%sを含む選択肢が許可されているので、これを選んだらクラッシュしてハンドラ動いた。

```c
char choice2[BUFSIZE];
scanf("%s", choice2);
char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
if (!on_menu(choice2, menu2, 3)) {
    printf("%s", "There is no such burger yet!\n");
    fflush(stdout);
} else {
    printf(choice2);
    fflush(stdout);
}
```
