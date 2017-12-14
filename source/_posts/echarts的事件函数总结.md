---
title: echarts的事件函数总结
date: 2016-11-16 11:03:58
tags:
  - ECharts
  - JavaScript
categories: 
  - 笔记
---
echarts事件在不同条件下有不同的触发方式
div不是自适应的话，一定要设置宽高！！！

<!-- more -->

## 在echarts2中的函数事件（标签式单文件引入）
``` bash
<div id="main" style="width: px;height: px;"></div>
<script src="http://echarts.baidu.com/build/dist/echarts-all.js"></script>    // echarts2版本
<script>
    var myChart = echarts.init(document.getElementById('main')); 
    var option = {
       ...,
        series:[
            ...,
            clickable: true,
            ...
       ] 
    };
    myChart.setOption(option);
    window.onresize=myCharts.resize;  //图表自适应宽度，不用设置宽，但存在一个页面多个表格切换时不能用，只适合一页页面一个数据表格使用
    var ecConfig = echarts.config;
    myChart.on(ecConfig.EVENT.CLICK, eConsole);   //其他时间形式一样
    function eConsole(param) {
      if (typeof param.seriesIndex == 'undefined') {
        return;
      }
      if (param.type == 'click') {
        alert(param.name);
        alert(param.data);            
      }
    }
</script>
```

## 在echarts2中的函数事件（模块化单文件引入）
``` bash
<body>
    <div id="main" style="width: px;height: px;"></div>
    <!-- ECharts单文件引入 -->
    <script src="http://echarts.baidu.com/build/dist/echarts-all.js"></script>   // echarts2版本
    <script type="text/javascript">
        // 路径配置
        require.config({
            paths: {
                echarts: 'http://echarts.baidu.com/build/dist'
            }
        });
        // 使用
        require(
            [
                'echarts',
                'echarts/chart/bar'    // 使用柱状图就加载bar模块，按需加载
            ],
            function (ec) {
                // 基于准备好的dom，初始化echarts图表
                var myChart = ec.init(document.getElementById('main')); 
                var option = {
                    ...,
                    series : [
                          ...,
                          clickable: true,
                          ...
                    ]
                };
                // 为echarts对象加载数据 
                myChart.setOption(option); 
            }
        );
        var ecConfig = require('echarts/config');
        function eConsole(param) {
                ......
        }
        myChart.on(ecConfig.EVENT.CLICK, eConsole);    //各种事件
        myChart.on(ecConfig.EVENT.DBLCLICK, eConsole);
        myChart.on(ecConfig.EVENT.HOVER, eConsole);
        myChart.on(ecConfig.EVENT.DATA_ZOOM, eConsole);
        myChart.on(ecConfig.EVENT.LEGEND_SELECTED, eConsole);
        myChart.on(ecConfig.EVENT.MAGIC_TYPE_CHANGED, eConsole);
        myChart.on(ecConfig.EVENT.DATA_VIEW_CHANGED, eConsole);
    </script>
</body>
```

## 在echarts3中的函数事件（标签式单文件引入）
```bash
<div id="main" style="width: px;height: px;"></div>
<script src="js/echarts.js"></script>   // echarts3版本
<script>
    var myChart = echarts.init(document.getElementById('main')); 
    var option = {
       ...,
        series:[
            ...,
            clickable: true,
            ...
       ] 
    };
    myChart.setOption(option);
    myChart.on('click', function eConsole(param) {
      if (typeof param.seriesIndex == 'undefined') {
        return;
      }
      if (param.type == 'click') {
        alert(param.name);
      }
    });
</script>
```