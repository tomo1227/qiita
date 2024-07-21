---
title: 次世代エディタ6選
tags:
  - editor
  - IDE
  - まとめ
  - エディタ
private: false
updated_at: '2024-07-12T16:56:00+09:00'
id: 4faccd04f0f19f3fdb5e
organization_url_name: null
slide: false
ignorePublish: false
---
# Cursor

個人的に一番勢いのあるIDE
ただし、Cursorでコードを書くと、サーバーに保管されてしまうので、業務ではPrivacy Modeにする必要がある。

https://www.cursor.com

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/532323ee-7244-4c6d-b9a5-1a3460f811b6.png)

|  | Free | Pro | Business |
|:-:|:-:|:-:|:-:|
| 月契約  | 無料  | $20  | $40  |
| 年契約  | 無料  | $192(月\\$16)  | $384(月\\$32)  |
| cursor-small  | 200回  | 無制限  | 無制限  |
| GPT-4（低速）  | 50回  | 無制限  | 無制限  |
| GPT-4（高速）  | ❌  | 500回  | 500回  |
| copilot++  | 2000回  | 無制限  | 無制限  |
| Claude Opus  | ❌  | 10  | 10  |
| copilot++  | 2000回  | 無制限  | 無制限  |
|強制プライバシーモード|❌|❌|⭕️|
|管理者使用ダッシュボード|❌|❌|⭕️|
|ゼロ・リテンション・ポリシー|❌|❌|⭕️|

<details><summary> インストール方法</summary>

* https://www.cursor.com/ からダウンロードしてください。

Macの人はbrewでも可能です。

```
brew install --cask cursor
```

</details>

<details><summary> 使用方法</summary>

以下に詳しく書かれていました。

* [Cursorの使い方｜サクッと始めるAIコードエディタ【Cursor / VS Code / ChatGPT】](https://zenn.dev/umi_mori/books/ai-code-editor-cursor/viewer/how_to_use)

</details>

# Replit

https://replit.com/

ブラウザで利用できるオンラインIDE。色々なテンプレートから始められるので、初心者にはおすすめ。
また、Replit AIというAIが搭載されている。AIには、BasicとAdvanceがある。

|  | Starter | Replit Core | Teams | Enterprise |
|:-:|:-:|:-:|:-:|:-:|
| 料金  | $0  | $10  | $33  | 未定  |
| AI  | △  | ⚪︎  | ⚪︎  | ⚪︎  |
| コード補完  | △  | ⚪︎  | ⚪︎  | ⚪︎  |
|  コード生成 | △  | ⚪︎  | ⚪︎  | ⚪︎  |
| AIデバッガー  | △  | ⚪︎  | ⚪︎  | ⚪︎  |
| Accountストレージ  | 10GiB  | 50GiB  |  250GiB |  カスタム |

* ⚪︎ : 無制限のBasic、限度ありのAdvance
* △ : 限度ありのBasic

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/ef683486-c83f-ebce-ce21-8f89f40c2956.png)

# Zed

https://zed.dev/

Atomの製作者達によって作成されたOSSのRust製エディタ。現状はMacOSのみで使用可能。そのほかのOSも現在対応中。パフォーマンスを重視しているので軽い。AIは搭載していないが、Copilotを使用することは可能。

Github : [zed-industries/zed](https://github.com/zed-industries/zed/tree/main?tab=readme-ov-file)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/5e213fbc-f42b-ea01-8b1e-e0965f9fddd6.png)

<details><summary> インストール方法</summary>

https://zed.dev/download からインストールしてください。(MacOSのみ)

</details>

<details><summary> 使用方法</summary>

以下に詳しく書かれていました。

* [思考のスピードでコードを書け！！Rust製の次世代エディタ「Zed」](https://zenn.dev/smartcamp/articles/c421e752119cee)

</details>

# Positron

https://github.com/posit-dev/positron

VSCodeをベースにした次世代データサイエンスIDE。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/f515ac2d-f965-7c6a-745b-a9555f44d1cb.png)

<details><summary> インストール方法</summary>

https://github.com/posit-dev/positron/releases からインストールしてください。

</details>

<details><summary> 使用方法</summary>

[Positron 最速入門](https://zenn.dev/yutannihilation/articles/39c881445ca1b7) に詳しく書かれていました。

</details>

# Lapce

https://lapce.dev/

Rust製のエディタ。OSはLinux, Windows, Macに対応している。軽い。
Googleが作成した[Xi-editor](https://github.com/xi-editor/xi-editor)の後継エディタとなる。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/93e38903-2163-28d3-def8-5eff9742484a.png)

<details><summary> インストール方法</summary>

https://lapce.dev/#downloads-all  からインストールできます。

</details>

# Helix

https://helix-editor.com/

Rust製のCLIエディタ。MacもWindowsもLinuxも対応しています。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/570db90e-f076-0c97-00aa-05cb3287b444.png)

<details><summary> インストール方法</summary>

Macの場合は、brewでインストールできます。その他は [Installation](https://docs.helix-editor.com/install.html)を参考にしてください。

```sh
brew install helix
```

-Vでバージョン確認できます。

```shell-session
$ hx -V
helix 24.3 (2cadec0b)
```

</details>

<details><summary> 使用方法</summary>

`hx` コマンドでファイルやディレクトリを開けます。

テーマのもたくさんあります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/f2473dc2-c3ae-3ef3-e6b5-0c2f87fca0c0.png)

`:config_open`で設定ファイルを開けます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/156096/a51afdfc-9f33-d25c-2287-b664fea3952d.png)

また、`hx —health　{言語}`で使用できるLSPの確認ができます。

個人的には、File explorer機能使ってみたいと思いました。(参考 : [最新のHelixでFile explorerを使いたい](https://zenn.dev/fraim/articles/migrate-helix-file-explorer))

</details>
