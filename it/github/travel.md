---
layout: default
title: Github Gistで旅行した場所を表示
description: Github GistでGeoJsonを利用して旅行した個所を表示
category: IT
---

Github GistにGeoJsonを記載すると地図上に点を打ったりできます。  
それを利用して旅行したところに点を打ってみました。  
全然旅行していないと妻に怒られました。  
コロナだから勘弁して～  
コロナじゃなくてもあんまりいかなかった気はしますがｗ

今後も点を足していこうかと思います。  
早くコロナが終わっていっぱい旅行したいな(*'ω'*)

<script src="https://gist.github.com/mtaketani113/0aa9228ab7eefcaa149e789791f7a7ff.js"></script>

ちなみに、Geo Jsonでの点の書き方は以下です。

```Json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {
        "title": "タイトル",
        "description": "説明"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [
          "経度",
          "緯度"
        ]
      }
    }
  ]
}
```

featuresの中を増やしていけば点が増えていきます。

後はこれをGithub Gistにアップして右上にあるURLをコピーして  
サイトに張り付ければ完了です。
すごく簡単(^^)/

↓ここ  
![地図の埋め込みスクリプト](/images/it/github/mapEmbedScript.png)

他にも線を書いたり面を書いたりもできるみたいなので、  
機会があったら挑戦してみようと思います。