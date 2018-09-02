---
layout: post
title: ArduinoでDMXを受ける
category: 舞台技術
tag: DMX512
---
DMXSerial使っただけ。この方法だとHardwareSerialを使うので、スケッチ書き込み時にTX,RXからピンを抜いておかないと書き込みエラーが出たりするので、組み込みで半田付けしてからスケッチ修正できない。ムムム

![null](/images/uploads/20180105190616.png)

{% gist 257ba101ae0ba998f83275564c8c5f6a %}

<https://qiita.com/loveandsheep/items/e1295ec9ce589>
