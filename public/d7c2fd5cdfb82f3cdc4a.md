---
title: 【Go言語】Go100Tips1~4をまとめてみた
tags:
  - Go
private: false
updated_at: '2024-07-09T00:36:12+09:00'
id: d7c2fd5cdfb82f3cdc4a
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

Go 100 Tipsの 1〜4 をまとめました。

スライドもあります。

https://speakerdeck.com/tomo1227/goyan-yu-100tips-no-dot-1-5-matome

また、Tip.3に関しては、別の記事にしました。

https://qiita.com/twrcd1227/items/6e7079d16fd27a7c7d6e

# シャドーイング

シャドーイングとは、異なるスコープで同じ名前の変数が使用されたときに、異なる変数として扱われること。ちなみに、同じスコープ内だと再宣言となり、コンパイラエラーとなります。

```go
func main() {
	n := 1
	if true {
		n := 999 // シャドウイングされる
		fmt.Println(n) // 999
	}
	fmt.Println(n) // 1
}
```

`:=`ではなく、代入演算子`=`を使うとシャドウイングされません。

```go
var n int
func main() {
	n := 1
	if true {
		n = 999
		fmt.Println(n) // 999
	}
	fmt.Println(n) // 999
}
```

気をつけないといけないのは、以下のように関数が複数返す際に、片方の変数が宣言されていなければ、`=`を使うとコンパイラエラーになるとき。エラーになるので、`:=`にしてしまう人がいるのですが、シャドーイングが発生してしまいます。

```go
func main() {
	n := 1
	if true {
		n, err := f() // 代入演算子 = にするとエラーが出る
		if err != nil {
			return
		}
		fmt.Println(n) // 999
	}
	fmt.Println(n) // 1
}

func f() (int, error) {
	return 999, nil
}
```

## 解決法1

一時変数格納してから、元の変数に入れる

```go
func main() {
	n := 1
	if true {
		tmp, err := f() // 代入演算子 = にするとエラーが出る
        n = tmp
		if err != nil {
			return
		}
		fmt.Println(n) // 999
	}
	fmt.Println(n) // 999
}

func f() (int, error) {
	return 999, nil
}
```

## 解決法2

前もってスコープ外で宣言していれば、代入演算子を使用できます。

```diff_go
func main() {
	n := 1
	var err error
	if true {
		n, err = f() // 代入演算子 = にするとエラーが出る
		if err != nil {
			return
		}
		fmt.Println(n) // 999
	}
	fmt.Println(n) // 999
}

func f() (int, error) {
	return 999, nil
}
```

# メンタルコードモデルを形成する

以下の3つを守ることで可読性を高め、メンタルコードモデルを形成します。

* ネストさせない　→ 早期returnで返す
* ハッピーパスを左側で揃える(elseは使わない)
* ノンハッピーパスは条件を反転して、ハッピーパスにする

以下はハッピーパスで揃えた例である。

```go
	if s1 == "" {
		return "", errors.New("s1 is empty")
	}
	if s2 == "" {
		return "", errors.New("s2 is empty")
	}
	if err != nil {
		return "", err
	}
	if len(s2) > max {
		return "", nil
	}
```

# getterとsetterの使い方

データのカプセル化 : オブジェクトの値や状態を隠すこと。
getterとsetterは、公開されていないオブジェクトのフィールドに対して、公開されたメソッドを提供することによって、カプセル化を可能にする手段。


```go
timer := time.NewTimer(time.Second) // 1秒後に発火
<-timer.C // 1秒後に通知
```

`C`は直接修正できるが、イベントを受け取れなくなる。

## getterとセッターを使用する利点

* フィールドの取得や設定に関連する動作をカプセル化し、後から新たな機能を追加できるようにする。(フィールドを検証したり、計算による値を返したり、フィールドへのアクセスをミューテックスで保護したりする)
* 内部表現を隠蔽することで、公開するものを柔軟に変更できる
* 実行時にプロパティが変更された場合のデバッグ用ブレイクポイントを提供し、デバッグを容易にする

また、前方互換性を保証するユースケースの可能性が予想されるときに有効である。
balanceというフィールドでgetter,setterを使用する場合、

* getterメソッドの名前は`Balance`にする(❌GetBalance)
* setterメソッドの名前は`SetBalance`にする

```go
currentBalance := customer.Balance() // getter
if currentBalance < 0 {
    customer.SetBalance(0) // setter
}
```

構造体のgetter,setterが何の価値もないのであれば、使うべきではない。

# 参考文献

1. [Japanese Version - 100 Go Mistakes and How to Avoid Them](https://100go.co/ja/)
