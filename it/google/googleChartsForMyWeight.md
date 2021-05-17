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

  function drawChart() {
    var data = google.visualization.arrayToDataTable([
      ['日付', '体重(Kg)'],
      ['21/05/16',  70.8],
      ['21/05/17',  70.7],
    ]);

    var options = {
      title: '私の体重の推移',
      legend: { position: 'bottom' }
    };

    var chart = new google.visualization.LineChart(document.getElementById('curve_chart'));

    chart.draw(data, options);
  }
</script>

<div id="curve_chart" style="width: 100%; height: 500px"></div>