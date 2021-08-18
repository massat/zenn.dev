---
title: '"Symfony5: The Fast Track"の作業環境を anyenv + phpenv で作って失敗したメモ'
emoji: "😇"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["anyenv", "phpenv", "php", "symfony"]
published: true
---

## TL;DR

Symfony5 の入門として [The Fast Track \- 基礎から最速で学ぶ Symfony 5 入門 \(Symfony Docs\)](https://symfony.com/doc/current/the-fast-track/ja/index.html) の環境を手元に PHP 8 で作ろうとしたが `anyenv` + `phpenv` 環境だとうまくいかなかった。

https://symfony.com/doc/current/the-fast-track/ja/index.html

## はじめに

Symfony5 に興味を持ったので、とっかかりとして [The Fast Track \- 基礎から最速で学ぶ Symfony 5 入門 \(Symfony Docs\)](https://symfony.com/doc/current/the-fast-track/ja/index.html) を追ってみることにした。

まずは [ステップ 1: 作業環境を確認する \(Symfony Docs\)](https://symfony.com/doc/current/the-fast-track/ja/1-tools.html) にしたがって、手元の Macbook に環境を作ってみた。

## 作業

### PHP

[こちら](install-php8-with-phpenv)の状態からスタート。

https://zenn.dev/massat/articles/install-php8-with-phpenv

`phpenv` でローカルでは `8.0.9` を使うように設定。

```
➜  symfony5-the-fast-track git:(main) ✗ phpenv local 8.0.9
8.0.9
```

> PHP は 8.0 バージョンを使い、下記の PHP 拡張 がインストール済みになっていることを確認してください。もし入っていなければ今インストールしてください： intl, pdo_pgsql, xsl, amqp, gd, openssl, sodium 。オプションで下記をインストールしてください： redis, curl , zip 。

とあるので module を比べてみる。

```
➜  symfony5-the-fast-track git:(main) ✗ php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dba
dom
exif
fileinfo
filter
ftp
gd
hash
iconv
json
ldap
libxml
mbstring
mysqli
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
Reflection
session
shmop
SimpleXML
snmp
soap
sockets
SPL
sqlite3
standard
sysvmsg
sysvsem
sysvshm
tidy
tokenizer
wddx
xml
xmlreader
xmlrpc
xmlwriter
xsl
zlib

[Zend Modules]

```

足りないのは

- intl
- pdo-pgsql
- amqp
- sodium
- Redis
- zip

の 6 つ。

#### pecl

`redis` は `pecl` で入れることを記憶していたが、[こちら](install-php8-with-phpenv)の状態では入っていなかった。 `--with-pear` オプションをつけて再度インストールした。

#### Redis

`pecl` でインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ pecl install redis

(中略)

Build process completed successfully
Installing '/Users/massat/.anyenv/envs/phpenv/versions/8.0.9/lib/php/extensions/no-debug-non-zts-20200930/redis.so'
install ok: channel://pecl.php.net/redis-5.3.4
configuration option "php_ini" is not set to php.ini location
You should add "extension=redis.so" to php.ini

➜  symfony5-the-fast-track git:(main) ✗ echo "extension=redis.so" >  ~/.anyenv/envs/phpenv/versions/8.0.9/etc/conf.d/redis.ini
➜  symfony5-the-fast-track git:(main) ✗ php -m | grep redis
redis
```

#### intl

`--enable-intl` オプションを追加してインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ PHP_BUILD_CONFIGURE_OPTS="--with-pear --enable-intl --with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9

(中略)

➜  symfony5-the-fast-track git:(main) ✗ php -m | grep intl
intl
```

#### pdo-pgsql

Homebrew で `postgresql` をインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ brew install postgresql

(略)
```

`--with-pdo-pgsql` オプションを追加してインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ PHP_BUILD_CONFIGURE_OPTS="--with-pear --enable-intl --with-pdo-pgsql=$(brew --prefix postgresql) --with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9

(中略)

➜  symfony5-the-fast-track git:(main) ✗ php -m | grep pgsql
pdo_pgsql
```

#### amqp

Homebrew で `rabbitmq-c` をインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ brew install rabbitmq-c
```

pecl で `amqp` をインストール。amqp は作業段階では PHP 8 をサポートしてるバージョンが `1.11.0beta` しかないので、これを指定する。

```
➜  symfony5-the-fast-track git:(main) ✗ pecl install amqp-1.11.0beta

(中略)

Set the path to librabbitmq install prefix [autodetect] : /usr/local/opt/rabbitmq-c

(中略)

➜  symfony5-the-fast-track git:(main) ✗ echo "extension=amqp.so" >  ~/.anyenv/envs/phpenv/versions/8.0.9/etc/conf.d/amqp.ini
➜  symfony5-the-fast-track git:(main) ✗ php -m | grep amqp
amqp
```

#### sodium

Homebrew で `libsodium` をインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ brew install libsodium
```

`--with-sodium` オプションを追加してインストール。

```
➜  symfony5-the-fast-track git:(main) ✗ PHP_BUILD_CONFIGURE_OPTS="--with-pear --enable-intl --with-pdo-pgsql=$(brew --prefix postgresql) --with-sodium=$(brew --prefix libsodium) --with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9

(中略)

➜  symfony5-the-fast-track git:(main) ✗ php -m | grep sodium
sodium
```

#### zip

上の module をインストールする中でインストールが為されていた。

```
➜  symfony5-the-fast-track git:(main) ✗ php -m | grep zip
zip
```

#### Composer

https://getcomposer.org/

[Composer](https://getcomposer.org/download/) にある通りインストール。

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

### NodeJS

(割愛)

### Symfony CLI

[ステップ 1: 作業環境を確認する \(Symfony Docs\)](https://symfony.com/doc/current/the-fast-track/ja/1-tools.html#symfony-cli) の通りにインストール。

https://symfony.com/doc/current/the-fast-track/ja/1-tools.html#symfony-cli

## 環境のチェック

Symfony CLI には The Fast Track プロジェクト向けの環境をチェックをするコマンドが同梱されているので、それを利用して環境を確認する。

```
➜  symfony5-the-fast-track git:(step-1) symfony book:check-requirements
[OK] Git installed
[OK] PHP installed version 7.3.24 (/usr/bin/php)
[KO] PHP extension "intl" not found, please install it - required
[KO] PHP extension "pdo_pgsql" not found, please install it - required
[OK] PHP extension "xsl" installed - required
[KO] PHP extension "sodium" not found, please install it - required
[OK] PHP extension "json" installed - required
[OK] PHP extension "ctype" installed - required
[KO] PHP extension "zip" not found, optional - needed only for chapter 17 (Panther)
[OK] PHP extension "session" installed - required
[OK] PHP extension "curl" installed - optional - needed only for chapter 17 (Panther)
[OK] PHP extension "mbstring" installed - required
[OK] PHP extension "openssl" installed - required
[OK] PHP extension "gd" installed - optional - needed only for chapter 23 (Imagine)
[KO] PHP extension "redis" not found, optional - needed only for chapter 31
[KO] PHP extension "amqp" not found, optional - needed only for chapter 32
[OK] PHP extension "tokenizer" installed - required
[OK] PHP extension "xml" installed - required
[OK] Composer installed
[OK] Docker installed
[OK] Docker Compose installed
[OK] Yarn installed


  You should fix the reported issues before starting reading the book.
```

`phpenv` で指定した `8.0.9` ではなく `/usr/bin/php` の `7.3.24` が参照されている。
Symfony CLI にはローカルの使用可能な PHP 環境を一覧できるコマンドがあるので、それにて確認をしてみる。

```
➜  symfony5-the-fast-track git:(step-1) symfony local:php:list
WARNING the current dir requires PHP 8.0.9 (.php-version from current dir: /Users/massat/src/github.com/massat/symfony5-the-fast-track/.php-version), but this version is not available
┌─────────┬───────────┬─────────┬──────────────┬─────────┬─────────┬─────────┐
│ Version │ Directory │ PHP CLI │   PHP FPM    │ PHP CGI │ Server  │ System? │
├─────────┼───────────┼─────────┼──────────────┼─────────┼─────────┼─────────┤
│ 7.3.24  │ /usr      │ bin/php │ sbin/php-fpm │         │ PHP FPM │ *       │
└─────────┴───────────┴─────────┴──────────────┴─────────┴─────────┴─────────┘

The current PHP version is selected from default version in $PATH

To control the version used in a directory, create a .php-version file that contains the version number (e.g. 7.2 or 7.2.15).
If you're using SymfonyCloud, the version can also be specified in the .symfony.cloud.yaml file.
```

`8.0.9` が指定されていることは認識されているものの `but this version is not available` と記述されている 🤔
「え、PHP 8 は対応してないの？」と思ったが、ググってたら [こちら](https://zenn.dev/chatii/articles/c464eae220334c) で `anyenv` + `phpenv` の環境に対応していないと述べられていた。(どのように推測なされたかの記述はなかった)

https://zenn.dev/chatii/articles/c464eae220334c

## おわり

- PHP 8 に対応していない?と一瞬思ったが、違う原因によるらしいことがすぐに見つかってまぁよかった。
- `anyenv` + `phpenv` 環境と `phpenv` だけでの環境とで、差が出ているのが気になる。
- まだ触り始めたばかりだけど、Symfony5 の重厚感にちょっと食あたり気味。
  - 入門記事のサンプルプロジェクトが CLI に同梱されてるとか。
