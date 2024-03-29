---
layout: default
title: GitHubのAPIを利用して一覧表示
description: GitHubのリポジトリーやIssueをAPIを利用してJavascriptで一覧表示する方法を記載します。
category: github
---

**重要**  
APIのアクセスには上限があるため、アクセスを繰り返すと  
リストを出せないようですｗ  
「API rate limit exceeded」とでます。  
アクセス制限がかかっているときのエラー処理など  
今後追記していこうかな(#^^#)

本題を始めます。
async/awaitとfetch関数を利用して、GithubのAPIを呼び出し、
私のリポジトリをリスト表示しました。

<script>
    async function getRepository (url) {
        const res = await fetch(url);
        const status = await res.status;
        if(status == 200){
            const json = await res.json();
            json.forEach(e => {
                document.getElementById("repos")
                    .insertAdjacentHTML('afterbegin',
                        '<li>'+ e.name +'</li>');
            })
        }else{
            document.getElementById("repos")
                .insertAdjacentHTML('afterbegin',  
                '<li style="color:red">APIの利用上限を超えました。しばらくしてからアクセスしてください。</li>');
        }
    }
    getRepository("https://api.github.com/users/mtaketani113/repos");
</script>

<div id = "repos"></div>

ソースは以下のように書きました  
最近はAPIの呼び出しも簡単ですね(#^^#)

```JavaScript
<script>
    async function getRepository (url) {
        const res = await fetch(url);
        const status = await res.status;
        if(status == 200){
            const json = await res.json();
            json.forEach(e => {
                document.getElementById("repos")
                    .insertAdjacentHTML('afterbegin',
                        '<li>'+ e.name +'</li>');
            })
        }else{
            document.getElementById("repos")
                .insertAdjacentHTML('afterbegin',  
                '<li style="color:red">APIの利用上限を超えました。しばらくしてからアクセスしてください。</li>');
        }
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

こんな感じのExcelをCSVで出力しました。

<script>
    async function getIssues (url) {
        const res = await fetch(url);
        const status = await res.status;
        if(status == 200){
            const json = await res.json();
            json.forEach(e => {
            document.getElementById("issues")
                .insertAdjacentHTML('afterbegin',
                    '<tr><td>'+ e.number +'</td><td>'+ e.title 
                    +'</td><td>'+ e.created_at +'</td></tr>');
            })
        }else{
            document.getElementById("issues")
                .insertAdjacentHTML('afterbegin',  
                '<tr><td colspan="3" style="color:red">APIの利用上限を超えました。しばらくしてからアクセスしてください。</td></tr>');
        }
    }
    getIssues("https://api.github.com/repos/mtaketani113/createIssueCsv/issues");
</script>

<table>
    <thead>
        <tr>
            <th>Issue No.</th>
            <th>Title</th>
            <th>作成日</th>
        </tr>
    </thead>
    <tbody id="issues">
    </tbody>
</table>


上のコードはこんな感じです。

```JavaScript
<script>
    async function getIssues (url) {
        const res = await fetch(url);
        const status = await res.status;
        if(status == 200){
            const json = await res.json();
            json.forEach(e => {
            document.getElementById("issues")
                .insertAdjacentHTML('afterbegin',
                    '<tr><td>'+ e.number +'</td><td>'+ e.title 
                    +'</td><td>'+ e.created_at +'</td></tr>');
            })
        }else{
            document.getElementById("issues")
                .insertAdjacentHTML('afterbegin',  
                '<tr><td colspan="3" style="color:red">APIの利用上限を超えました。しばらくしてからアクセスしてください。</td></tr>');
        }

    }
    getIssues("https://api.github.com/repos/mtaketani113/createIssueCsv/issues");
</script>
```

管理者からすると一覧で見たいとか、納期を一目で見たいとか、  
Excelのほうが、ぱっと見てすぐわかるとかが大事のようです。  
Githubを新たに使おうとすると、こうした抵抗にあうと思います。

たしかに、Excelの方がよいことも多々あります。  
なので彼らの不安をわからずやと、何も対策しないのではなく  
真摯に払拭してあげる必要があります。

そんな人たちを納得させるために、GithubのAPIを使って  
上記のサンプルのように課題を一覧表にしたり、ちょっとアレンジして  
特定の形式だ書いたら納期も一覧表に追加するようにしましょう。  
きっと納得してもらえると思います。  

本当はそんなことせずGithubを使って見てほしいんですがね。  
現実はなかなかそうはいきませんね。

ちなみにIssueをCSVにして取得するJavaのプログラムは私も書いてますが、  
ググればもっといいのがいっぱいあると思いますので、  
そちらを利用してみてくださいｗ