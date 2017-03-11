---
title: gulp搭建静态项目结构
date: 2017-01-10 21:03:08
tags: 新建,模板,小书匠
---

## 基本文件结构
```
├─dist
├─node_modules
└─src
    ├─app
    │  ├─login
    │  └─register
    ├─fonts
    ├─img
    └─less
```

## 安装相关的依赖包
```bash?linenums
npm install --save angular
npm install --save angular-route gulp del  gulp-concat gulp-uglify gulp-yargs gulp-if gulp-less gulp-cssnano gulp-angular-templatecache browser-sync
```
<!--more-->
### 扩展
```bash
# 当根据package.json文件下载安装依赖的时候，如果只想安装生产依赖
npm install --production
```
## 对文件进行打包操作
当写项目的时候需要对项目进行**实时**打包，合并成单个文件
- 将静态文件打包到dis中，并保存目录结构
```javascript?linenums
/**
 * 每次构建dist目录时先删除dist文件
 */
gulp.task('clear', function(callback) {
	del(paths.dist)
		.then(function() {
			callback() // 保证任务的执行顺序
		})
});
/**
 * 拷贝静态文件到指定目录，每次拷贝前先删除dist文件夹中的内容
 */
gulp.task('static', ['clear'], function() {
	return gulp.src(paths.staticPath, {
			base: './src' //拷贝时，保留src下的目录结构不变
		})
		.pipe(gulp.dest(paths.dist))
});
```

- 将引入的css都打包成一个文件，如： `app.css`
```javascript?linenums
/**
 * 编译main.less文件
 */
gulp.task('less',  function() {
	return gulp.src(paths.lessPath) // lessPath: './src/less/main.less'
		.pipe(less())
		.pipe(gulpif(argv.deploy, css()))
		.pipe(gulp.dest(paths.lessDist)) // lessPath: './src/less/main.less'
});
```
- 将第三方的js文件打包合并成一个 `vender.js` 文件
```javascript?linenums
/**
 * 压缩合并第三方依赖的JS文件到指定目录
 */
gulp.task('vender', function() {
	return gulp.src(paths.venders) // venders: ['./node_modules/angular/angular.js', './node_modules/angular-route/angular-route.js'],
		.pipe(concat('vender.js')) // 合并JS代码并取名为vender.js
		.pipe(gulpif(argv.deploy, uglify())) // 当命令行包含参数-deploy时执行uglify()压缩JS代码
		.pipe(gulp.dest(paths.venderDist)) // venderDist: './dist/js'
});
```
- 将自己的js文件打包合并成一个 `bundle.js` 文件
```javascript?linenums
// html文件中的外部文件路径的引入问题
// 使用angular的$templateCache将所有html文件读取出来生成一段js代码
/**
 * 读取app文件夹中所有的html文件自动生成模板字符串缓存
 * templatecache参数
 * 	参数一：fileName默认是templates.js
 * 	参数二：options
 * 		root: 生成的路径名
 * 		module：angularjs模块名，默认是templates，然后在主模块中引入该模块
 * 		standalong：创建一个独立的模块
 * 		transformUrl：(类型：function)转换路径，修改原有的生成规则，返回新的路径
 * 			利用node的内置模块==>node.basename(path)
 */
gulp.task('template',  function() {
	return gulp.src(paths.template)
		.pipe(templatecache({
			module: 'templates',
			standalong: true,
			transformUrl: function(url) {
				return './' + path.basename(url)
			}
		})) //将读取到的html文件生成一段js代码，以字符串的形式存储到缓存中
		.pipe(gulpif.dest('./')) //将构建出来的文件存放到
});
/**
 * 压缩合并自己的JS文件到指定目录，包括templates.js文件
 */
gulp.task('script', ['template'], function() {
	return gulp.src(paths.scripts)
		.pipe(concat('bundle.js')) // 合并JS代码
		.pipe(gulpif(argv.deploy, uglify())) // 当命令行包含参数-deploy时执行uglify()压缩JS代码
		.pipe(gulp.dest(paths.scriptDist))
});
```
- 开启服务监视文件改动自动刷新浏览器
```javascript?linenums
/**
 * 监视js文件的变动
 */
gulp.task('js-watch', function(done) {
	browserSync.reload()
	done()
});
/**
 * 开启服务
 */
gulp.task('server', function() {
	browserSync.init({
		server: {
			baseDir: path.dist
		}
	})
	gulp.watch(['./src/app/**/*.js',
		'./src/app/**/*.less',
		'./src/**/*.html'
	], ['js-watch'])
});
```

## 最终的`gulpfile.js`

*这里的代码任务之间的依赖性太强，有很大的优化空间，代码仅供参考*

