---
title: webpack打包后如何修改配置项
date: 2018-03-12 17:21:43
tags:
  - vue
  - JavaScript
categories: 
  - 工作
---

以vue-cli为例，当前端将项目使用打包后，生成的dist文件是写死的。当服务器迁移或项目迁移时，会需要修改后端请求api。这样只能通过重新修改前端源码、编译打包，再发布服务器，不利于开发。

<!-- more -->

## 解决方法
使用generate-asset-webpack-plugin插件，在webpack.prod.conf.js中去生成configServer.json文件，让其在build的时候生成json文件，然后再使用axios异步获取json，替换url即可。

## 具体步骤
* 安装generate-asset-webpack-plugin插件 
`npm install --save-dev generate-asset-webpack-plugin `

* 在webpack.prod.conf.js里面配置配置文件
```bash
    // 引用
    var GenerateAssetPlugin = require('generate-asset-webpack-plugin'); 
    var createServerConfig = function(compilation){
    let cfgJson={ApiUrl:"http://139.129.31.108:8001"};
    return JSON.stringify(cfgJson);
    }

    // 在plugins:[]中配置
    new GenerateAssetPlugin({
        filename: 'serverconfig.json',
        fn: (compilation, cb) => {
            cb(null, createServerConfig(compilation));
        },
        extraFiles: []
    })
```

* 调用
```bash
    axios.get("serverconfig.json").then((result)=>{
        localStorage.setItem('ApiUrl',result.data.ApiUrl);
        console.log(localStorage.getItem('ApiUrl'));
    }).catch((error)=>{console.log(error)});
```

## 实际操作
![](https://i.imgur.com/SiaUXhK.png)

### 问题
* 修改serverconfig.json后，请求并没有刷新

### 解决
* 因为axios请求了缓存中的数据，所以修改文件后，页面并没有获取到新数据，所以需要在调用函数是加上随机数。
```bash
    axios.get("serverconfig.json?rand=" + new Date().getTime()).then((result)=>{
        localStorage.setItem('ApiUrl',result.data.ApiUrl);
        console.log(localStorage.getItem('ApiUrl'));
    }).catch((error)=>{console.log(error)});
```
![](https://i.imgur.com/QathqLO.jpg)![](https://i.imgur.com/osm8urQ.png)