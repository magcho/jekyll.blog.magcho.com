---
layout: post
title: Dockerで立ち上げたwebサーバでmod_rewriteしたかった話
category: プログラミング
tag: Docker
---
## 内容
DockerHubの公式のPHPイメージを使っていて、mod_rewiteしたい人向けのお話です。結論だけ知りたい人は読み飛ばしてね。

## 事の発端
僕自身が立ち上げたwebサービスではもともとURLが```http://example.com/index.php?cmd=view&page=123```という感じでとてもダサかったので、かっこよくしたかった。WordPressなどではパーマリンクと呼ばれURLがイカした感じになっているので、僕も方法をパクった。
調べるとapache側の設定でmod_rewriteを使うとアクセス時にURLを記述したルールに基づいて置換してくれるらしい。


僕のwebサービスではアクセスを全てindex.phpで受け、getパラメータで動作を変化させている、pukiwikiとか？の設計をパクったはず。動作ルールは以下


```example.com/index.php?cmd=[動作モード]&page=[記事のNo.]```

- cmd
  - view 記事の表示、pageで記事を指定
  - edit 記事の編集、pageで記事を指定
  - category カテゴリーごとに記事一覧を表示する　　などなど

これを```example.com/[動作モード]/[記事のNo.]```にしたかった。
さらにviewの時には```example.com/[記事のNo.]```にしたい。

このルールでURLを置換させるには.htaccessに
```
RewriteEngine On
RewriteBase /
RewriteRule ^([0-9]+) index.php?cmd=view&page=$1 [L]
RewriteRule ^edit/([0-9]+) index.php?cmd=edit&page=$1 [L]
RewriteRule ^edit index.php?cmd=edit [L]
RewriteRule ^category index.php?cmd=category [L]
```
のように記述する、よくある正規表現と似ているので特に迷うことはないだろう。

いざ、Dockerで立てた開発環境に置いてみると、500エラー。なんで！！
しばらく悩むも解決せず、諦めて本番環境に置いてみると動いた！なんで！！


理由は単純だった、使っているシステムが違った。どちらもapacheなのは違いないが、本番はCentOSで構成されており、開発環境のDockerの公式imageのphpはdebianで構成されていた。

[https://github.com/docker-library/php/blob/2630167f7e69394bdd91f240443a0a521fd7872d/7.0/apache/Dockerfile:embed:cite]


debian系のapacheでmod_rewriteを有効化するにはコンテナに入ってコマンドを打てばいいらしい。
```
$ docker exec -it {php_container_name} bash
$ a2enmod rewrite
```
そして/etc/apache2/sites-available/000-default.confの<VirtualHost *:80>の中に
```
    <Directory /var/www/html>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
```
を追加して再起動すればOK!


でも、せっかくdocker-compose使ってるからいちいちコマンド打つの面倒だよね。ってことで

## 結論


**Dockerfile**
```
FROM php:7.0-apache
RUN  a2enmod rewrite
```

**docker-compse.yml**
```yml
php-7.0:
  build: .
  ports:
    - "80:80"
  volumes:
    - "../:/var/www/html"
    - "./000-default.conf:/etc/apache2/sites-available/000-default.conf"
```

**000-default.conf**
```
<VirtualHost *:80>
    <Directory /var/www/html>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
~~~~~~~~~~~~~~以下略~~~~~~~~~~~~~~~~
```

この３ファイルを同じディレクトリに入れ```docker-compose up```でOK。


ちなみに、mysqlも使いたいよって人は

**Dockerfile**
```
FROM php:7.0-apache
RUN apt-get update && \
  docker-php-ext-install pdo_mysql mysqli mbstring && \
  a2enmod rewrite
```
こうすれば良いです。
