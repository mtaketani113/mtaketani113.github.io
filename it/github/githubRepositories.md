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
