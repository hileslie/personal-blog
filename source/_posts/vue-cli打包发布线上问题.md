---
title: vue-cli打包上线
date: 2017-06-13 12:44:44
tags:
  - vue
	- JavaScript
categories: 
  - 工作
---
vue-cli打包上线相关问题

<!-- more -->

### 问题：
 vue-cli直接npm run build，打包生成dist文件，部署到服务器，会出现页面空白，并报错的问题![](http://i.imgur.com/0YY2TIp.png)

### 问题来源：
我们会看到css文件和js文件没有引入，打开dist文件夹下的index.html文件，发现css和js，并没有加引号，导致没有引入![](http://i.imgur.com/cN9M4Ti.png)

### 解决方法：
找到build文件夹下的webpack.prod.conf.js文件，修该removeAttributeQuotes，将其设置成false，![](http://i.imgur.com/i4fP7MI.png)修改后引号自动加上去了，打包出来的文件仍然有问题，引入路径还是显示错误![](http://i.imgur.com/R6vmUKG.png)接下来找到config文件夹下的index.js文件，将build中的assetsPublicPath: '/',修改为assetsPublicPath: './',dev不修改，不然npm run dev 会出错，![](http://i.imgur.com/3hOnGWf.png)我们就可以看到，css和js文件都已引入![](http://i.imgur.com/5p13qEt.png)仍有问题存在，背景图和某些图片没有加载出来![](http://i.imgur.com/FBXXPY9.png)这是因为图片的位置错误，应该放到与src文件夹同级的static静态文件内，并且图片的大小大于10KB![](http://i.imgur.com/9VywE52.png)url-loader配置，test是正则匹配规则，匹配项目中所有的以正则规则结尾的格式的文件，直白点就是所以的图片（png，jpg，jpeg，gif，svg）。然后用url-loader进行处理。处理也有个规则如下，当不大于10000B的文件进行base64转码，就是将图片转为base64的格式。如果超过10KB的图片就单独打包到utils.assetsPath(‘img/[name].[hash:7].[ext]’) 这个目录下,当我们创建了一个这样的目录之后，所以的图片访问路径就成了对应的static/img/'图片名'。到这里就可以确定，如果小于10KB的图片转为base64，大于10KB的图片已经将图片路径改为了static/img/'图片名'。
index.html走static/img/'图片名'是能正确访问到图片的，所以img的路径没问题，然后app.css访问static/img/'图片名'是访问错误的，因为在css目录下并没有static目录。这样就造成了路径访问失败的问题。

使用绝对路径（建议）：将图片等静态资源全放在static文件内，然后使用绝对路径引用

	<img src='/static/img/...'>
	background:url('/static/img/...)

使用小图片作为背景图片（建议）：
将小于10KB的图片作为背景图片，如果有大于10KB的图片作为img图片。使用小图片作为背景图片，大图片用img标签。首先得分清背景图片和图片img的一些区别，就各人理解而言，背景图片是用来修饰网页的，与实际内容无关的东西，使用背景图片。如果与内容有关的东西都应该使用img标签算作网页结构的内容。修饰的图片尽量的小，也可以使用图片压缩等策略减小图片大小。

修改url-loader的limit值（不建议）：
从上面分析可知，当图片转为base64就没有路径错误的问题，保证自己的背景图片都能转为base64就可以防止该错误发生,将limit的值改为你的背景图最大那一张的值还大一点就行，换算为B的单位。不建议修改limit值的原因是，url-loader的配置是针对整个项目的图片，修改了limit值也等于让html中img标签的图片也跟着进行了base64的转换，而对于base64的转换的缺点是他会增大图片原本的体积，如果对大图进行了转base64会造成你的js文件过大，从而增加了加载js时间过长。

最后图片就可以加载出来了![](http://i.imgur.com/BpVu00W.png)


***
另注：当所有配置都正确时，页面有时也会整个显示空白，但js和css都已加载进来，也无报错![](http://i.imgur.com/Z5U1hqa.png)

解决方法：配置路由是时去掉

	mode: 'history'
详情参考[https://router.vuejs.org/zh-cn/essentials/history-mode.html](https://router.vuejs.org/zh-cn/essentials/history-mode.html)
