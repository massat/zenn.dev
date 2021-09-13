---
title: 'Symfony5 "The Fast Track"用のDockerイメージを作った'
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["php", "symfony", "docker"]
published: false
---

## TL;DR

["Symfony 5: The Fast Track"](https://symfony.com/book) を試す環境づくりが面倒な感じになったので Docker イメージを作った。

https://hub.docker.com/r/massat/symfony5-the-fast-track

(Dockerfile は[こちら](https://github.com/massat/Dockerfile-symfony5-the-fast-track))

## 背景

["Symfony 5: The Fast Track"](https://symfony.com/book) (以下`"The Fast Track"`)を試すために手元の Mac に環境を作ろうとしていたがうまくいかなかった。

https://zenn.dev/massat/articles/symfony5-the-first-track-1

> We will use Docker for services, but I like to have PHP installed on my local computer for performance, stability, and simplicity reasons. Call me old school if you like, but the combination of a local PHP and Docker services is the perfect combo for me.
> ( https://symfony.com/doc/current/the-fast-track/en/1-tools.html#php より )

とあるように"The Fast Track"の著者はローカルマシンに PHP をインストールして使うのが好みらしい。
入門する上でそれに倣って環境を作ろうとしたが `anyenv` `phpenv` 周りでハマってしまった格好。

ローカルマシンに PHP をインストールするのは諦めて、 Docker イメージを作って進めることにした。

## 使い方

こんな感じで動く。

```sh
docker run --rm -it -p 8000:8000 -v $(PWD):/src -v /var/run/docker.sock:/var/run/docker.sock massat/symfony5-the-fast-track symfony book:check-requirements
```

![](/images/docker-image-symfony5-the-first-track/check-requirements.gif)

### Docker in Docker

"The Fast Track"は環境に Docker が必要で、今回はその環境ごと Docker 化しているのでいわゆる"Docker in Docker"の形にする必要があった。
これはホストで実行する Docker のソケットファイルをゲストにマウントすることで簡単に実現できた。

## 参考

- https://esakat.github.io/esakat-blog/posts/docker-in-docker/
