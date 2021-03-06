---
layout: post
title: Docker volumeを調べたメモ
category: プログラミング
tag: Docker
---
完全にメモ書きになってるのでEvernoteにでも書けよって感じ。

Dockerはコンテナ型仮想化システムで、必要な時に必要なだけコンテナを作り、終わったら捨てる。いわば使い捨てだ。でも、消えて欲しくないデータを扱う事もあるだろう。そんな話。

自分はインフラエンジニアではないし、Dockerでホストの環境弄らずに開発環境作りたかったから始めたっていう理由

Dockerコンテナは使い捨てるもの→残したいファイルはコンテの外に保存しておこうっていう考えらしい。

## Docker の volumeにも色々ある
- docker runの-vオプション
    ```
    $ Docker run -v /hoge centos:latest
    ```
    centosのlatest(最新版)を起動するコマンドである。この時-vオプションを使ってコンテナ内に/hogeという名前のシンボリックリングを作成し、フォルダの実体は/var/lib/docker/volumes/{ランダムな英数字}/_data/にある。このフォルダはdockerがコンテナと連動させて管理しているため、docker コンテナを消せばこのフォルダも消えます。
__ 説明のため、コマンドは一部省略しています __

この時、内部では
    1. ホストに実体のフォルダを作る
    2. イメージからコンテナをつくり動かす
    3. ホストのフォルダへのリンクをコンテナに作る(上書く)

ってイメージらしい。

 - ホストのフォルダとコンテナのフォルダを同期する
   ```
    $ docker run -v {ホストのフォルダ(実体)}:{コンテナ内のリンク名} centos:latest
   ```
このコマンドでホストのフォルダへのシンボリックリンクをコンテナ作成時に貼る。こうすれば、コンテナの設定を変えてリビルドしてもファイルは消えずに残る。
また、ホストにファイルがあるのでホストでエディターを開き、コンテナで開発環境を揃え実行できるので環境のバージョンを変えるのもイメージを差し替えるだけだし、ソースをホスト側でgit管理する事もできる。

## 後記
dockerfileやdocker-composeにも同様の機能があるので、もう少し検証したい
