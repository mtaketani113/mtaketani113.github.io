---
layout: default
title: GitHub Pageが更新されない
description: GitHub Pagesのビルドが実行されず、ページが更新されない現象が発生しました。原因はGithubの障害でした。
category: IT
---
2021年の5月16日にGithub Pagesが更新されないという現象が発生しました。  

Github Pagesはリポジトリにプッシュすると自動でビルドが始まって、  
サイトが更新されます。  

しかし、数分前までうまく動いていたのに急に動かなくなり、以下のメールが送られてきました。  
誤記を訂正したぐらいなので、失敗する要素がないのですが・・・

```
The page build failed for the `master` branch with the following error:

Unable to build page. Please try again later.
```

Githubの履歴を見ても、ビルドされたら`✓`がつくのですが、
今回は以下のように何もつきませんでした。

![ビルドされない]](/images/it/github/donotWrokBuild.png)

ビルドが失敗すると以下のように`×`がつきます。

![ビルド失敗]](/images/it/github/buildFailed.png)

今回の原因は、結局のところ **Githubの障害** でした。

[Github Pagesの障害](https://www.githubstatus.com/incidents/zbpwygxwb3gw)

ずーっと、なんでだろうと試していたのですが、  
たまにGithubでも障害が発生することを忘れていました(*'ω'*)

みなさんも、もし何をやってもダメなときは、  
障害を疑ってみては？

現状のステータスは以下のサイトで見えます。  
[Githubのステータス](https://www.githubstatus.com/)

2021年5月17日は何もないようですねｗ

![Githubのステータス](/images/it/github/githubStatus.png)


履歴を見ると、意外に発生しているのに驚きました。  

[Githubのステータスの履歴](https://www.githubstatus.com/)

日々更新しているからですからね？  
Githubも苦労してるんですね~ｗ  
大変。。


また、他にも、Github Pagesには以下の制限があるようです。  

- 1時間当たりビルドは10回以下
- サイトのサイズが10G以下
- 通信できる帯域は1ヶ月100GB以下

1時間当たり10回のビルドは少し引っ掛かりそう(*'▽')

[Github Pagesの制限](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#usage-limits)