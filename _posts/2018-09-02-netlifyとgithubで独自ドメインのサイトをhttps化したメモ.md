---
layout: post
title: NetlifyとGithubで独自ドメインのサイトをHTTPS化したメモ
category: プログラミング
tag: Netlify
---
**情報が古く、執筆時のNetliftyのUI等が変わっています。ご注意を**

## 事の発端

IOS 11ｷﾀ━(ﾟ∀ﾟ)━! 　Safariからカメラの映像が取得できる！　これでアプリではなくブラウザからARできるようになった！と喜んでいたら、このカメラのapiはhttpsじゃないとセキュリティ的な問題で動かないのである。

Github pagesで独自ドメインでホスティングしているサイトをHTTPSにしたいけどお金はない。

すでに導入レポは検索すればいくつも見つかるものの、どうも日本語の情報が少ないのでメモすることにした。

プログラミング## 使ったもの

* Github
* Netlify
* お名前.com

## 手順

1. お名前.comでドメインを取得します。
2. Githubに公開するサイトのファイル一式をpushしておきます。

一度Github pagesで公開してみて問題がないことを確認しておきましょう。設定で有効化して、HTTPSにチェックを入れれば\`\``https://<username>.github.io/<repository name>/\`\``でアクセスできます。

2. Netlifyに登録します。
   <https://www.netlify.com/>
       Githubアカウントでログインし、先ほどpushしたリポジトリを選択し、デプロイしましょう。
3. Netlifyのsite nameを変更しましょう

デプロイをするとデフォルトでランダムな英単語が割り当てられるので、変更しなくても問題はないが変更しておきましょう。


\`\`\`General -> Setting -> Site details -> Change site name\`\`\` から設定します。

![null](/images/uploads/20171015014625.png)

4. カスタムドメインを設定しましょう

\`\`\`Domain management -> Domains -> Custom domain\`\`\` に取得しておいたドメインを設定します。
ここで、Netlifyはwww付きのドメインにすることを勧めてきます。DoS攻撃から守りやすいとか、ロードが早いとか色々恩恵があるらしいのですが、それよりもURLが短くなる方がいいなって思ったので、自分はwwwなしにしました。

![null](/images/uploads/20171015015607.png)

設定後、NetlifyのDNSを利用するので \`\`\` Use Netlify DNS \`\`\` を押します。４つURLが表示されると思います。これは次の作業で使うので覚えときましょう。

5. ドメインの設定をしましょう

今度はお名前.comでドメインの設定をしていきます。


ログイン後、\`\`\` ネームサーバーの設定 -> DNS関連機能の設定 \`\`\` にAレコードとCNAMEを追加します。

* Aレコードで 104.198.14.52を追加します。
* CNAMEにwwwをつけてNetlifyで設定したURLを追加します。`<site name>.netlify.com`です

![null](/images/uploads/20171015021014.png)

次に、DNSを変更します。


\`\`\` ネームサーバーの設定 -> ネームサーバーの変更 -> 他社のネームサーバーを利用\`\`\` に4でNetlifyにドメインを設定した時に出てきた４つのDNSのURLを設定します。人によってこのURLは異なるみたいです。

![null](/images/uploads/20171015021555.png)

設定が反映されると、独自ドメインにアクセスするとHPが表示されると思います。このままではまだHTTPです。いよいよ次でHTTPSになります。

6. NetlifyでSSLを有効化しましょう

Netlifyで \`\`\`Domain management -> Domains -> HTTPS -> SSL/TLS certificate\`\`\`で \`\`\`Verify DNS configration\`\`\`をクリックし\`\`\` Let's Encypt certificate\`\`\`をクリックです。

![null](/images/uploads/20171015022037.png)

お疲れ様でした、試しにhttps://でアクセスしてみましょう、表示されれば成功です。セキュリティーがどーたらと表示されてしまう場合はしばらく待ってれば反映されるっぽいです。


最後の仕上げとして、httpでアクセスがあった時にhttpsにリダイレクトさせたいので \`\`\`Force TLS connections\`\`\`を有効化して完了です。そんなの表示されてないよって時はリロードするとたぶん出てきます。




![](/images/uploads/20171015023118.png)

## コメント

ドメイン周りの設定は順番が大切なので、3回ぐらい失敗してサイトを消してやり直してを繰り返してしまった。勉強不足だ。
そんなこんなで、準備は整ったので、AR.jsとやらに挑戦します。
