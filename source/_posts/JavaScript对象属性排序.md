---
title: JavaScript对象属性排序
date: 2017-10-19 12:44:44
tags:
  - JavaScript
categories: 
  - 笔记
---
>对象是Object类型的成员。它是一个无序的属性集合，每个属性都包含一个原始值，对象或函数。存储在对象的属性中的函数称为方法。所以JavaScript中不能保证对象中的属性顺序，需要用数组的方式解决顺序问题。映射以插入顺序迭代其元素，而不为对象指定迭代顺序。

<!-- more -->

### 解决问题
文章对象按时间倒叙排列

### 实例
#### 原函数
``` bash
    const articlesArr = [
      {
        date: '2017-07-13',
        name: 'xxx'
      },
      {
        date: '2017-09-21',
        name: 'yyy'
      },
      {
        date: '2016-06-03',
        name: 'zzz'
      },
      {
        date: '2015-02-13',
        name: 'vvv'
      },
    ]
    const time = ['2017','2016','2015']
    let archives = {}
    for (let i = 0, _len = time.length; i < _len; i++) {
      archives[time[i]] = []
      for (let j = 0, _len = articlesArr.length; j < _len; j++) {
        if (time[i] === articlesArr[j].date.substring(0, 4)) {
          archives[time[i]].push(articlesArr[j])
        }
      }
    }
    console.log(archives)
    //  结果并不是期望的从2017开始排序，并且2017的数组对象也不是按时间倒叙
    archives = {
      2015: [
        {date: "2015-02-13", name: "vvv"}
      ],
      2016: [
        {date: "2016-06-03", name: "zzz"}
      ],
      2017: [
        {date: "2017-07-13", name: "xxx"},
        {date: "2017-09-21", name: "yyy"}
      ]
    }
```

#### 解决方案
``` bash
    let archivesArr = []  //  存放的数组
    for (let i = 0, _len = time.length; i < _len; i++) {
      archives[time[i]] = []
      for (let j = 0, _len = articlesArr.length; j < _len; j++) {
        if (time[i] === articlesArr[j].date.substring(0, 4)) {
          archives[time[i]].push(articlesArr[j])
        }
      }
      archives[time[i]].sort(function (a, b) {  // 对每个对象中数组内的值按时间倒叙
        return Date.parse(b.date) - Date.parse(a.date)
      })
      archivesArr.unshift(archives[time[i]]) //  再将原对象的属性值按序添加到数组
    }
    console.log(archivesArr)
    //  2017的数组对象已按按时间倒叙
    archives = {
      2015: [
        {date: "2015-02-13", name: "vvv"}
      ],
      2016: [
        {date: "2016-06-03", name: "zzz"}
      ],
      2017: [
        {date: "2017-09-21", name: "xxx"},
        {date: "2017-07-13", name: "yyy"}
      ]
    }
    // archivesArr是期望的按时间顺序倒叙
    archivesArr = [
      [
        {date: "2017-09-21", name: "yyy"},
        {date: "2017-07-13", name: "xxx"}
      ],
      [
        {date: "2016-06-03", name: "zzz"}
      ],
      [
        {date: "2015-02-13", name: "vvv"}
      ]
    ]
```    
