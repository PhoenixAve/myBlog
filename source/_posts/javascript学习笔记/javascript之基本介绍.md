---
title: javascript之基本介绍
date: 2015-03-05 21:23:08
tags: javascript
---

## javascript语言特点
1. javascript是一门弱类型、动态类型、解析型的脚本语言
	-  弱类型：定义变量不需要指定变量类型
	- 动态类型：
		- 执行时才知道变量到底是什么类型
		- 对象想要有属性或方法，直接 **.** 或者["属性名"]就可以
	- 解析型：一行一行解析，解析一行执行一行
	- 脚本语言：不需要编译，直接运行时边解析边执行的语言
		- 编译：一次性把代码转换成cpu可以看懂的语言，一行一行执行，执行速度快
		- 解释：一行一行解析，解析一行执行一行，执行速度慢 
2. javascript是一种客户端的脚本语言

<!-- more -->

## 浏览器的工作原理
- 浏览器基本组成
	- 用户界面 - 包括地址栏、前进/后退按钮、书签菜单等。除了浏览器主窗口显示的你请求的页面外，其他显示的各个部分都属于用户界面。
	- 浏览器引擎 - 在用户界面和渲染引擎之间传送指令。
	- 渲染引擎 - 负责显示请求的内容。如果请求的内容是 HTML，它就负责解析 HTML 和 CSS 内容，并将解析后的内容显示在屏幕上。
	- 网络 - 用于网络调用，比如 HTTP 请求。其接口与平台无关，并为所有平台提供底层实现。
	- 用户界面后端 - 用于绘制基本的窗口小部件，比如组合框和窗口。其公开了与平台无关的通用接口，而在底层使用操作系统的用户界面方法。
	- JavaScript 解释器。用于解析和执行 JavaScript 代码，比如chrome的javascript解释器是V8。
	- 数据存储。这是持久层。浏览器需要在硬盘上保存各种数据，例如 Cookie。新的 HTML 规范 (HTML5)定义了“网络数据库”，这是一个完整（但是轻便）的浏览器内数据库。

![浏览器工作原理](img/20160313105554184.jpg)

[详细文章请移步](http://blog.csdn.net/dangnian/article/details/50876241)

## javascript组成部分
1. ECMAScript： javascript的语法规范
2. DOM： javascript操作网页上元素的API
3. BOM： javascript操作浏览器部分功能的API

## javascript的使用方式
1. 内嵌式（html页面编写javascript代码）
```javascript?linenums
<script type="text/javascript">
		// 你的javascript代码
</script>
```
2. 外联式（在js文件中编写javascript代码，在html页面中引入js文件）
	- src：引入文件的地址
	- type：引入的文件类型
		- 写法一：type="text/javascript"
		- 写法二：language="javascript"
	- async：异步
		- 交给浏览器去异步下载，不会阻塞程序的执行
		- js文件下载完就可以立马执行
	- sync：同步
		- 只能一个任务一个任务的执行会阻塞程序的执行
	- defer：
		- 等html文档加载并显示完之后才执行下载完的js代码
		- 只有外部脚本才可以使用
		
```javascript?linenums
<script src="/javascripts/application.js" type="text/javascript" charset="utf-8" async defer></script>
```

3. 内联式（在html标签中编写javascript代码）
```javascript?linenums
<input type="buttom" onclick="function(){}">
```

## javascript注释
注释的应用
	- 一般用于解释某些复杂代码的逻辑，方便后期的维护与开发
	- 用于对整个模块进行分割划分，方便于代码查找和维护
	- 用于：模块、函数、复杂逻辑注释、文件注解、维护记录等
```javascript?linenums
//单行注释
/*多行注释*/
/**
  * 注解内容
  */
```
好了，现在你可以开始编写你的javascript代码了
