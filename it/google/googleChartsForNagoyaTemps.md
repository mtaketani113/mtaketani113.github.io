---
layout: default
title: Google Chartsを使って名古屋の予想気温表示
description: Google Chartsを使って名古屋の予想気温表示します。
category: IT
---

Google Chartsを使って名古屋の予想気温表示します。

<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
  google.charts.load('current', {'packages':['corechart']});
  google.charts.setOnLoadCallback(drawChart);

  async function getTempsData(){
    //気象庁のデータを取得。
    let meteologicalData = await fetch("https://www.jma.go.jp/bosai/forecast/data/forecast/230000.json")
                             .then(res => res.json())
    return meteologicalData;
  };

  function drawChart() {
    getTempsData().then(tempsData =>{
      let chartArray = [[]];
      chartArray[0] = tempsData[1].timeSeries[1].timeDefines;
      chartArray[1] = tempsData[1].timeSeries[1].areas[0].tempsMax;
      chartArray[2] = tempsData[1].timeSeries[1].areas[0].tempsMin;
      let data = google.visualization.arrayToDataTable(chartArray);

      var options = {
        title: '名古屋の予想気温',
        legend: { position: 'bottom' },
        vAxis: {minValue:0,maxValue:40}
      };

      var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

      chart.draw(data, options);
    });
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>

ちなみにソースは以下のように書きました。  
```JavaScript
<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
  google.charts.load('current', {'packages':['corechart']});
  google.charts.setOnLoadCallback(drawChart);

  async function getTempsData(){
    //気象庁のデータを取得。
    let meteologicalData = await fetch("https://www.jma.go.jp/bosai/forecast/data/forecast/230000.json")
                             .then(res => res.json())
    return meteologicalData;
  };

  const transpose = a => a[0].map((_, c) => a.map(r => r[c]));

  function drawChart() {
    getTempsData().then(tempsData =>{
      let chartArray = [[]];
      chartArray[0] = tempsData[1].timeSeries[1].timeDefines;
      chartArray[1] = tempsData[1].timeSeries[1].areas[0].tempsMin;
      chartArray[2] = tempsData[1].timeSeries[1].areas[0].tempsMax;
      chartArray[0][0] = "日付";
      chartArray[1][0] = "最低気温";
      chartArray[2][0] = "最高気温";

      for(var i = 0; i < chartArray[0].length; i++){
        //体重の値は「"」を削除
        if(chartArray[1][i].match(/\-?\d+(.\d+)?(e[\+\-]d+)?/)){
          chartArray[1][i] = parseFloat(chartArray[1][i].replace("'", ''));
        }
        if(chartArray[2][i].match(/\-?\d+(.\d+)?(e[\+\-]d+)?/)){
          chartArray[2][i] = parseFloat(chartArray[2][i].replace("'", ''));
        }
      }

      chartArray = transpose(chartArray);
      let data = google.visualization.arrayToDataTable(chartArray);

      var options = {
        title: '名古屋の予想気温',
        legend: { position: 'bottom' },
        vAxis: {minValue:0,maxValue:40}
      };

      var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

      chart.draw(data, options);
    });
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>
```
