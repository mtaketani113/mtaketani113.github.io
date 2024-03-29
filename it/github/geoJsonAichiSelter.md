---
layout: default
title: 愛知の避難所を地図で表示
description: G空間情報センターのデータを利用して、愛知の避難所を地図に出してみました。避難所の場所だけではなく、対応災害も確認しましょう。
category: github
---

愛知の避難所（2019年5月13日更新）を地図に載せました。

Github Gistを利用して地図を利用する方法を以下を参照ください。    
[Github Gistで旅行した場所を表示](/it/github/travel.html)

<script src="https://gist.github.com/mtaketani113/774f13a319d0fd9766a786e241482869.js"></script>

今回は、G空間情報センターの以下のデータを使用。

[指定緊急避難場所データ_23愛知県](https://www.geospatial.jp/ckan/dataset/hinanbasho/resource/8c2ba5d8-e32c-4c2d-91f9-f63c9688a7ea)

このサイトを利用すれば、GeoJsonに変換できるようですが、  
今回は、以下のサイトを利用して、変換しました。

[Shapefile を javascript だけで GeoJSON にしてみる](https://qiita.com/frogcat/items/b3235c06d64cee01fa47)

このサイトの「3.ブラウザでの使用」が一番簡単そうだったのですが、
やってみると以下のように、説明文のところが文字化けしました。

```JSON
{
    "type": "Feature",
    "properties": {
        "Žw’è‹Ù‹}”ð": "–Lª‘º–ðê@•xŽRŽxŠ",
        "ŠÝ’n": "ˆ¤’mŒ§–Lª‘º•xŽRŽš‰º“È13-3",
        "^…": "",
        "‚ª‚¯•ö‚êA": "",
        "‚’ª": null,
        "’nk": "",
        "’Ã”g": null,
        "‘å‹K–Í‚È‰Î": "",
        "“à…”Ã—”": null,
        "‰ÎŽRŒ»Û": null
    },
    "geometry": {
        "type": "Point",
        "coordinates": [
            137.809613646678,
            35.1896523757199
        ]
    }
}
```

UTF-8で変換しているからでしょうかね。  
ですので、1,2をほぼそのまま実行したら簡単にできました。  
でも、説明文のところの文字数制限がかかってるのかな？  
まだ少し変・・・またなおそっと。

最近は異常気象？で大雨が多かったりするので、  
皆さん近くの避難所を確認してみてください。  
21年は梅雨入りもめっちゃ早いし、21年5月22日も大雨でしたし・・・

他にも東海地方は南海トラフ地震が  
今後30年で70～80%の確率で   
発生するといわれているようです。  
名古屋に住む人間として、ほんと怖い。  

自分はこれを見て、一番近くの避難所で津波に〇  
が付いていないことに気が付きました( ﾟДﾟ)  
もう少し北の避難所に行かないといけないのか。。  

みなさんも、自分の家の近くの避難所と、対応災害を確認してみてください。  
参照したデータは「2019年5月13日更新」と書いてあったので
最新はググったほうがよさそうな気がするｗ  