---
title: Gitでmerge済みだけど消し忘れていた大量のリモートブランチを一括で消したワンライナーのメモ
emoji: "🧹"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git"]
published: true
---

長いこと Git リポジトリをチーム運用してきて "プルリク merge したけどブランチ消し忘れた" 的なのがつもりに積もり、気がついたらリモートにブランチが 100 くらいありました。
`main` ブランチに merge 済みのブランチを以下のワンライナーで消しました。

```sh
git branch --remotes --merged | grep -v -e origin\/main | sed  s/origin\\///g | xargs -I % git push --delete origin %
```
