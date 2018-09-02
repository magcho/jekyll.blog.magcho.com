---
layout: post
title: MBAのSerriaをクリーンインストールした時の手順メモ
category: プログラミング
tag: mac
---
**自分用のメモ記事なので、参考程度にどうぞ。**

## 環境

* MacBookAir macOS Sierra 10.12.2
* homebrew 1.1.5
* homebrew-cask 1.1.5
* mas 1.3.1
* mackup 0.8.15

## 準備編

１.  必要なファイルを外部のストレージにコピー

* homeディレクトリをそのままコピーしてしまえばある程度は安心
* プッシュしていないGitプロジェクトはプロジェクトごとコピーしておけばブランチ構造ごとコピーできる
* VirtualBoxのエクスポートツールでバックアップしてもいいし、面倒ならフォルダごとコピーして*.vboxファイルをあとでインポートすればOK

２. Dropboxをインストールし同期できるように設定しておく

* インストール済みならばそれでOK

３. アプリの設定ファイルなどをバックアップ

```
$ brew install mackup
$ mackup backup
```

* エクスポートしたファイル群はDropboxの共有フォルダにあるので、同期しておく
* /Users/{ユーザー名}/Dropbox/Mackupが作られる

４. homebrwe, homebrew cask でコマンドでインストールしたアプリの一覧を取得する
     

```
$ cd ~/Dropbox/mac_backup
$ brew list >> brew_list.txt
$ brew cask list >> brew_cask_list.txt
```

* Docker は別で再インストールしたほうがいい(Docker tool box)

５. AppStoreでからインストールしたアプリの一覧を取得する

```
$ cd ~/Dropbox/mac_backup
$ brew install mas
$ mas list >> mas_list.txt 
```

６. インストール一覧からシェルスクリプトを作る

{% gist 5023c8d38d325b6578112807c1fd46b2 %}

[macを引っ越す時にアプリを一括インストールするスクリプト](https://gist.github.com/5023c8d38d325b6578112807c1fd46b2)

## リストア編

１.  xcodeをappstoreからインストール

２. xcodeを起動しxcode comand line toolをインストール

３. Homebrewをインストール

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

４. 1passwordをインストールする

```
$ brew cask install 1passwrod
```

* **caskは\[brew cask]とコマンドを打つと気を利かせてhomebrewがインストールしてくれるので、自分でやる必要はない**
* 自分はiPhoneとも同期しているのでicloud同期で設定

５. Dropboxをインストールしファイルを同期する

```
$ brew cask install dropbox
```

６. mackupをインストールし、復元

```
$ brew install mackup
$ mackup restore
```

７. 準備編(上記)で作ったシェルスクリプトを実行してbrew と cask系を一括インストール

```
$ cd /Users/{ユーザー名}/Dropbox/mac_backup
$ sudo sh brew-install-list.sh
```

８. atomエディタのアドオンと設定を復元

```
$ apm install atom-settings
[atom内のコマンドで（cmd+shift+p)] -> [sync-settings:restore]とタイプしEnter
```

* apmコマンドが使えない場合は一度atomを起動し \[Atom] -> \[Install Shell Commands]を押しapmが使えるようにする。 
* 復元中に、gistのリストとパッケージの最新バージョンが違ったりすると警告が出るので、各自判断で手動ならインストールできます。
* Settings > Packages を見るとatomの再起動が必要なパッケージはダウンロードされた時点で一時停止しているので、インストールを押してやればOKです。

９. Git系のセットアップ

* gitはsourcetree内蔵ではなく、自分で入れたものを使っているのでbrew でインストール後Sourcetreeの設定でシステムのGitに切り替えます
* GithubとBitbucketでログインする。２段階認証にどちらも対応してるのでアクセストークンをあれこれする必要はなく、IDとPWでログインして、携帯などで6桁の数字を確認して２段階認証しておきましょう
* OuathかBasicで認証したりSSHキーを認証したりしておく。

## 後記

アプリのインストール管理はbrew-fileとやらでできるらしいので、もっと早く知りたかった。それと、1passwordってappstoreからだと7,800円するけど、caskなら試用版をメール認証なしでインストールできるっていう発見。
