---
title: "phpenvでphp8をインストールした作業メモ"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["macOS", "php", "phpenv"]
published: true
---

手元の Macbook に phpenv をインストールした作業メモ。
元々色々とパッケージがインストールされていただろう環境での作業なので再現性はなさそうだが、エラーメッセージにしたがって作業していけば大丈夫。

## 環境

- OS: macOS 11.5.1
- phpenv: v0.9.0-rc.1

## 作業

おもむろにインストールを試みる。

```
$ phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Downloading]: https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Please reinstall the BZip2 distribution
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811213401.log'.
[Warn]: Aborting build.
```

`bzip2` がないと怒られた。

### bzip2

[こちら](https://qiita.com/takeshi81/items/72e0010454f05bf97639)を参照。
`bzip2` は Homebrew でインストールしていたが、インストールプロセスからは見えなかったらしい。
環境変数から、`bzip2`の場所を渡してあげる。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2)" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Package requirements (libpng) were not met:

No package 'libpng' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables PNG_CFLAGS
and PNG_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811213852.log'.
[Warn]: Aborting build.
```

`libpng` がないと怒られた。

### libpng

Homebrew で`libpng`をインストールする。

```
$ brew install libpng
==> Downloading https://ghcr.io/v2/homebrew/core/libpng/manifests/1.6.37
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/libpng/blobs/sha256:a8f1c35f9f004c4f7878c30027e35a9fb9551782df963f88deebd3dc29d94d51
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:a8f1c35f9f004c4f7878c30027e35a9fb9551782df963f88deebd3dc29d94d51?se=2021-08-11T12%3A50%3A00Z&sig=SBbItSLMomLLowa7ah8sJt
######################################################################## 100.0%
==> Pouring libpng--1.6.37.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/libpng/1.6.37: 27 files, 1.3MB
```

再度インストールを試行。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2)" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Package requirements (libjpeg) were not met:

No package 'libjpeg' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables JPEG_CFLAGS
and JPEG_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811214349.log'.
[Warn]: Aborting build.
```

`libjpeg` がないと怒られた。

### libjpeg

Homebrew で`libjpeg`をインストールする。

```
$ brew install libjpeg
==> Downloading https://ghcr.io/v2/homebrew/core/jpeg/manifests/9d
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/jpeg/blobs/sha256:c565929a4901365a3408b57275802f943625c1e29e1b48a186edd2e97d8c0bdb
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:c565929a4901365a3408b57275802f943625c1e29e1b48a186edd2e97d8c0bdb?se=2021-08-11T12%3A50%3A00Z&sig=8sTx4NGNXFJfhMAVbMVbHP
######################################################################## 100.0%
==> Pouring jpeg--9d.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/jpeg/9d: 21 files, 954.0KB
```

再度インストールを試行。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2)" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Please specify the install prefix of iconv with --with-iconv=<DIR>
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811214519.log'.
[Warn]: Aborting build.
```

`iconv`が見えないらしい。

### libiconv

Homebrew で`libiconv`をインストールする。

```
$ brew install libiconv
==> Downloading https://ghcr.io/v2/homebrew/core/libiconv/manifests/1.16
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/libiconv/blobs/sha256:9253ae6551eb63499fb292b4a65d054c918b93dab8beff0bc12f3290f77bd15c
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:9253ae6551eb63499fb292b4a65d054c918b93dab8beff0bc12f3290f77bd15c?se=2021-08-11T12%3A55%3A00Z&sig=YQWyRDuIQae9jAZR9jwYQi
######################################################################## 100.0%
==> Pouring libiconv--1.16.big_sur.bottle.tar.gz
==> Caveats
libiconv is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have libiconv first in your PATH, run:
  echo 'export PATH="/usr/local/opt/libiconv/bin:$PATH"' >> ~/.zshrc

For compilers to find libiconv you may need to set:
  export LDFLAGS="-L/usr/local/opt/libiconv/lib"
  export CPPFLAGS="-I/usr/local/opt/libiconv/include"

