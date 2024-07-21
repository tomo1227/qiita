---
title: 【超難問】絶対に解けないJavaScriptクイズ8選
tags:
  - JavaScript
private: false
updated_at: '2024-06-29T21:36:25+09:00'
id: a64c3f22da46ff2c0fbd
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

以下のクイズのコードの実行結果をそれぞれ考えてみてください。
一番最後にスライドもあります。

## Q1

```js
console.log(018 - 015);
```

<details><summary>解答</summary>

```txt:実行結果
5
```

</details>

<details><summary>解説</summary>

JavaScript では、先頭に `0` が付いた数字は8進数とみなされます。
しかし、`018`は無効な8進数なので、10進数とみなされる。`015`は8進数となり、13となります。
ゆえに、`018-015 = 18-13 = 5`となります。

</details>

## Q2

```js
const isTrue = true == []; 
const isFalse = true == ![]; 

console.log ( isTrue + isFalse);
```

<details><summary>解答</summary>

```txt:実行結果
0
```

</details>

<details><summary>解説</summary>

まず、1行目を解説します。`true`と`[]`は型が違うので、数値へと変換されます。`Number(true)`と`Number([])`が最初に評価されて、それぞれ`1`、`0`となります。`1==0`となるので `const isTrue = false`となります。

2行目も同様かと思うのですが、`true`と`![]`は両方ともブール値なので、数字に変換されません。また、`[]`は*true*と解釈されるので、`![]`はfalseになります。
ゆえに、`const isFalse = true == false`となり、`const isFalse = false`となります。

以上から最後の行`console.log ( isTrue + isFalse);`は`console.log ( false + false);`となる。 *false*が数値に変化され、`console.log (0 + 0);` 
したがって、`0`が答えとなる。
 
</details>

## Q3

```js
console.log(3 > 2 > 1);
```

<details><summary>解答</summary>

```txt:実行結果
false
```

</details>

<details><summary>解説</summary>

まず、`3 > 2`が*true*と評価されます。
次に`true > 1`ですが、`>`演算子の場合は両方数値型に変換されます。

`Number(true) > Number(1)` => `1 > 1`となるので答えは*false*になります。

</details>

## Q4

```js
console.log(typeof typeof 1);
```

<details><summary>解答</summary>

```txt:実行結果
string
```

</details>

<details><summary>解説</summary>

最初に`typeof 1`が評価され、`number`となります。
`typeof 'number'`は string となるので、string が出力されます。

</details>

## Q5

```js
console.log(('b' + 'a' + + 'a' + 'a').toLowerCase());
```

<details><summary>解答</summary>

```txt:実行結果
banana
```

</details>

<details><summary>解説</summary>

`'ba' + + 'a'`は `'ba' + NaN`になります。
`+ 'a'` の `+` はプレフィックス演算子となり、`Number('a')`と等しいです。
ゆえに以下のように評価されます。

```txt
'b' + 'a' -> 'ba'
'ba' + + 'a' -> 'ba' + NaN -> 'baNaN'
'baNaN' + 'a' -> 'baNaNa'
'baNaNa'.toLowerCase() -> 'banana'
```

</details>

## Q6

```js
console.log(typeof NaN);
```

<details><summary>解答</summary>

```txt:実行結果
number
```

</details>

<details><summary>解説</summary>

NaNの型は **number** (数値型) です。

</details>

## Q7

```js
console.log(0.1 + 0.2 == 0.3);
```

<details><summary>解答</summary>

```txt:実行結果
false
```

</details>

<details><summary>解説</summary>

`0.1 + 0.2`は **0.30000000000000004** となります。
なので、`console.log(0.30000000000000004  == 0.3);`　となり、答えは *false*となります。

</details>

## Q8

```js
const numbers = [33, 2, 8];
numbers.sort();
console.log(numbers[1])
```

<details><summary>解答</summary>

```txt:実行結果
33
```

</details>

<details><summary>解説</summary>

JavaScript では以下のような配列も可能である。

```js
const array = ["1", true, 55, 1.421, "foo", {}];
```

`Array.prototype.sort`では、配列を一度stringにしてからソートする。

上記の問題の場合だと、ソート時に`["33", "2", "8"];`とみなされる。
string だと 2 → 3 → 8 の順になるので`numbers.sort()`は`["2", "33", "8"]`となる。よって、答えは33となる。

</details>

## スライド

<script defer class="speakerdeck-embed" data-id="c70ce9c4d6a84c6aa72ebd25ccefa486" data-ratio="1.7777777777777777" src="//speakerdeck.com/assets/embed.js"></script>

## 元ネタ

https://javascriptquiz.com/

さて8問中何問解けましたか？
もし全問解けたらコメントください！(何もないけど)
