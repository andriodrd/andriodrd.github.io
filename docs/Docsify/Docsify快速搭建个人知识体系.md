# **Docsify快速搭建个人知识体系**

```
平常写一些文档或者个人笔记时，Markdown 是我的第一个选择，因为它用起来真的很方便、简洁。那么今天要讲的 Docsify 是什么呢？
```

## **Docsify简介**

1. Docsify 是一个动态生成文档网站的工具。不同于 GitBook、Hexo 的地方是它不会生成将 .md 转成 .html 文件，所有转换工作都是在运行时进行。
2. 这将非常实用，如果只是需要快速的搭建一个小型的文档网站，或者不想因为生成的一堆 .html 文件“污染” commit 记录，只需要创建一个 index.html 就可以开始写文档而且直接部署在 GitHub Pages。
3. 中文教程链接地址：https://docsify.js.org/#/zh-cn/

## **全局安装**

安装脚手架工具 docsify-cli，安装过程中较慢的可以切换 npm 源为 [cnpm](https://blog.csdn.net/devops/npm-deploy.md)

```shell
npm i docsify-cli -g
```

## **初始化文档**

注意这里的文件名约定为 docs 也是官方推荐，请按照规则设置，否则发到 Github 可能会出现一些问题

```shell
$ docsify init docs

Initialization succeeded! Please run docsify serve docs
```

执行完以上命令 docs 文件目录下会生成以下 3 个文件：

- `index.html`：入口文件
- `README.md`：会做为主页内容渲染
- `.nojekyll`：用于阻止 GitHub Pages 会忽略掉下划线开头的文件

## **启动本地服务预览**

docs 同级目录下执行以下命令，打开本地服务器，默认地址为：[http://localhost:3000](http://localhost:3000/)

```shell
$ docsify serve docs

Serving /Users/may/Nodejs-Roadmap/docs now.

Listening at http://localhost:3000
```

## **搭建博客**

- **设置封面**

设置我们的封面图，需要两步，首先在 `docs/index.html` 文件中将设置 `coverpage: true`，之后创建 `docs/_coverpage.md`文件

- **定制导航栏**

官方支持两种方式，可以在 HTML 里设置，但是链接要以 #/ 开头，另外一种通过 Markdown 配置导航，我们这里用的也是后者

首先配置 `loadNavbar: true`，之后创建 `docs/_navbar.md`文件.

## **GithubPages预览**

将我们搭建的 Blog 托管到 Github，可以实时访问，在项目的 Settings 里开启 GitHub Pages 功能

选择 dcos 文件目录，如下所示：

![图片描述](assets/16af37b5e9cb7111)

浏览器输入 https://q-angelo.github.io/Nodejs-Roadmap/ 即可访问，q-angelo 为您的用户名，Nodejs-Roadmap 为您的项目名称。