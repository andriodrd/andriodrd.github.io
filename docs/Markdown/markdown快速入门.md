[toc]

# Markdown快速入门

## 1、标题语法

```
//标题一共有6个级别，书写方式如下：
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```
## 2、字体
```html
//粗体
**内容**

//斜体
*内容*

//代码高亮显示
==内容==

//删除线
~~内容~~

//字体颜色等属性
<span style='color:文字颜色;background:背景颜色;font-size:文字大小;font-family:字体;'>文字</span>

//设置标题居中
<h1 align = "center">标题居中的方法</h1>

//设置文本居中
<div align = "center">文本居中的方法</div>
```
## 3、引用

```
//引用书写语法如下：

>内容
>>内容
>>>内容
```
## 4、分割线

```
//分隔线（长度：根据内容而定）
---
//分割线（占全屏）
***
```
## 5、图片
```
//图片插入
![图片名称](图片地址-可以本地：也可以网络)
```
## 6、超链接
```
//超链接
[点击跳转到我的GitHub](https://github.com/yerenping)
```
## 7、列表

```
//有序列表：序号+点+空格，生成列表 
1. 首页
2. 分类
3. 标题
//无需列表：- + 空格
- 
```
## 8、表格
```html
// 1.ctrl+t

// 2.表格内容换行
 <br>

// 3.合并表格行
<table>
    <tr>
        <td>张</td>
        <td>王</td>
    <tr>
    <tr>
        <td colspan="2">姓氏</td>
    <tr>
</table>

// 4.合并表格列
<table>
    <tr>
        <td>类别</td>
        <td>名称</td>
    </tr>
    <tr>
        <td rowspan="2">颜色</td>
        <td>红色</td>
    </tr>
    <tr>
        <td>黄色</td>
    </tr>
    <tr>
        <td rowspan="2">姓氏</td>
        <td>张</td>
    </tr>
    <tr>
        <td>王</td>
    </tr>
</table>
```
## 9、标题

```
//[toc]
```

## 10、快捷键

```
标题：ctrl+数字
表格：ctrl+t
生成目录：[TOC]按回车
选中一整行：ctrl+l
选中单词：ctrl+d
选中相同格式的文字：ctrl+e
跳转到文章开头：ctrl+home
跳转到文章结尾：ctrl+end
搜索：ctrl+f
替换：ctrl+h
引用：输入>之后输入空格
加粗：ctrl+b
倾斜：ctrl+i
下划线：ctrl+u
删除线：alt+shift+5
插入图片：直接拖动到指定位置即可或者ctrl+shif t+i
插入链接：ctrl+k
打开通用设置：ctrl+,
```
## 11、常用插件

```shell
// 1.增加 Java 代码高亮
<!--Java代码高亮-->
<script src="//unpkg.com/prismjs/components/prism-java.js"></script>

// 2.增加全文搜索功能
 <!--全文搜索,直接用官方提供的无法生效-->
 <script src="https://cdn.bootcss.com/docsify/4.5.9/plugins/search.min.js">
 
// 3.复制代码到剪切板
<!-- 复制代码到剪贴板 -->
<script src="//unpkg.com/docsify-copy-code"></script>

// 4.图片缩放和字数统计
<!-- 图片缩放 -->
<script src="//unpkg.com/docsify/lib/plugins/zoom-image.js"></script>
<!-- 字数统计 -->
<script src="//unpkg.com/docsify-count/dist/countable.js"></script>

// 5.edit on github
<script>
    window.$docsify = {
      ......
      plugins: [
        EditOnGithubPlugin.create('https://github.com/Snailclimb/JavaGuide-Interview/blob/master/')
      ],
    }
</script>
```