==> Summary
🍺  /usr/local/Cellar/libiconv/1.16: 31 files, 2.5MB
```

環境変数に`iconv`への PATH を追加しつつ再度インストールを試行。

```
➜  ~ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
No package 'icu-uc' found
No package 'icu-io' found
No package 'icu-i18n' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables ICU_CFLAGS
and ICU_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811220557.log'.
[Warn]: Aborting build.
```

`icu`のパッケージが色々と見つからない様子。

## icu4c

Homebrew で`icu4c`をインストールする。

```
$ brew install icu4c

Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 2 formulae.

==> Downloading https://ghcr.io/v2/homebrew/core/icu4c/manifests/69.1
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/icu4c/blobs/sha256:d46b8ec5c3db629e7848e9fd31e5ec99ed952d9c81c8936a2511fae803d831fd
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:d46b8ec5c3db629e7848e9fd31e5ec99ed952d9c81c8936a2511fae803d831fd?se=2021-08-11T13%3A30%3A00Z&sig=98RDFKyD50mBjWkpYhgX9B
######################################################################## 100.0%
==> Pouring icu4c--69.1.big_sur.bottle.tar.gz
==> Caveats
icu4c is keg-only, which means it was not symlinked into /usr/local,
because macOS provides libicucore.dylib (but nothing else).

If you need to have icu4c first in your PATH, run:
  echo 'export PATH="/usr/local/opt/icu4c/bin:$PATH"' >> ~/.zshrc
  echo 'export PATH="/usr/local/opt/icu4c/sbin:$PATH"' >> ~/.zshrc

For compilers to find icu4c you may need to set:
  export LDFLAGS="-L/usr/local/opt/icu4c/lib"
  export CPPFLAGS="-I/usr/local/opt/icu4c/include"

For pkg-config to find icu4c you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/icu4c/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/icu4c/69.1: 259 files, 72.8MB
```

環境変数に`PKG_CONFIG_PATH`を追加しつつ再度インストールを試行。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Cannot find libtidy
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811222338.log'.
[Warn]: Aborting build.
```

`libtidy` がないと怒られた。

## tidy-html5

Homebrew で`tidy-html5`をインストールする。

```
$ brew install tidy-html5
==> Downloading https://ghcr.io/v2/homebrew/core/tidy-html5/manifests/5.8.0
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/tidy-html5/blobs/sha256:9127cf10347816285db70f0ec794a08433e44426f9f4320d5fecedbdcfb15e2b
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:9127cf10347816285db70f0ec794a08433e44426f9f4320d5fecedbdcfb15e2b?se=2021-08-11T13%3A35%3A00Z&sig=i%2FpYK4b%2B%2Bw1b%2Bc
######################################################################## 100.0%
==> Pouring tidy-html5--5.8.0.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/tidy-html5/5.8.0: 14 files, 2.9MB
```

再度インストールを試みる。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
No package 'libzip' found
No package 'libzip' found
No package 'libzip' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables LIBZIP_CFLAGS
and LIBZIP_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811222857.log'.
[Warn]: Aborting build.
```

`libzip` がないと怒られた。

## libzip

Homebrew で`libzip`をインストールする。

```
$ brew install libzip

==> Downloading https://ghcr.io/v2/homebrew/core/libzip/manifests/1.8.0
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/libzip/blobs/sha256:f5b0d74305b2f249a8389bbee71ab51e446fcc824c950b2a954860d21e4d61b4
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:f5b0d74305b2f249a8389bbee71ab51e446fcc824c950b2a954860d21e4d61b4?se=2021-08-11T13%3A40%3A00Z&sig=UUnbh51Jj8eKF5OrMc2q5A
######################################################################## 100.0%
==> Pouring libzip--1.8.0.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/libzip/1.8.0: 144 files, 771KB
```

再度インストールを試みる。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9
[Compiling]: /var/tmp/php-build/source/8.0.9
[xdebug]: Installing version 3.0.4
[Downloading]: http://xdebug.org/files/xdebug-3.0.4.tgz
[xdebug]: Compiling xdebug in /var/tmp/php-build/source/xdebug-3.0.4

-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
PEAR package PHP_Archive not installed: generated phar will require PHP's phar extension be enabled.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.0.9.20210811223422.log'.
[Warn]: Aborting build.
```

