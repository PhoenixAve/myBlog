---
title: angular 容易遇到的坑
date: 2017-02-12
tags: angular
grammar_cjkRuby: true
---

## angular 容易遇到的坑

### 进行遍历ng-repeat的时候要注意是否有重复数据

F：我们在进行ng-repeat遍历数组的时候当我们的数据有重复的时候，我们的NG会报错，

Q：处理办法是，在我们的要进行数据遍历的地方加入 track by $index

<!--more-->

### 我们在进行事件广播的时候一定要切记的事项

我们在进行数据广播的时候，要注意的是我们要进行调用的事件是否已经初始化完毕，

一般处理办法，是进行一个延时广播的操作，这样子就可以就可以避免这个问题了

还有就是我们在进行事件广播的时候一定要避免的是我们的emit和我们的broadcast的事件名称不能相同，不然的话，我们的事件广播会进入一个死循环

### 还有就是切记不要在我们的controller中操作我们的dom

如果必须要进行操作dom的话，我们也要在我们的指令`directive`  中进行操作，我们在操作过dom之后要进行`$scope.$digest()`的调用，这样子就可以同步我们的视图和数据了，简单来说，当我们的双向数据绑定发生失效的时候，我们就要调用我们的`$scope.$digest()`函数，进行数据和视图之间的同步

`$scope.$digest()`一般情况下，调用这个函数会报错的

为什么不能直接在我们的controller中直接操作我们的dom呢，

因为我们的dom被我们直接操作以后，很容易与我们的NG产生冲突，这样子的话，可能是我们的整个NG模块出现不可预知的问题

### 当我们的自己调用我们自己写的jsonp的时候，我们的怎么进行数据的刷新

`$scope. $apply`

### $timeout

和我们js的timeout用法基本一致

只是我们在把时间设为0之后，并不会立即执行，只是把我们的匿名函数，放在我们队列的最后面（因为我们的js语言的原理所导致的，因为我们的js是单线程的）



### 在使用directive（指令）的时候，当我们对外界暴露一个接口的注意事项

我们不可以直接在我们的当我们要给我们的接口直接赋值的情况下我们的不要直接给接口一个等于，而是间接地给一个@

因为我们的如果直接给它赋值的话，如果不加单引号的情况下，会直接报错

例如下面这个指令

`appHeadBar.js`

```javascript
//这儿是我们定义的指令
"use strict";
angular.module("app").directive("appHeadBar",function(){
    return{
        restrict:"A",
        replace:true,
        templateUrl:"views/template/headBar.html",
      //当我们使用这种写法的时候，我们只需要使用下面的第一种写法进行引用即可
        scope:{
            text:"@"
        },
      //当我们使用这种写法的时候，我们要使用第二种写法，不然的话，会在浏览器中报错
      	scope:{
            text:"@"
        },
        link:function($scope){
            $scope.back=function(){
                //这儿window一定要小写
                window.history.back();
            };
        }
       
    };
});
```

`post.html`

```html
<!--写法一--> 
<div app-head-bar text="我的收藏"></div>

<!--写法二-->
<div app-head-bar text="'我的收藏'"></div>
```

`ERROR`

```
Error: [$parse:lexerr] Lexer Error: Unexpected next character  at columns 0-0 [我] in expression [我的收藏].
```

