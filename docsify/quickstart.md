# 快速开始

推荐全局安装 `docsify-cli` 工具，可以方便地创建及在本地预览生成的文档。

```bash
npm i docsify-cli -g
```

## 初始化项目

如果想在项目的 `./docs` 目录里写文档，直接通过 `init` 初始化项目。

```bash
docsify init ./docs
```

## 开始写文档

初始化成功后，可以看到 `./docs` 目录下创建的几个文件

- `index.html` 入口文件
- `README.md` 会做为主页内容渲染
- `.nojekyll` 用于阻止 GitHub Pages 忽略掉下划线开头的文件

直接编辑 `docs/README.md` 就能更新文档内容，当然也可以[添加更多页面](zh-cn/more-pages.md)。

## 本地预览

通过运行 `docsify serve` 启动一个本地服务器，可以方便地实时预览效果。默认访问地址 http://localhost:3000 。

```bash
docsify serve docs
```

?> 更多命令行工具用法，参考 [docsify-cli 文档](https://github.com/docsifyjs/docsify-cli)。

## 手动初始化

如果不喜欢 npm 或者觉得安装工具太麻烦，我们可以直接手动创建一个 `index.html` 文件。

*index.html*

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

如果你的系统里安装了 Python 的话，也可以很容易地启动一个静态服务器去预览你的网站。

```python2
cd docs && python -m SimpleHTTPServer 3000
```

```python3
cd docs && python -m http.server 3000
```

## Loading 提示

初始化时会显示 `Loading...` 内容，你可以自定义提示信息。


```html
  <!-- index.html -->

  <div id="app">加载中</div>
```

如果更改了 `el` 的配置，需要将该元素加上 `data-app` 属性。

```html
  <!-- index.html -->
  <div data-app id="main">加载中</div>

  <script>
    window.$docsify = {
      el: '#main'
    }
  </script>
```

对比 [el 设置](zh-cn/configuration.md#el)。


## 创建更多页面
要创建多个页面，只要在docs文件夹下直接创建md文档即可。

假设我们要创建以下文档，目录结构如下:
```
|—— docs
  |-- REAMD.md
  |-- 自定义.md
  |-- 指导文档
       |-- READMD.md
       |-- 部署.md
```
那么其对应的 url 地址为（假设当前的服务器地址为http://localhost:3000）:
```
docs/README.md ==> http://localhost:3000
docs/自定义.md  ==>  http://localhost:3682/#/?id=自定义
docs/指导文档/README.md ==>  http://localhost:3000/#/?id=指导文档/
docs/指导文档/部署.md ==> ???显示不出来
```
## 侧边菜单栏
在docs目录下新建_sidebar.md文件，以配置侧边栏。

  1.在index.html文件中，设置loadSidebar属性为true
```html
<!-- index.html -->

<script>
  window.$docsify = {
    loadSidebar: true,
  };
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```
  2.编写侧边栏文件_sidebar.md
> 文档中必须写入内容，否则显示为 404 NOT FOUND
```
- [首页](/)
- [快速入门](快速入门.md)
```
如果当前目录下没有.nojekll文件，那么需要手动创建。

## 为页面设计标题
我们可以使用如下方式设置页面的标题。页面的标题会显示在 tab 栏顶部，同时这样做，也有利于 SEO。
```
- [首页](/)
- [快速入门](快速入门.md "从这里开始")
```
从这里开始将显示在页面 tab 的标题栏中

## 文档内容目录
创建好_sidebar.md文件以后，导航侧边栏会自动生成文档的导航。但是如果要文档内内容也出现在导航栏中，需要在index.html文件中做如下配置：
```html
<script>
  window.$docsify = {
    loadSidebar: true,
    subMaxLevel: 2,
  };
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```
subMaxLevel 为导航侧边栏菜单的级数，这里设置为自动生成 2 级，我们也可以设置更大的数字来生成更加详尽的导航目录。

### 忽视内容目录
如果我们不想内容目录出现在侧边导航栏里，我们只需要在标题右侧如下配置：
```
### 忽视内容目录 {docsify-ignore}

如果我们不想内容目录出现在侧边导航栏里，我们只需要在标题右侧如下配置：
```

如果该页面不需要生成内容目录，那么我们需要在该页面最高层级标题，做如下配置：

```
## 最高层级标题 {docsify-ignore-all}

若干文字
...

（```）

`{docsify-ignore-all}`和`{docsify-ignore}`都不会显示在文档中。
（```）
```