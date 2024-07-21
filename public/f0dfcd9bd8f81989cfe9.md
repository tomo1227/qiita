---
title: MIPSアセンブリ言語入門3
tags:
  - C
  - アセンブリ言語
  - MIPS
private: false
updated_at: '2021-05-13T16:02:55+09:00'
id: f0dfcd9bd8f81989cfe9
organization_url_name: null
slide: false
ignorePublish: false
---
#for文とwhile文

`for(sta1; expr; sta2) sta3`はwhile文と等価で
これをMIPSで書くと

```
sta1;
while (expr) {
    sta3;
    sta2;
}
```

#switch文

```
switch(expr) {
    case v_1: sta_1; break;
    case v_2: sta_2; break;
        ・・・
    case v_n: sta_n; break;
    defalut : sta_d;
}
sta_next
```

上記のようなswitch文は以下のような文と等価である。

```sum
    tmp=expr;
    if(tmp==v_1) goto l_case_1;
    if(tmp==v_2) goto l_case_2;
       ・・・
    if(tmp==v_n) goto l_case_n;
    goto l_case_d;
l_case_1: sta_1; goto l_end_sw;
l_case_2: sta_2; goto l_end_sw;
       ・・・
l_case_n: sta_n; goto l_end_sw;
l_case_d: sta_d; goto l_end_sw;
l_end_sw: sta_next;
```

これをMIPSで翻訳すればよい

#例

```C:sum.c
int s;
int i;

int main () {
    s = 0;
    i = 1;
    while (i <= 100) {
    	s = s + i;
    	i = i + 1;
    }
    return 0;
}
```
これをMIPSで書くと

```:sum.asm
# データ領域開始の宣言
        .data

# int s
        .globl s
s:      .word  0

# int i
        .globl i
i:      .word  0

# テキスト領域開始の宣言
        .text

# int main ()
        .globl main
main:

# s = 0
    la    $t0, s
    sw    $zero, 0($t0)

# i = 1
    li    $s0, 1
    la    $t0, i
    sw    $s0, 0($t0)

# while (i <= 100)
_main_1:
    la    $t0, i
    lw    $s0, 0($t0)
    li    $t1, 100
    sle   $t0, $s0, $t1
    beqz  $t0, _main_2

# s = s + i
    la    $t0, s
    lw    $s0, 0($t0)
    la    $t1, i
    lw    $s1, 0($t1)
    add   $s0, $s0, $s1
    sw    $s0, 0($t0)

# i = i + 1
    la    $t0, i
    lw    $s0, 0($t0)
    addi  $s0, $s0, 1
    sw    $s0, 0($t0) 

# }
    b    _main_1

# return 0
_main_2:
    li    $v0, 0
    jr    $ra
```

#最適化

上記の例の`sum.asm`は色々無駄があり、最適化すると以下のようになる。

```
# $t1に変数s, $t2に変数i,$t3に定数100を入れる
_main_1:
    sle    $t0, $t1, $t3
    beqz   $t0, _main_2

# s=s+i
    add    $t1, $t1, $t2

# i=i+1
    addi   $t2, $t2, 1

# while loopの最後
    b _main_1
_main_2:
    la    $t0, s
    sw    $t1, 0($t0)
    la    $t0, i
    sw    $t2, 0($t0)
```


#関連記事

https://qiita.com/twrcd1227/items/91f048de69bf92355868

https://qiita.com/twrcd1227/items/405cf58e8c9fcd3447ac

https://qiita.com/twrcd1227/items/9e4829f46d51172fed21
