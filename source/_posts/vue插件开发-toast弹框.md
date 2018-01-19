---
title: vue插件开发-toast弹框
date: 2018-01-17 09:22:59
tags:
  - vue
	- JavaScript
categories: 
  - 工作
  - 学习
---

最近接手了一个移动端的项目，基于 vue + muse-ui 开发。其中 muse-ui 的 toast 插件的样式以及功能不是太适合这个项目，所以自己动手开发了一个简单的 toast 插件。

<!-- more -->

> Vue插件添加方法有4种，这里采用第四种，[参考官方文档](https://cn.vuejs.org/v2/guide/plugins.html "参考官方文档")
>* 1、添加全局方法或者属性
>* 2、添加全局资源：指令/过滤器/过渡等
>* 3、通过全局 mixin 方法添加一些组件选项
>* 4、添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现
>* 5、一个库，提供自己的 API，同时提供上面提到的一个或多个功能

## 技术栈

vue

## 功能需求

* 提示框位置可改变
* 提示框内容可编辑
* 提示框类型可改变

## 注
* [源码地址](https://github.com/hileslie/vue-test)

## 在 component 文件夹中新建一个 toast 文件夹,新建 toast.js 和 toast.css 文件

* toast.js 文件

```bash
import './toast.css'
var Toast = {}
Toast.install = function(Vue) {
  // 默认配置
  let opt = {
    content: 'dsb',
    position: 'top',
    duration: 3000
  }
  Vue.prototype.$toast = (options, type) => {
    if (!type) {
      type = 'info'
    }
    if (options) {
      opt.content = options.content ? options.content : options
      opt.position = options.position ? options.position : opt.position
      opt.duration = options.duration ? options.duration : opt.duration
    }
    if (document.getElementsByClassName('vue-toast').length) {
      return
    }
    // 创建构造器,定义好提示信息的模板
    let toastTpl = Vue.extend({
      template: `
        <div class="vue-toast vue-toast-${opt.position}">
          <i class="vue-toast-i vue-toast-${type}">${type}: </i>
          <span class="vue-toast-span">${opt.content}</span>
        </div>
      `
    })
    // 创建实例,挂载到文档上
    let tpl = new toastTpl().$mount().$el
    // 将实例添加到body中
    document.body.appendChild(tpl)
    // 销毁dom
    setTimeout(() => {
      document.body.removeChild(tpl)
    }, opt.duration)
    // 还原默认配置
    opt = {
      content: 'dsb',
      position: 'top',
      duration: '3000'
    }
  }
  // 提示消息类型
  const messageType = ['info', 'success', 'warning', 'error']
  messageType.forEach(type => {
    Vue.prototype.$toast[type] = options => {
      return Vue.prototype.$toast(options, type)
    }
  })
}
export default Toast
```

* toast.css 文件

```bash
.vue-toast {
  position: fixed;
  left: 50%;
  word-wrap: break-word;
  padding: 10px;
  text-align: center;
  z-index: 9999;
  font-size: 0.3rem;
  max-width: 80%;
  color: #fff;
  border-radius: 5px;
  background: rgba(0, 0, 0, 0.7);
  overflow: hidden;
}
.vue-toast-top {
  top: 10%;
}
.vue-toast-middle {
  top: 50%;
}
.vue-toast-bottom {
  top: 90%;
}
@-webkit-keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
.vue-toast {
  -webkit-animation-name: fadeIn; /*动画名称*/
  -webkit-animation-duration: 0.5s; /*动画持续时间*/
  -webkit-animation-iteration-count: 1; /*动画次数*/
  -webkit-animation-delay: 0s; /*延迟时间*/
}
```

## 在 main.js 文件中引用 toast

```bash
import Toast from '@/components/toast/toast.js'
Vue.use(Toast)
```

## 使用 toast 插件

```bash
<template>
  <div class="toast">
    <button @click="info">info</button>
    <button @click="infoMiddle">infoMiddle</button>
    <button @click="infoBottom">infoBottom</button>
    <button @click="success">success</button>
    <button @click="warning">warning</button>
    <button @click="error">error</button>
  </div>
</template>
<script>
export default {
  data() {
    return {}
  },
  methods: {
    info() {
      this.$toast.info('hello leslie')
    },
    infoMiddle() {
      this.$toast.info({
        content: 'hello leslie',
        position: 'middle'
      })
    },
    infoBottom() {
      this.$toast.info({
        content: 'hello leslie',
        position: 'bottom',
        duration: 2000
      })
    },
    success() {
      this.$toast.success('hello leslie')
    },
    warning() {
      this.$toast.warning('hello leslie')
    },
    error() {
      this.$toast.error('hello leslie')
    }
  }
}
</script>
```

## 演示

![](https://i.imgur.com/VRQ2rg5.gif)
