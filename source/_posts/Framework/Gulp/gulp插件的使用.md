---
title: gulp插件的使用
date: 2017-03-06
tags: gulp，
grammar_cjkRuby: true
---

## 常用插件列表
| 插件名称                   | 作用                                               |
| -------------------------- | -------------------------------------------------- |
| del                        | 删除文件或文件夹                                   |
| gulp-less                  | 编译LESS文件                                       |
| gulp-rname                 | 重命名文件                                         |
| gulp-imagemin              | 图片压缩                                           |
| gulp-uglify                | 压缩Javascript                                     |
| gulp-concat                | 合并 js 文件                                       |
| gulp-concat-css            | 合并 css 文件                                      |
| gulp-cssnano               | 压缩 css                                           |
| gulp-htmlmin               | 压缩HTML                                           |
| gulp-rev                   | 添加版本号                                         |
| gulp-rev-collector         | 内容替换                                           |
| gulp-useref                | gulp-if                                            |
| gulp-load-plugins          | 依赖自动加载                                       |
| gulp-useref                | 自动合并打包处理                                   |
| gulp-wrap                  | 包装内容                                           |
| gulp-angular-templatecache | AngularJS 模板缓存                                 |
| browser-sync               | 和 gulp 配合使用实现文件改变执行某个任务后自动刷新 |
| yargs                      | 获取命令行参数                                     |
| gulp-if                    | 根据判断执行某个插件                               |
<!--more-->
### gulp-angular-templatecache
#### 安装插件
```bash
npm install --save-dev gulp-angular-templatecache
```
#### 基本使用
##### `ulpfile.js`
> 连接模板目录中所有.html文件的内容，并保存到public / templates.js（默认文件名）。
```javascript
var gulp = require('gulp');
var templateCache = require('gulp-angular-templatecache');
gulp.task('default', function () {
  return gulp.src('templates/**/*.html')
    .pipe(templateCache())
    .pipe(gulp.dest('public'));
});
```

##### 结果` (public/templates.js)`
> 输出样式（预处理）。
```javascript
angular.module("templates").run([$templateCache,
  function($templateCache) {
    $templateCache.put("template1.html",
      // template1.html content (escaped) 
    );
    $templateCache.put("template2.html",
      // template2.html content (escaped) 
    );
    // etc. 
  }
]);
```
将此文件包含在您的应用程序中，AngularJS将使用$ templateCache（如果可用）。
**注意**：这个插件不会默认创建一个新的AngularJS模块，但需要调用templates模块。如果想要创建新模块，请将 `options.standalone` 设置为true。

**注意**：如果在Windows上使用Visual Studio，您可能会遇到此错误消息：ASPNETCOMPILER : error ASPRUNTIME: The specified path, file name, or both are too long. The fully qualified file name must be less than 260 characters, and the directory name must be less than 248 characters.
这很可能是由于路径名太长，可以通过在 `lodash.bindpackage.json` 中添加一个 `dev dependecy` 来修复。无论如何，如果你遇到这个错误，请在＃62中删除一个注释，我们可以合并＃63。
 #### API
##### gulp-angular-templatecache(filename, options)

1. filename - {string} [filename='templates.js']
	> Name to use when concatenating.
2. options
	- root - {string}
	> Prefix for template URLs.

	- module - {string} [module='templates']
	> Name of AngularJS module.

	- standalone - {boolean} [standalone=false]
	> Create a new AngularJS module, instead of using an existing.

	- base {string | function} [base=file.base]
	> Override file base path.

	- moduleSystem {string}
	> Wrap the templateCache in a module system. Currently supported systems: RequireJS, Browserify, ES6 and IIFE (Immediately-Invoked Function Expression).

	- transformUrl {function}
	> Transform the generated URL before it's put into $templateCache. 
```javascript
transformUrl: function(url) {
    return url.replace(/\.tpl\.html$/, '.html')
}
```
	- templateHeader {string} [templateHeader=see below]
	> Override template header. 
```javascript
var TEMPLATE_HEADER = 'angular.module("<%= module %>"<%= standalone %>).run(["$templateCache", function($templateCache) {';
```
	- templateBody {string} [templateBody=see below]
	> Override template body. 
```javascript
var TEMPLATE_BODY = '$templateCache.put("<%= url %>","<%= contents %>");'; 
```
	- templateFooter {string} [templateFooter=see below]
	> Override template footer.
```javascript
var TEMPLATE_FOOTER = '}]);';
```