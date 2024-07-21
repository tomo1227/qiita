---
title: 【Go言語】パニック
tags:
  - Go
private: false
updated_at: '2024-07-17T01:10:18+09:00'
id: fb47a9d483e4fa86bcee
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

Goにはtry/catchがないが、panicやrecoverをむやみにつかってはいけない。一般的には、error型で返すのが好ましい。

# panicとは

[Go Playground](https://go.dev/play/p/AQKptHYU6SO)

```go
func main() {
	fmt.Println("a")
	panic("foo")
	fmt.Println("b")
}
```

パニックが発生すると、現在の goroutine がリターンするか、recover でパニックが捕捉されるまで、パニックはコールスタックをさかのぼる。

以下はrecoverで捕捉する例である。

```go
func main() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("recover", r)
		}
	}()

	f() // パニックが発生するfをコールする
}

func f() {
	fmt.Println("a")
	panic("foo")
	fmt.Println("b")
}
```

`recover`で捕捉されると、goroutineは終了しない。
`recover`を呼び出すのは、deferに与えられた関数内だけで有効。recover関数はnilを返し、何も効果はない

# パニックを発生させるべきとき

* 純粋なプログラマーエラー
* アプリケーションが必須依存関係を必要とするにも関わらず、初期化に失敗するとき

## 純粋なプログラマーエラー

net/httpパッケージのWriteHeaderでは、ステータスコードが有効化どうかを検査するために`checkWriteHeaderCode()`が呼ばれる。

```go
func checkWriteHeaderCode(code int) {
	if code < 100 || code > 999 {
		panic(fmt.Sprintf("invalid WriteHeader code %v", code))
	}
}
```

ステータスコードが不正な値をとるとパニックを起こす。

その他にも`database/sql`パッケージのRegister()が０位に挙げられる。

## アプリケーションが必須の依存関係の作成に失敗するとき

新たな顧客アカウントを作成するためのサービスを公開するとき、提供された電子メールアドレスの検証を正規表現を用いて行う。

regexpパッケージには、文字列から正規表現を作成する関数として、以下の２つがある。

* Compile : *regexp.Regexpとエラーを返す
* MustCompile : エラーが発生したらパニックを発生させる

# まとめ

ほとんどの場合は、最後の戻り値として、error型を返すべきである。

# 参考文献

1. [100 Go Mistakes and How to Avoid Them](https://100go.co/ja/)
