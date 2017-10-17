# vue

- 全局安装脚手架：sudo npm install -g vue-cli vue-router vuex vue-resource vue-loader webpack
- 以webpack模板初始化项目： vue init webpack sell：程序文件名称
- 模块安装：npm insall
- 运行开发者模式：npm run dev(代码实时更新)
- 打包文件：npm run build，包含index文件与dist资源包

## 项目：

- [vuejs/vue](A progressive, incrementally-adoptable JavaScript framework for building UI on the web. http://vuejs.org):A progressive, incrementally-adoptable JavaScript framework for building UI on the web. http://vuejs.org
- [webpack-simple](https://github.com/vuejs-templates/webpack-simple)
- [pwa](https://github.com/vuejs-templates/pwa) progressive-web-apps
- [vuejs-templates/webpack](https://github.com/vuejs-templates/webpack):通过webpack打包的vuejs模版
- [vue2-happyfri](https://github.com/bailicangdu/vue2-happyfri)vue2 + vue-router + vuex 入门项目
- [vue2-elm](https://github.com/bailicangdu/vue2-elm)基于 vue2 + vuex 构建一个具有 45 个页面的大型单页面应用
- [vuejs/vue-router](https://github.com/vuejs/vue-router):The official router for Vue.js.
- [pagekit/vue-resource](https://github.com/pagekit/vue-resource):The HTTP client for Vue.js
- [vuejs/vue-hackernews-2.0](https://github.com/vuejs/vue-hackernews-2.0):HackerNews clone built with Vue 2.0, vue-router & vuex, with server-side rendering
- [liangxiaojuan/eleme](https://github.com/liangxiaojuan/eleme):vue2 +vue-router2 + es6 +webpack 高仿饿了么app商家详情，demo：http://yangyi1024.com/elem 还有我最新的实战项目,点它=》 http://yangyi1024.com/meizi
- [ustbhuangyi/vue-sell](https://github.com/ustbhuangyi/vue-sell):Vue.js高仿饿了么外卖App课程源码 http://coding.imooc.com/class/74.html

## 重构

- [tonyfree/youzan](https://github.com/tonyfree/youzan)

### App 流程

- 需求分析
- 脚手架工具
- 数据mock
- 架构设计
  - 模块拆分
  - 组件抽象
- 代码编写
- 自测
- 编译打包

### 组件

- vue-resource
- vue-router
- better-scroll

- 工程化

- 模块化

- 组件化

## 使用

* 定义View
* 定义Model
* 创建一个Vue实例或"ViewModel"，它用于连接View和Model

### 语法

```
<input type="text" v-model="message"/>  //创建双向数据绑定
<h1 v-if="age >= 25">Age: {{ age }}</h1> //  条件渲染指令，它根据表达式的真假来删除和插入元素
<h1 v-show="age >= 25">Age: {{ age }}</h1> //  指令的元素始终会被渲染到HTML，它只是简单地为元素设置CSS的style属性。
用v-else指令为v-if或v-show添加一个“else块”。v-else元素必须立即跟在v-if或v-show元素的后面——否则它不能被识别。
v-for="item in items" // v-for指令基于一个数组渲染一个列表
v-bind:argument="expression"  // 指令可以在其名称后面带一个参数，中间放一个冒号隔开，这个参数通常是HTML元素的特性（attribute） 简写为 ：
<a v-on:click="doSomething">   // v-on指令用于给监听DOM事件，它的用语法和v-bind是类似的，例如监听<a>元素的点击事件   简写为@

```

数据绑定最常见的形式就是使用 "Mustache" 语法：{{}}

### 特点

- 数据驱动
- 组件化

### npm源替换`nmp install -g --registery= https://registery.npm.taobao.org`

### webstrom设置

- 添加vuejs插件
- File Types配置 将.vue格式的文件注册为HTML文件类型

### 添加插件

- package.json中添加"stylus-loader": "^1.4.0"，npm install安装插件

## 组件

* [Vuex](https://vuex.vuejs.org/zh-cn/):Centralized State Management for Vue.js.
* [ElemeFE/vue-amap](https://github.com/ElemeFE/vue-amap):vue-amap - 基于 Vue 2.x 和高德地图的地图组件 https://elemefe.github.io/vue-amap/

## 工具

- [vetur](https://github.com/vuejs/vetur)：Vue tooling for VSCode.
- [vuejs/vue-loader](https://github.com/vuejs/vue-loader):Webpack loader for Vue.js components
- [vuejs/vue-test-utils](https://github.com/vuejs/vue-test-utils):Utilities for testing Vue components https://vue-test-utils.vuejs.org
- [vuejs/vue-class-component](https://github.com/vuejs/vue-class-component):ES / TypeScript decorator for class-style Vue components.
- [vuejs/vue-devtools](https://github.com/vuejs/vue-devtools):Chrome devtools extension for debugging Vue.js applications.
- [vuejs/vue-cli](https://github.com/vuejs/vue-cli):Simple CLI for scaffolding Vue.js projects
- [vuejs-templates/webpack](https://github.com/vuejs-templates/webpack):A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.

## 参考

* [Vue.js——60分钟快速入门](http://www.cnblogs.com/keepfool/p/5619070.html)
* [官方文档](https://cn.vuejs.org/v2/guide/)
* [awesome-vue](https://github.com/vuejs/awesome-vue):A curated list of awesome things related to Vue.js


## UI

* [ElemeFE/mint-ui](https://github.com/ElemeFE/mint-ui):Mobile UI elements for Vue.js http://mint-ui.github.io/#!/en
* [ElemeFE/element](https://github.com/ElemeFE/element):A Vue.js 2.0 UI Toolkit for Web http://element.eleme.io/
* [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin):vue2.0 admin / a management system template http://panjiachen.github.io/vue-element-admin
* [vue-bulma/vue-admin](https://github.com/vue-bulma/vue-admin):Vue Admin Panel Framework, Powered by Vue 2.0 and Bulma 0.3 https://admin.vuebulma.com

## todo

- [JavaScript 进阶之 Vue.js + Node.js 入门实战开发](http://blog.csdn.net/gitchat/article/details/77931664)
- http://www.cnblogs.com/keepfool/
