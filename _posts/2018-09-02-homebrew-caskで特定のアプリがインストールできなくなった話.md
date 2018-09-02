---
layout: post
title: Homebrew Caskで特定のアプリがインストールできなくなった話
category: プログラム
tag: Homebrew
---
## 状況説明
かなり前からhomebrew caskを使うと。

```
$ brew cask list
Warning: The default Caskroom location has moved to /usr/local/Caskroom.

Please migrate your Casks to the new location and delete /opt/homebrew-cask/Caskroom,
or if you would like to keep your Caskroom at /opt/homebrew-cask/Caskroom, add the
following to your HOMEBREW_CASK_OPTS:

  --caskroom=/opt/homebrew-cask/Caskroom

For more details on each of those options, see https://github.com/caskroom/homebrew-cask/issues/21913.
```

こんなのが出てきていた。「caskのバージョンアップでアプリが配置されるディレクトリの場所が変わったから前の場所に置いてたアプリ新しいとこに移動してくれ」っていう話だった気がする、筆者は<s>面倒だから</s>移動しなくても使えるからやっていなかったが、dockerがうまくインストールできないので今更対応した。
やることは簡単、コンソールに書いてある[URL](https://github.com/caskroom/homebrew-cask/issues/21913)に書いている通りに進めるだけだが、しかし今回Thunderbirdが再インストールに失敗した。

```
$ brew cask install thunderbird-ja
==> Downloading https://download.mozilla.org/?product=thunderbird-45.4.0&os=osx&
Already downloaded: /Users/{username}/Library/Caches/Homebrew/Cask/thunderbird-ja--45.4.0.0&os=osx&lang=ja-JP-mac
==> Verifying checksum for Cask thunderbird-ja
fatal error
```

エラー吐いてインストールが止まってしまう。

## 対応
上のコンソールで```Already download```ってなってるから以前ダウンロードしてあるインストーラを使おうとしているのだが

```
$ cd /Users/{username}/Library/Caches/Homebrew/Cask/
No such file or directory
```

となる。ディレクトリ自体が無いので、移動時にどっかいった可能性が高い。そこで新しいファイルをダウンロードすればいいと思い```$ brew cask install --force thunderbird-ja```としたもののやはり```Already download```となりダウンロードしてくれない。そこで最新バージョンではなくBetaを一旦インストールする。そしてキャッシュを消し、最新バージョンへアップグレードする。とうまくいった。
事前にcask versionsがインストールしてある場合は２つ目の$から始めてもらえばいいかと。
```
$ brew tap caskroom/versions
==> Tapping caskroom/versions
Cloning into '/usr/local/Homebrew/Library/Taps/caskroom/homebrew-versions'...
remote: Counting objects: 209, done.
remote: Compressing objects: 100% (207/207), done.
remote: Total 209 (delta 22), reused 15 (delta 0), pack-reused 0
Receiving objects: 100% (209/209), 76.51 KiB | 0 bytes/s, done.
Resolving deltas: 100% (22/22), done.
Checking connectivity... done.
Tapped 0 formulae (226 files, 276.3K)

$ brew cask install Thunderbird-beta
==> Downloading https://ftp.mozilla.org/pub/thunderbird/releases/50.0b2/mac/en-U
==> Verifying checksum for Cask thunderbird-beta
==> Moving App 'Thunderbird.app' to '/Applications/Thunderbird.app'
🍺  thunderbird-beta was successfully installed!

$ brew cask uninstall thunderbird-beta
==> Removing App: '/Applications/Thunderbird.app'

$ brew cask cleanup
==> Removing cached downloads
/Users/{username}/Library/Caches/Homebrew/Cask/thunderbird-beta--50.0b2.dmg
==> This operation has freed approximately 77.8M of disk space.

$ brew cask install thunderbird
==> Downloading https://ftp.mozilla.org/pub/thunderbird/releases/45.5.1/mac/ja-J
==> Verifying checksum for Cask thunderbird
==> Moving App 'Thunderbird.app' to '/Applications/Thunderbird.app'
🍺  thunderbird was successfully installed!
```

## まとめ
brew caskのキャッシュ周りのことを理解しないで使っているので本当はもっといい解決策があるかもしれないが、今回はこれで収まったのでよしとする。
homebrewはオープンソースプロジェクトなのでGithubでどんなことやってるかは確認できるが、仕様変更なんかがあった時にエラー吐いてググって変更あったんだーってなることが多いので、もっと自己防衛のためにもアンテナ張ってなきゃな〜って思った。
