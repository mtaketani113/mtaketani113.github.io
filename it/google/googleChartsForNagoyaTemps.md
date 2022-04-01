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

  function getTempsData(){
    //気象庁のデータを取得。
    let meteologicalData;
    fetch("https://www.jma.go.jp/bosai/forecast/data/forecast/230000.json")
      .then((res) => res.json())
      .then(data => meteologicalData=data);
    return meteologicalData;
  };


  function drawChart() {
    let tempsData = getTempsData();

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
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>

ちなみにソースは以下のように書きました。  
```JavaScript
<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
  google.charts.load('current', {'packages':['corechart']});
  google.charts.setOnLoadCallback(drawChart);

  function getTempsData(){
    //気象庁のデータを取得。
    let meteologicalData;
    fetch("https://www.jma.go.jp/bosai/forecast/data/forecast/230000.json")
      .then((res) => res.json())
      .then(data => meteologicalData=data);
    return meteologicalData;
  };


  function drawChart() {
    let tempsData = getTempsData();

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
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>
```
