---
title: pipコマンド
tags:
  - Python
  - pip
private: true
updated_at: '2019-11-07T08:38:10+09:00'
id: d8e3427cffaa5ba2c67b
organization_url_name: null
slide: false
ignorePublish: false
---
#アップデートに関するコマンド

* pip自体のアップデート

```terminal
pip install -U pip
```

* パッケージのインストール

```terminal
pip install [package-name]
```

* パッケージのアップデート

```terminal
pip install -U [package-name]
```

* アップデートの必要なパッケージのリストの表示

```terminal
pip list -o
```

##pipのパッケージを一括アップデート

まずアップデートの必要なパッケージの名前をpip_update.txtに書き込む。

```
pip list -o | awk '{print $1}' > pip_update.txt
```

以下のpip_updater.shを作成。

```bash:pip_updater.sh
#!/bin/bash

while read line
do
  pip install -U $line
done < ./pip_update.txt
```

pip_updater.shを実行すると一括でアップデートされる。

```terminal
bash pip_updater.sh
```
