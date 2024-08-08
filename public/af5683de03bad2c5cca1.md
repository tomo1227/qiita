---
title: 【Go言語】range
tags:
  - Go
private: true
updated_at: '2024-08-06T21:09:12+09:00'
id: af5683de03bad2c5cca1
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

Goでは、doやwhileはなく、一般化されたforしか存在しない。
rangeは様々なデータ構造に対して、反復処理を行うのに便利な方法。インデックスや終了状態を扱う必要がない。しかし、rangeがどのように値を割り当てるかを知っていないと間違いを起こす原因となりかねない。

## rangeとは

rangeは以下のデータ構造に対して、反復処理を行える。

* 文字列
* 配列
* 配列へのポインタ
* スライス
* マップ
* 受信チャネル

[Go Playground](https://go.dev/play/p/JXGaeTIki7v)

```go
s := []string{"a", "b", "c"}
for i, v := range s {
    fmt.Printf("index=%d, value=%s\n", i, v)
}
```

```:実行結果
index=0, value=a
index=1, value=b
index=2, value=c
```

indexと要素の値をiとvに入れている。一般に、rangeは受信チャネルを除く各データ構造に対して、2つの値を生成するが、受信チャネルの場合は1つの要素(値)を生成する。

ローカル変数を使わないとコンパイルエラーになるので、代わりに次のようにブランク識別子を使ってインデックス変数を置き換えられる。

[Go Playground](https://go.dev/play/p/jMQQcr7iCys)

```go
s := []string{"a", "b", "c"}
for _, v := range s {
    fmt.Printf("value=%s\n", v)
}
```

```:実行結果
value=a
value=b
value=c
```

ブランク識別子によって、`v`に要素の値だけを代入できた。

値に興味がなければ以下のようにする。

[Go Playground](https://go.dev/play/p/TROb7LnPKRy)

```go
func main() {
	s := []string{"a", "b", "c"}
	for _ = range s {
		fmt.Println("a")
	}
}
```

:::note warn
`for _ := range s {}` はコンパイルエラーになる。`=`を使わなければならない。
:::

## 値のコピー

```go
type account struct {
    balance float32
}
```

以下は、account構造体のスライスを作成し、rangeで各要素を反復処理して、balanceを増やすコード。
[Go Playground](https://go.dev/play/p/iH-u9-hD5sz)

```go
type account struct {
	balance float32
}

func createAccounts() []account {
	return []account{
		{balance: 100.},
		{balance: 200.},
		{balance: 300.},
	}
}

func main() {
	accounts := createAccounts()
	for _, a := range accounts {
		a.balance += 1000
	}
	fmt.Println(accounts) // [{100} {200} {300}]
}
```

`[{1100} {1200} {1300}]`にはならない。その理由は、Goでは代入されるものは全てコピーであるからである。

* 構造体を返す関数の結果を代入するとその構造体のコピーが行われる
* ポインタを返す関数の結果を代入すると、メモリアドレスのコピーが行われる。(64bitアーキテクチャではアドレスは64bit長となる。)

rangeはデータ構造を反復処理するとき、各要素を値変数へコピーする。

この解決法としては、2つ存在する。

* スライスのインデックスを用いて要素にアクセスする
  * 従来のfor
  * range
* ポインタを使用する

### スライスのインデックスを使用する

#### range

[Go Playground](https://go.dev/play/p/m3k7sFDivdh)

```go
func main() {
	accounts := createAccounts()
	for i := range accounts {
		accounts[i].balance += 1000
	}
	fmt.Println(accounts) // [{1100} {1200} {1300}]
}
```

#### 普通のfor

[Go Playground](https://go.dev/play/p/62_kCfhZuHu)

```go
func main() {
	accounts := createAccounts()
	for i := 0; i < len(accounts); i++ {
		accounts[i].balance += 1000
	}
	fmt.Println(accounts) // [{1100} {1200} {1300}]
}
```

### ポインタを使用する

ポインタを
[Go Playground](https://go.dev/play/p/-NMk4njjyVZ)

```go
func main() {
	accountsPtr := createAccountsPtr()
	for _, a := range accountsPtr {
		a.balance += 1000
	}
	printAccountsPtr(accountsPtr)
}

func createAccountsPtr() []*account {
	return []*account{
		{balance: 100.},
		{balance: 200.},
		{balance: 300.},
	}
}

func printAccountsPtr(accounts []*account) {
	sb := strings.Builder{}
	sb.WriteString("[")
	s := make([]string, len(accounts))
	for i, account := range accounts {
		s[i] = fmt.Sprintf("{%.0f}", account.balance)
	}
	sb.WriteString(strings.Join(s, " "))
	sb.WriteString("]")
	fmt.Println(sb.String())
}
```

これは2つのデメリットが存在する。

* スライス型を変更する必要があるが、常に可能とは限らない
* 性能を重視する場合、ポインタのスライスを反復処理することは予測可能性にかけるため、CPUにとって効率が悪くなる可能性がある

## まとめ

* range での値要素はコピーである
* 上記の解決策としては、rangeや従来のforでindex経由で要素にアクセスする
