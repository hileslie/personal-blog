---
title: echarts3 提示框浮层内容格式器
date: 2017-12-14 10:27:55
tags:
  - ECharts
  - JavaScript
categories: 
  - 工作
---

工作项目需求,需要做统计分析图,所以采用了 echarts 工具。然后其中需要把后台返回的时间戳数据转化为时分秒,显示在折线图中的提示框浮层中...

<!-- more -->

## echarts 的 tooltip 配置项

tooltip 的 formatter 属性,用来显示提示框浮层内容:

* 1.字符串模板:
  模板变量有 {a}, {b}，{c}，{d}，{e}，分别表示系列名，数据名，数据值等。示例:

```bash
formatter: '{a} <br/>{b}: {c} ({d}%)'
```

* 2.回调函数回调函数格式：

```bash
(params: Object|Array, ticket: string, callback: (ticket: string, html: string)) => string
```

[详细配置,参考 echarts 官网](http://echarts.baidu.com/option.html#tooltip.formatter)

## 项目原始数据和显示

* 代码：

```bash
      let option = {
        tooltip: {
          trigger: 'axis'
        },
        xAxis: {
          type: 'category',
          data: this.totalTimeX,
          axisTick: {
            show: false
          }
        },
        yAxis: {
          name: '',
          min: 0,
          interval: 3600,
          axisTick: {
            show: false
          },
          axisLine: {
            show: false,
            lineStyle: {
              color: '#90979c'
            }
          },
          axisLabel: {
            // y轴坐标时间戳显示转化为时分秒显示
            formatter: function(value, index) {
              var hh
              var mm
              var ss
              // 传入的时间为空或小于0
              if (value == null || value < 0) {
                return
              }
              // 得到小时
              hh = (value / 3600) | 0
              value = parseInt(value) - hh * 3600
              if (parseInt(hh) < 10) {
                hh = '0' + hh
              }
              // 得到分
              mm = (value / 60) | 0
              // 得到秒
              ss = parseInt(value) - mm * 60
              if (parseInt(mm) < 10) {
                mm = '0' + mm
              }
              if (ss < 10) {
                ss = '0' + ss
              }
              return hh + ':' + mm + ':' + ss
            }
          },
          type: 'time',
          splitNumber: 1
        },
        series: [
          {
            name: '发作总时长',
            type: 'line',
            data: this.totalTimeY,
            showSymbol: true,
            smooth: true,
            symbol: 'none',
            lineStyle: {
              normal: {
                color: '#d6fcd7'
              }
            }
          }
        ]
      }
```

* 其中发作总时长是时间戳格式,单位为秒
  ![](https://i.imgur.com/lVcaswZ.gif)

## 修改配置项

修改 tooltip 中的 formatter 配置项,提示框的显示格式以及内容已经改变

```bash
        tooltip: {
          trigger: 'axis',
          formatter: function(data) {
            // console.log(data[0].value)
            let value = data[0].value
            var hh
            var mm
            var ss
            // 传入的时间为空或小于0
            if (value == null || value < 0) {
              return
            }
            // 得到小时
            hh = (value / 3600) | 0
            value = parseInt(value) - hh * 3600
            if (parseInt(hh) < 10) {
              hh = '0' + hh
            }
            // 得到分
            mm = (value / 60) | 0
            // 得到秒
            ss = parseInt(value) - mm * 60
            if (parseInt(mm) < 10) {
              mm = '0' + mm
            }
            if (ss < 10) {
              ss = '0' + ss
            }
            return '时间:' + data[0].name + '<br/>总时长:' + hh + ':' + mm + ':' + ss
          }
        },
```

![](https://i.imgur.com/lN2597q.gif)

## 总结

y 轴和提示窗框的时间戳转化函数可以写成一个公共函数调用,这里方便演示,所以没有提取出来