```javascript?linenums
/**
 * 1. 拷贝静态资源
 * 2. 压缩合并第三方JS文件
 * 3. 压缩合并自己的JS文件
 * 4. 编译压缩less文件
 */

var gulp = require('gulp')
var del = require('del') // 删除文件
var concat = require('gulp-concat') // 合并JS文件
var uglify = require('gulp-uglify') // 压缩JS文件
var argv = require('yargs').argv // 获取命令行参数
var gulpif = require('gulp-if') // 根据判断执行某个插件
var less = require('gulp-less') // 编译less文件
var css = require('gulp-cssnano') // 压缩 css
var templatecache = require('gulp-angular-templatecache') // 压缩 css
var path = require('path') // 加载node内置的path方法，解析路径
var browserSync = require('browser-sync').create() // 开启服务并和 gulp 配合使用实现文件改变执行某个任务后自动刷新

var paths = {
	staticPath: [
		'./src/index.html',
		'./src/img/**/*.*',
		'./src/fonts/**/*.*'
	],
	dist: './dist'
	venders: [
		'./node_modules/angular/angular.js',
		'./node_modules/angular-route/angular-route.js'
	],
	venderDist: './dist/js',
	scripts: [
		'/templates.js', // 放在前面，保证在合成的时候也是在文件内容的最上面
		'./src/app/**/*.js'
	],
	scriptDist: './dist/js',
	lessPath: './src/less/main.less', //其他less文件都引入main.css文件中，最后编译成一个main.css文件
	lessDist: './dist/css',
	template: './src/app/**/*.html'
}

/**
 * 每次构建dist目录时先删除dist文件
 */
gulp.task('clear', function(callback) {
	del(paths.dist)
		.then(function() {
			callback() // 保证任务的执行顺序
		})
});
/**
 * 拷贝静态文件到指定目录
 */
gulp.task('static', ['clear'], function() {
	return gulp.src(paths.staticPath, {
			base: './src' //拷贝时，保留src下的目录结构不变
		})
		.pipe(gulp.dest(paths.dist))
});
/**
 * 压缩合并第三方依赖的JS文件到指定目录
 */
gulp.task('vender', ['less'], function() {
	return gulp.src(paths.venders)
		.pipe(concat('vender.js')) // 合并JS代码并取名为vender.js
		.pipe(gulpif(argv.deploy, uglify())) // 当命令行包含参数-deploy时执行uglify()压缩JS代码
		.pipe(gulp.dest(paths.venderDist))
});
/**
 * 压缩合并自己的JS文件到指定目录，包括templates.js文件
 */
gulp.task('script', ['template'], function() {
	return gulp.src(paths.scripts)
		.pipe(concat('bundle.js')) // 合并JS代码
		.pipe(gulpif(argv.deploy, uglify())) // 当命令行包含参数-deploy时执行uglify()压缩JS代码
		.pipe(gulp.dest(paths.scriptDist))
});
/**
 * 编译main.less文件
 */
gulp.task('less', ['static'], function() {
	return gulp.src(paths.lessPath)
		.pipe(less())
		.pipe(gulpif(argv.deploy, css()))
		.pipe(gulp.dest(paths.lessDist))
});

// html文件中的外部文件路径的引入问题
// 使用angular的$templateCache将所有html文件读取出来生成一段js代码
/**
 * 读取app文件夹中所有的html文件自动生成模板字符串缓存
 * templatecache参数
 * 	参数一：fileName默认是templates.js
 * 	参数二：options
 * 		root: 生成的路径名
 * 		module：angularjs模块名，默认是templates，然后在主模块中引入该模块
 * 		standalong：创建一个独立的模块
 * 		transformUrl：(类型：function)转换路径，修改原有的生成规则，返回新的路径
 * 			利用node的内置模块==>node.basename(path)
 */
gulp.task('template', ['vender'], function() {
	return gulp.src(paths.template)
		.pipe(templatecache({
			module: 'templates',
			standalong: true,
			transformUrl: function(url) {
				return './' + path.basename(url)
			}
		})) //将读取到的html文件生成一段js代码，以字符串的形式存储到缓存中
		.pipe(gulpif.dest('./')) //将构建出来的文件存放到
});
/**
 * 监视js文件的变动
 */
gulp.task('js-watch', ['script'], function(done) {
	browserSync.reload()
	done()
});
/**
 * 开启服务
 */
gulp.task('server', ['script'], function() {
	browserSync.init({
		server: {
			baseDir: path.dist
		}
	})
	gulp.watch(['./src/app/**/*.js',
		'./src/app/**/*.less',
		'./src/**/*.html'
	], ['js-watch'])
});
```