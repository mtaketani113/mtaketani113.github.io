---
layout: default
title: Google Chartsを使って体重管理
description: Google Chartsを使って私の体重の推移を観察。目標は60Kg
category: IT
---

Google Chartsを使って私の体重の推移を観察してみようかと。  
家から全然でないので、かなり太ってしました。  
目標は60Kgです。

<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
  google.charts.load('current', {'packages':['corechart']});
  google.charts.setOnLoadCallback(drawChart);

  function getCsv(url){
    //CSVファイルを文字列で取得。
    var txt = new XMLHttpRequest();
    txt.open('get', url, false);
    txt.send();

    //改行ごとに配列化
    var arr = txt.responseText.split('\n');
  
    //1次元配列を2次元配列に変換
    var res = [];
    for(var i = 0; i < arr.length; i++){
      //空白行が出てきた時点で終了
      if(arr[i] == '') break;
  
      //","ごとに配列化
      res[i] = arr[i].split(',');
  
      //体重の値は「"」を削除
      if(res[i][1].match(/\-?\d+(.\d+)?(e[\+\-]d+)?/)){
        res[i][1] = parseFloat(res[i][1].replace('"', ''));
      }
    }
    return res;
  }


  function drawChart() {
    var weight = getCsv("/it/google/weight.csv");

    var data = google.visualization.arrayToDataTable(weight);

    var options = {
      title: '私の体重の推移',
      legend: { position: 'bottom' },
      vAxis: {minValue:50,maxValue:80}
    };

    var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

    chart.draw(data, options);
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>

ちなみにソースは以下のように書きました。  
データの個所は外部ファイルにしようかな。
```JavaScript
<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
  google.charts.load('current', {'packages':['corechart']});
  google.charts.setOnLoadCallback(drawChart);

  function drawChart() {
    var data = google.visualization.arrayToDataTable([
      ['日付', '体重(Kg)'],
      ['21/05/16',  70.8],
      ['21/05/17',  70.7],
    ]);

    var options = {
      title: '私の体重の推移',
      legend: { position: 'bottom' },
      vAxis: {minValue:50,maxValue:80}
    };

    var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

    chart.draw(data, options);
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>
```


## 改修その１

getCsvメソッドを追加して、CSVファイルを読み込むように修正しました。  

```
  function getCsv(url){
    //CSVファイルを文字列で取得。
    var txt = new XMLHttpRequest();
    txt.open('get', url, false);
    txt.send();

    //改行ごとに配列化
    var arr = txt.responseText.split('\n');
  
    //1次元配列を2次元配列に変換
    var res = [];
    for(var i = 0; i < arr.length; i++){
      //空白行が出てきた時点で終了
      if(arr[i] == '') break;
  
      //","ごとに配列化
      res[i] = arr[i].split(',');
  
      //体重の値は「"」を削除
      if(res[i][1].match(/\-?\d+(.\d+)?(e[\+\-]d+)?/)){
        res[i][1] = parseFloat(res[i][1].replace('"', ''));
      }
    }
    return res;
  }

  function drawChart() {
    var weight = getCsv("/it/google/weight.csv");

    var data = google.visualization.arrayToDataTable(weight);

    var options = {
      title: '私の体重の推移',
      legend: { position: 'bottom' },
      vAxis: {minValue:50,maxValue:80}
    };

    var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

    chart.draw(data, options);
  }
```

日付のほうは文字列なので、  
登録忘れをすると
スケールがおかしくなる気がします。

次は、日付型に改修ですかね。

## 参考サイト

[折れ線グラフ【Google Charts入門】- 配当金推移をチャート化する方法](https://uxbear.me/googlecharts-line/)

[CSVファイルをグラフ化](https://kurage.ready.jp/jhp_g/html5/graph/csvTgraph.html)