---
title: vue小技巧
date: 2017-07-13 12:44:44
tags:
  - vue
  - JavaScript
categories: 
  - 笔记
---

关于 vue 的一些小技巧

<!-- more -->

## 路由懒加载

使用普通的 import 路由导出方式，打包时会打包到一个 js 文件中，但当项目大的时候，js 文件夹会过大，导致页面初始化加载时非常慢：

```bash
	import Vue from 'vue'
	import App from './App'
	import VueRouter from 'vue-router';

	import about from './components/about/about.vue'
	import join from './components/join/join.vue'

	Vue.use(VueRouter)

	const routes = [
		{
			path: '/about',
    		component: about
		},
		{
			path: '/join',
    		component: join
		}
	]

	const router = new VueRouter({
	  routes
	}

	new Vue({
	  el: '#app',
	  template: '<App/>',
	  components: {App},
	  router
	})
```

---

这时使用路由懒加载模式，可以按需加载路由，优化页面的加载速度：

```bash
	import Vue from 'vue'
	import App from './App'

	import VueRouter from 'vue-router';

	const home = resolve => require(['./components/home/home.vue'], resolve);
	const about = resolve => require(['./components/about/about.vue'], resolve);

	Vue.use(VueRouter);
```

---

但是在开发时，过多的路由懒加载，会导致修改代码，页面热更新时的速度非常慢，不利于开发，这时可以自定义封装一个方法，例如\_import()方法，在正式环境下才使用懒加载：

```bash
	import Vue from 'vue'
	import App from './App'

	import VueRouter from 'vue-router';

	const _import = require('./_import_' + process.env.NODE_ENV);

	const home = _import('./components/home/home.vue');
	const about = _import('./components/about/about.vue');

	Vue.use(VueRouter);
```

以上笔记借鉴出处[https://segmentfault.com/a/1190000010043013#articleHeader1](https://segmentfault.com/a/1190000010043013#articleHeader1)

---

## vue 倒计时功能 demo

```bash
	<div id="app">
        <button @click="countDown" :disabled="disabled">{{countdown}}</button>
    </div>

	new Vue({
        el: '#app',
        data() {
            return {
                disabled: false,
                countdown: '点击倒计时',
                time: 10
            }
        },
        methods: {
            countDown() {
                let that = this
                let init = setInterval(function () {
                    that.countdown = '剩余(' + that.time + ')秒'
                    that.time--
                    that.disabled = true
                    if (that.time < 0) {
                        that.countdown = '点击倒计时'
                        that.disabled = false
                        that.time = 10
                        clearInterval(init)
                    }
                },1000)
            }
        }
    })
```

![](http://i.imgur.com/ewkdj8j.png)

---

## 设置 vue 全局过滤器 filter

新建一个 filter.js 文件，内容如下：

```bash
	export function fromTime(now) {
	  var date = new Date(now);
	  var seperator1 = "-";
	  var seperator2 = ":";
	  var month = date.getMonth() + 1;
	  var strDate = date.getDate();
	  if (month >= 1 && month <= 9) {
	    month = "0" + month;
	  }
	  if (strDate >= 0 && strDate <= 9) {
	    strDate = "0" + strDate;
	  }
	  var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate;
	  return currentdate;
	}
```

在 main.js 文件中，内容如下：

```bash
	import * as filters from './filters/filters.js' // 全局vue filter
	Object.keys(filters).forEach(key => {
	  Vue.filter(key, filters[key])
	});
```

或者：

```bash
	import { fromTime } from './filters/filters.js'
	Vue.filter('fromTime', fromTime)
```

在模板中直接使用，内容如下：

```bash
	<tr class="item" v-for="x in recurrenceList" :key="x">
		<td>预后随访{{x.id}}</td>
        <td>{{x.createTime | fromTime}}</td>
	</tr>
```

## 注册全局组件

在 main.js 文件中,写入一下内容：

```bash
// 单个组件注册
import loading from './components/loading'
Vue.component('v-loading', loading)
```

或者多个组件注册：

```bash
// 通过components下的index.js文件导入组件
import components from './components/';
// 对导入的组件进行全局组件注册
Object.keys(components).forEach((key)=>{
  Vue.component(key,components[key])
})
```

在模板中直接使用，内容如下：

```bash
<template>
  <div class="header">
    <v-loading v-if="loading" :text="'退出'"></v-loading>
  </div>
</template>
```
