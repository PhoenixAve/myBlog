---
title: Gulp构建工具的使用
date: 2017-03-04 21:50:52
tags: gulp
grammar_cjkRuby: true
---

> **Automate and enhance your workflow**
> *gulp is a toolkit for automating painful or time-consuming tasks in your development workflow, so you can stop messing around and build something.*

## 简介
本文主要是根据[gulp中文网文档](http://www.gulpjs.com.cn/docs/api/)和[gulp教程之gulp中文API](http://www.ydcss.com/archives/424)的内容综合而成，方便自己理解与学习`gulp`自动化工具的基本使用

<!-- more -->

## 工具介绍
1. `Gulp`是前端开发过程中一种基于流的代码构建工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，不仅可以很愉快的编写代码，而且大大提高我们的工作效率。
2. 项目构建是指项目上线之前对项目源代码进行一系列处理，使其以最佳的形式运行于线上服务器。
3. 常见处理任包括以下几方面：
	- 模块化开发可以实现功能的复用并解决模块间的依赖关系，但带来好处的同时也使得功能代码的碎片化（若干文件）程度增加。
	- 使用`less`、`sass`等预处理器，可以降低`CSS`的维护成本，最终需要将这些预处理器编译成`css`文件；
	- 对静态资源（`css`、`js`、`html`、`images`）压缩合并可以提升网页打开速度，提高性能；
4. 以上任务完如果完全靠手动来完成是非常耗时耗力的且容易出错，实际开发通常借助构建工具来实现。所谓构建工具是指通过一系简单配置就可以帮我们实现合并、压缩、校验、预处理等一系列任务的软件工具。常见的构建工具包括：`Grunt`、`Gulp`、`F.I.S`（百度出品）、`webpack` 等。
5. `Gulp`是基于`Nodejs`开发的一个构建工具，借助`gulp`插件可以实现不同的构建任务，其以简洁的配置和卓越的性能成为目前主流的构建工具。

## 相关链接
>官方：http://gulpjs.com/
> 中文官网：http://www.gulpjs.com.cn/
> npm：https://www.npmjs.com/package/gulp
> Github：https://github.com/gulpjs/gulp
> Gitbook：https://wizardforcel.gitbooks.io/gulp-doc/content/2.html

## 安装

1. 首先要全局安装`gulp-cli`工具
	```bash
	npm install --global gulp-cli
	```
2. 安装全局`gulp`
	```bash
	npm install --global gulp
	```
3. 作为项目的开发依赖（`devDependencies`）安装
	```bash
	npm install --save-dev gulp
	```
4. 在项目根目录下创建一个名为 `gulpfile.js` 的文件：
	```javascript?linenums
	var gulp = require('gulp'); // 引入gulp这个第三方包
	gulp.task('default', function() {
	  // 将你的默认的任务代码放在这
	});
	```
5. 运行 `gulp`
	```bash
	gulp
	# 默认的名为 default 的任务（task）将会被运行，在这里，这个任务并未做任何事情。
	# 想要单独执行特定的任务（task），请输入 gulp <task>  <othertask>。
	```

## API文档

> 官方文档：https://github.com/gulpjs/gulp/blob/master/docs/API.md
> 中文文档：http://www.gulpjs.com.cn/docs/
> gulp教程之gulp中文API：http://www.ydcss.com/archives/424

### `gulp.src(globs[,options])`
指定需要处理的源文件的路径，`gulp`借鉴了Unix操作系统的管道（`pipe`）思想，前一级的输出，直接变成后一级的输入，`gulp.src`返回当前文件流至可用插件；
```javascript
// 通过gulp.src()读取文件，经过pipe管道流到gulp.dest()指定的文件中
gulp.src('client/templates/*.jade')
  .pipe(jade())
  .pipe(minify())
  .pipe(gulp.dest('build/minified_templates'));
```

#### `globs` 
类型： `String` 或`StringArray`
需要处理的源文件匹配符路径，字符串或者数组
通配符路径匹配示例：
> "src/a.js"：指定具体文件；
> "\*"：匹配所有文件    例：`src/*.js`(包含src下的所有js文件)；
> "\*\*"：匹配0个或多个子文件夹    例：`src/**/*.js`(包含src的0个或多个子文件夹下的js文件)；
> "\{\}"：匹配多个属性    例：`src/{a,b}.js`(包含a.js和b.js文件)  `src/*.{jpg,png,gif}`(src下的所有jpg/png/gif文件)；
> "!"：排除文件    例：`!src/a.js`(不包含src下的a.js文件)；

```javascript
var gulp = require('gulp'); // 导入第三方gulp插件
var less = require('gulp-less'); // 导入第三方gulp-less插件
 
gulp.task('testLess', function () { // 创建一个testLess任务
	// 匹配单个文件
    gulp.src('less/test/style.less')
	// 匹配多个文件（数组）
    gulp.src(['less/**/*.less','!less/{extend,page}/*.less']) // 匹配less文件夹下的所有.less文件，包括子文件夹下的.less，但是不包括less下的extend和page下的.less文件
        .pipe(less()) // 使用gulp-less插件编译less文件
        .pipe(gulp.dest('./css')); // 将编译后的css文件存放到当前目录的css文件夹下
});
```

#### `options`
类型（可选）： `Object`

- `options.buffer`
类型： `Boolean` 默认值： `true`
如果该项被设置为 `false`，那么将会以 `stream` 方式返回 file.contents 而不是文件 `buffer` 的形式。这在处理一些大文件的时候将会很有用。**注意**：插件可能并不会实现对 `stream` 的支持。

- `options.read`
类型： `Boolean` 默认值： `true`
如果该项被设置为 `false` 那么 file.contents 会返回空值（`null`），也就是并不会去读取文件。

- `options.base`
类型`String` 默认值：' '
设置输出路径以某个路径的某个组成部分为基础向后拼接，将会加在 `glob` 之前
如：请想像一下在一个路径为 `client/js/somedir` 的目录中，有一个文件叫 `somefile.js` 
```javascript
// 没有设置base属性
gulp.src('client/js/**/*.js') // 匹配 'client/js/somedir/somefile.js'
  .pipe(minify())
  .pipe(gulp.dest('build'));  // 写入 'build/somedir/somefile.js'
 // 设置 base属性
gulp.src('client/js/**/*.js', { base: 'client' })//设置输出路径为client的js文件夹中，最终变成 'build/js'
  .pipe(minify())
  .pipe(gulp.dest('build'));  // 写入 'build/js/somedir/somefile.js'
```

### `gulp.dest(path[,options])`
指定处理后的文件的输出路径。如果某文件夹不存在，将会自动创建它。
```javascript
gulp.src('./client/templates/*.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates')) // 将处理后的文件存放到当前文件夹下'build/templates'文件夹中
  .pipe(minify()) // 使用gulp-minify插件对文件进行压缩
  .pipe(gulp.dest('./build/minified_templates')); // 将处理后的文件存放到当前文件夹下'build/templates'文件夹中
```
文件被写入的路径是以所给的相对路径根据所给的目标目录计算而来。类似的，相对路径也可以根据所给的 base 来计算。 请查看上述的 gulp.src 来了解更多信息。

#### `path`
类型（必填）： `String` 或 `Function`
指定文件将被写入的路径（输出目录）。也可以传入一个函数，在函数中返回相应路径，这个函数也可以由 `vinyl` 文件实例 来提供。

#### `options`
类型（可选）： `Object`

- `options.cwd`
类型： `String` 默认值： `process.cwd()`
输出目录的 cwd 参数（前脚本的工作路径），当输出目录是相对路径时候有效。

- `options.mode`
类型： `String` 默认值： `0777`
八进制权限字符，用以定义在输出目录中所创建的目录的权限。

### `gulp.task(name[, deps], fn)`
定义一个使用 `Orchestrator` 实现的任务（`task`）。
```javascript
gulp.task('taskname', function() {
  // 做一些事
});
```

#### `name`
类型（必填）：`String`
指定任务的名称，名称中不能包含空格

#### `deps`
类型（可选）：`StringArray`
指定该任务所依赖的其他任务（**注意**：被依赖的任务需要返回当前任务的事件流，从而保证任务的执行顺序，请参考如下示例）
```javascript
gulp.task('testLess', function () {
	// 返回事件流，确保该任务执行后才执行minicss任务
    return gulp.src(['less/style.less'])
        .pipe(less())
        .pipe(gulp.dest('./css'));
});
// minicss任务依赖testLess任务
gulp.task('minicss', ['testLess'], function () { //执行完testLess任务后再执行minicss任务
    gulp.src(['css/*.css'])
        .pipe(minifyCss())
        .pipe(gulp.dest('./dist/css'));
});
```
#### `fn`
类型（必填）：`Function`
该函数定义任务所要执行的一些操作。通常来说，它会是这种形式：`gulp.src().pipe(someplugin())`。
异步任务支持：任务可以异步执行，如果 `fn` 能做到以下其中一点

1. 接受一个`callback`函数
	```javascript
	// 在 shell 中执行一个命令
	var exec = require('child_process').exec;
	gulp.task('jekyll', function(cb) {
	  // 编译 Jekyll
	  exec('jekyll build', function(err) {
		if (err) return cb(err); // 返回 error
		cb(); // 完成 task
	  });
	});
	```

2. 返回一个 `stream`（事件流）
	```javascript
	gulp.task('somename', function() {
	  var stream = gulp.src('client/**/*.js')
		.pipe(minify())
		.pipe(gulp.dest('build'));
	  return stream;
	});
	```

3. 返回一个 `promise`
	```javascript
	var Q = require('q');
	gulp.task('somename', function() {
	  var deferred = Q.defer();
	  // 执行异步的操作
	  setTimeout(function() {
		deferred.resolve();
	  }, 1);
	  return deferred.promise;
	});
	```

**注意**： 默认的，`task` 将以最大的并发数执行，也就是说，`gulp` 会一次性运行所有的 `task` 并且不做任何等待。如果你想要创建一个序列化的 `task` 队列，并以特定的顺序执行，你需要做两件事：

- 给出一个提示，来告知 `task` 什么时候执行完毕，
- 并且再给出一个提示，来告知一个 `task` 依赖另一个 `task` 的完成。

对于这个例子，让我们先假定你有两个 `task`，"one"和 "two"，并且你希望它们按照这个顺序执行：

1. 在 "one" 中，你加入一个提示，来告知什么时候它会完成：可以再完成时候返回一个 `callback`，或者返回一个 `promise` 或 `stream`，这样系统会去等待它完成。
2. 在 "two" 中，你需要添加一个提示来告诉系统它需要依赖第一个 `task` 完成。

因此，这个例子的实际代码将会是这样：
```javascript
var gulp = require('gulp');
// 返回一个 callback，因此系统可以知道它什么时候完成
gulp.task('one', function(cb) {
    // 做一些事 -- 异步的或者其他的
    cb(err); // 如果 err 不是 null 或 undefined，则会停止执行，且注意，这样代表执行失败了
});
// 定义一个所依赖的 task 必须在这个 task 执行之后完成
gulp.task('two', ['one'], function() {
    // task 'one' 完成后才执行这里的代码
});
gulp.task('default', ['one', 'two']);
```

### `gulp.watch(glob[, opts], tasks) 与 gulp.watch(glob[, opts, cb])`
监视文件，并且可以在文件发生改动时候做一些事情。它总会返回一个 `EventEmitter` 来发射（`emit`） `change` 事件。
#### `glob`
类型（必填）： `String` 或 `StringArray`
指定需要处理的一个或多个源文件的一个路径匹配符或路径匹配符数组
#### `opts`
类型（可选）：`Object`
传给 `gaze` 的参数 ，具体参看 https://github.com/shama/gaze
#### `tasks`
类型(必填)：`StringArray` 
需要执行的任务的名称数组

#### `cb(event)`
类型(可选)：`Function` 
每个文件变化执行的回调函数

```javascript
var watcher = gulp.watch('js/**/*.js', ['uglify','reload']);
watcher.on('change', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});

 // gulp.watch(glob[, opts], tasks)
gulp.task('watch1', function () {
    gulp.watch('less/**/*.less', ['testLess']);
});

// gulp.watch(glob[, opts, cb])
gulp.task('watch2', function () {
    gulp.watch('js/**/*.js', function (event) {
        console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
    });
});
```

## 常用插件

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

