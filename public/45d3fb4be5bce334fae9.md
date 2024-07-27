---
title: 【Go言語】文字列とrune
tags:
  - Go
private: false
updated_at: '2024-07-26T07:56:02+09:00'
id: 45d3fb4be5bce334fae9
organization_url_name: null
slide: false
ignorePublish: false
---
# 文字列

Goの文字列はイミュータブルなデータ構造

* イミュータブルなバイト列へのポインタ
* そのバイト列内のバイトの総数

# rune

* 文字セット : 文字の集合。Unicodeの文字セットは $ 2^{21} $個ある
* エンコーディング : 文字のリストを2進数で翻訳したもの。UTF-8は全てのUnicode文字を可変なバイト数(1~4byte)でエンコーディングできるエンコーディング規格

code pointとは、unicodeで1つの値で表される項目を指す。「漢」は、U+6f22コードポイントで識別される。UTF-8では、「漢」は0xE6, 0xBC, 0xACの3バイトでエンコードされる。runeはUnicodeのコードポイントである。
Unicodeのコードポイントは21ビットで、コードポイントを保持するためのruneはint32のエイリアスとなる。

```go
type rune = int 32
```

以下は文字リテラル(文字列定数)を代入している。

```go
s := "hello"
```

Goでは、ソースコードはUTF-8でエンコードされる。文字列リテラルは全てUTF-8でバイト列にエンコードされる。しかし、文字列は任意のバイト列であり、UTF-8であるわけではない。文字リテラルで初期化されていない変数を操作する場合(ファイルシステムから読み込む等)、必ずしもUTF-8エンコーディングを使っているとは限らない。

:::note warn
[golang.org/x](https://pkg.go.dev/golang.org/x)は、標準ライブラリに対する拡張を提供するリポジトリで、UTF-16とUTF-32を扱うたのパッケージを含む
:::

1文字は常に1バイトにエンコードされるわけではない。

```go
s := "hello"
fmt.Println(len(s)) // 5
s := "漢"
fmt.Println(len(s)) // 3
```

"漢"という文字は、`0xE6,0xBC, 0xA2`という2バイトで構成される。

```go
func main() {
	s := string([]byte{0xE6, 0xBC, 0xA2})
	fmt.Println(s) // 漢
}
```

#

```go
func main() {
	s := "hêllo"
	for i := range s {
		fmt.Printf("position %d: %c\n", i, s[i])
	}
	fmt.Printf("len=%d\n", len(s))
}
```

これを実行すると、以下のようになる。

```txt:実行結果
position 0: h
position 1: Ã
position 3: l
position 4: l
position 5: o
len=6
```

特殊文字`ê`をUTF-8にエンコードすると、`c3 aa`となる。
また、`c3`は`Ã`となる。

この解決法としては、`range`を使用する。

```go
func main() {
	s := "hêllo"
	for i, r := range s {
		fmt.Printf("position %d: %c\n", i, r)
	}
}
```

実行すると、ちゃんと`hêllo`が出力される。

```txt:実行結果
position 0: h
position 1: ê
position 3: l
position 4: l
position 5: o
```

ただし、indexはやはり、2が飛ばされている。

もう一つの方法として、以下のように文字列をスライスにする方法がある。

```go
func main() {
	s := "hêllo"
	runes := []rune(s)
	for i, r := range runes {
		fmt.Printf("position %d: %c\n", i, r)
	}
}
```

これを実行すると、

```txt:実行結果
position 0: h
position 1: ê
position 2: l
position 3: l
position 4: o
```

ただし、この解決法は、実行時のオーバーヘッドが発生する。文字列をルーンのスライスに変換するには、追加のスライスを割り当て、バイトをルーンに変換しなければならなく、時間計算量は`O(n)`となる。なので、できる限り最初の解決法で行う方が良い。

indexでアクセスしたい場合は、以下のようにする。

```go
func main() {
	s := "hêllo"
	r := []rune(s)[4]
	fmt.Printf("%c\n", r) // o
}
```

# スライド

<script defer class="speakerdeck-embed" data-id="52b0d477d76f4bbc93c81c428a4afc80" data-ratio="1.7777777777777777" src="//speakerdeck.com/assets/embed.js"></script>

# 参考文献

1. [Japanese Version - 100 Go Mistakes and How to Avoid Them](https://100go.co/ja/)