> PEAR package PHP_Archive not installed: generated phar will require PHP's phar extension be enabled.

これはググると `autoconf` がないことによるエラーのよう。

## autoconf

Homebrew で`autoconf`をインストールする。

```
$ brew install autoconf
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 1 formula.

==> Downloading https://ghcr.io/v2/homebrew/core/m4/manifests/1.4.19
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/m4/blobs/sha256:b22472f659112cf12163bba770d891618b3ada5aaf5baa01516d80fef6214617
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:b22472f659112cf12163bba770d891618b3ada5aaf5baa01516d80fef6214617?se=2021-08-11T13%3A55%3A00Z&sig=UkhKSZo8q0qMlB9WRAfpFh
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/autoconf/manifests/2.71
######################################################################## 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/autoconf/blobs/sha256:0aa64f171bac19ce6ac0c0ca697f30658db78cf175550dfde3dbda907b7f2500
==> Downloading from https://pkg-containers.githubusercontent.com/ghcr1/blobs/sha256:0aa64f171bac19ce6ac0c0ca697f30658db78cf175550dfde3dbda907b7f2500?se=2021-08-11T13%3A55%3A00Z&sig=DOCbQDw1E%2F2u1UyHAgNf
######################################################################## 100.0%
==> Installing dependencies for autoconf: m4
==> Installing autoconf dependency: m4
==> Pouring m4--1.4.19.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/m4/1.4.19: 13 files, 724.4KB
==> Installing autoconf
==> Pouring autoconf--2.71.big_sur.bottle.tar.gz
🍺  /usr/local/Cellar/autoconf/2.71: 71 files, 3.2MB
```

再度インストールを試みる。

```
$ PHP_BUILD_CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv)" PKG_CONFIG_PATH="$(brew --prefix icu4c)/lib/pkgconfig" phpenv install 8.0.9
phpenv: /Users/massat/.anyenv/envs/phpenv/versions/8.0.9 already exists
continue with installation? (y/N) y
[Info]: Loaded extension plugin
[Info]: Loaded apc Plugin.
[Info]: Loaded composer Plugin.
[Info]: Loaded github Plugin.
[Info]: Loaded uprofiler Plugin.
[Info]: Loaded xdebug Plugin.
[Info]: Loaded xhprof Plugin.
[Info]: Loaded zendopcache Plugin.
[Info]: php.ini-production gets used as php.ini
[Info]: Building 8.0.9 into /Users/massat/.anyenv/envs/phpenv/versions/8.0.9
[Skipping]: Already downloaded and extracted https://secure.php.net/distributions/php-8.0.9.tar.bz2
[Preparing]: /var/tmp/php-build/source/8.0.9
[Compiling]: /var/tmp/php-build/source/8.0.9
[xdebug]: Installing version 3.0.4
[Skipping]: Already downloaded http://xdebug.org/files/xdebug-3.0.4.tgz
[xdebug]: Compiling xdebug in /var/tmp/php-build/source/xdebug-3.0.4
[xdebug]: Installing xdebug configuration in /Users/massat/.anyenv/envs/phpenv/versions/8.0.9/etc/conf.d/xdebug.ini
[xdebug]: Cleaning up.
Makefile:245: warning: overriding commands for target `test'
Makefile:132: warning: ignoring old commands for target `test'
[Info]: Enabling Opcache...
[Info]: Done
[Info]: The Log File is not empty, but the Build did not fail. Maybe just warnings got logged. You can review the log in /tmp/php-build.8.0.9.20210811224807.log or rebuild with '--verbose' option
[Success]: Built 8.0.9 successfully.
```

インストールできた様子 🎉

## 確認

```
$ phpenv shell 8.0.9
8.0.9
$ php -v
PHP 8.0.9 (cli) (built: Aug 11 2021 22:52:20) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.0.9, Copyright (c) Zend Technologies
    with Zend OPcache v8.0.9, Copyright (c), by Zend Technologies
    with Xdebug v3.0.4, Copyright (c) 2002-2021, by Derick Rethans
```
