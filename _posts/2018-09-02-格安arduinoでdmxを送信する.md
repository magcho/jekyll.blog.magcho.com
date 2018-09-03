---
layout: post
title: 格安ArduinoでDMXを送信する
category: 電子工作
tag: DMX512
---


## 部品

* Arduino UNO 互換品
* RS485モジュール
* XLRコネクター

## 配線

![](/images/uploads/20180101214426.png)

## スケッチ

[DmxSimple](https://github.com/PaulStoffregen/DmxSimple)をとりあえず書き込んでサンプルを動かしてみた。
このライブラリデフォルトのPinアサインは上の図の通りだが、コード内で別途指定すれば変更できるっぽい。

**SerialToDmx**はPCからのシリアル画面から`{ch番号}c{ゲージ}w`って感じで送ってやれば送信できる、5chに255を出力すると`5c255w`と送信すればいいらしい。

**FadeUp**はPinアサインがデフォルトから変更されているので、

```
// DmxSimple.usePin(3);
```

しとけばとりあえず動いた。
