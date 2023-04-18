# Vue入门
## Vue简介

Vue是于2013年(与React框架同年发布)推出的一个渐进式、自底向上的前端框架，它的作者叫尤雨溪。那么什么叫做渐进式框架呢？比较官方的说法就是：以Vue内核作为核心，随着业务的深入、需求的递增，可以使用其周边生态(vue-router、vuex、ssr等)深度应用到项目中。那么通俗上来讲：就是我们可以使用vue的部分功能不断的迭代掉我们项目中部分的功能，从表单提交到列表渲染，再到多路由应用，再到SSR等。

​ Vue主要具备以下几个特点：

解耦视图和数据
组件复用
前端路由
状态管理
虚拟DOM
Vue的学习不需要你具备 Rect、Angular的基础，只需要具备HTML、CSS、Javascript的基础即可。


## 安装Vue

Vue的安装主要有三种方式：

CDN引入
下载本地引入
vue-cli脚手架的方式

```html
<!-- CDN的两种引入方式 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!-- 或 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```
```html
<!-- 下载本地然后引用 -->
<script src="./lib/js/vue.js"></script>
```