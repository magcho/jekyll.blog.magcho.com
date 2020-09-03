---
layout: post
title: pyenvのpythonでvscodeを使いたい
category: プログラミング
tag: vscode
---
macにpyenvを入れていてpythonを管理しているときにvscodeのpythonのlintツールを使いたいときにやった設定。

vscodeはシステムのpip コマンドを用いてpython-lintをインストールまで自動的にやってくれる。そこで初期状態のmacではpythonが入ってはいるもののバージョンがとても低いので付随してpipのバージョンも古かったり、そもそもコマンドが動作しないこともある。そこでpyenvを使って任意のバージョンのpythonをインストールすることになるだろう。

```
$ pyenv install {任意のバージョン}
```

でもこれではvscodeがpip
