---
layout: default
title: GitHubのリポジトリーを一覧表示
description: GitHubのリポジトリーをAPIを利用してJavascriptで一覧表示する方法を記載します。
category: IT
---
async/await と fetch関数を利用して、GithubのAPIを呼び出し、リスト表示してみました。

<script>
    async function getRepository (url) {
        const res = await fetch(url);
        const json = await res.json();
        json.forEach(e => {
            document.getElementById("repos")
                .insertAdjacentHTML('afterbegin',
                    '<li><a href="' + e.html_url + '">'+ e.name +'</a></li>');
        })
    }
    getRepository("https://api.github.com/users/mtaketani113/repos");
</script>

<div id = "repos"></div>

ソースは以下のように書きました

```JavaScript
<script>
    async function getRepository (url) {
        const res = await fetch(url);
        const json = await res.json();
        json.forEach(e => {
            document.getElementById("repos")
                .insertAdjacentHTML('afterbegin',
                    '<li><a href="' + e.html_url + '">'+ e.name +'</a></li>');
        })
    }
    getRepository("https://api.github.com/users/mtaketani113/repos");
</script>

<div id = "repos"></div>
```

ちなみに、async/awaitはIEには対応していないので、  
IEやEDGEのIEモードでは表示できないかと。

GithubにはAPIがあり、リポジトリだけでなく  
Issueの一覧を出せたりします。  
GithubのIssueで課題管理している方も多いかと思いますが、  
まだまだ会社ではExcel大好きな方々がいっぱいいるかとｗ

一覧で見たいとか、納期を一目で見たいとか、  
Excelのほうが、ぱっと見てすぐわかるとか  
Githubを新たに使おうとすると抵抗しだすと思います。

たしかに、Excelの方がよいことも多々あって、  
彼らの不安を払拭してあげる必要があります。

そんな人たちを納得させるために、GithubのAPIを使って  
課題を一覧表にしたり、ちょっとアレンジして  
特定の形式だ書いたら納期も一覧表に追加するようにしましょう。  
きっと納得してもらえると思います。  

本当はそんなことせずGithubを使って見てほしいんですがね。  
現実はなかなかそうはいきませんね。

ちなみにIssueをCSVにして取得するJavaのプログラムは私も書いてますが、  
ググればもっといいのがいっぱいあると思いますので、  
そちらを利用してみてくださいｗ