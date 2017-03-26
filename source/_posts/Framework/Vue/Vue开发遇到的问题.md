---
title: Vue开发遇到的问题
date: 2016-11-18 21:30:52
tags: Vue
grammar_cjkRuby: true
---

本文主要是记录学习及开发中遇到的一些问题，仅供自己学习所用，因此没有详细记录

## router-link不能够添加绑定事件
如果想要在添加点击事件，需要在router-link标签的内部或者外部标签上添加，不能直接在router-link上添加，没有效果
详情请看 https://segmentfault.com/q/1010000007891457

<!--more-->

## 返回按钮的显示隐藏
在首页时候没有返回按钮，但是其他页面有返回按钮，因此设置了一个isShow属性为false让其隐藏，当跳转到其他页面的时候设置为true，这个时候刷新页面就没有返回按钮了，因为刷新页面会重新构建vue对象的此时isShow是初始值false，所以就隐藏了，这时候就可以考虑在created方法执行的时候直接监听$route.path的变化设置isShow的值从而控制返回按钮的显示和隐藏

## 新闻列表到详情的内容获取不正确
**现象**：
一个首页中有通过`router-view`组件显示新闻列表以及新闻详情页面，当从列表页跳转到详情页时，第一次详情页数据没有问题，但是以后列表页到详情页的每次跳转都是第一次跳转得到的数据，只有手动刷新详情页才能实现新数据的加载

**原因**：
由于在`router-view`组件的外层包裹了`keep-alive`组件，因此`router-view`中的所有组件都会被缓存起来，所以组件只被创建了一次（即只会执行一次生命周期钩子函数），一直没有被销毁，也就是没有执行`destroyed`生命周期钩子，而新闻页数据的获取是在新闻页的`created`生命周期钩子中执行的，所以详情页的数据只获取了一次，即第一次访问时候的数据，因此以后的每次都是第一次获取的数据。而刷新页面相当于重新创建了一次该组件，因此会显示正确的新闻内容

**解决办法**：
- 方法一：去掉外层包裹的`keep-live`组件，即不缓存组件
- 方法二：使用路由钩子`beforeRouteEnter`处理数据请求问题

**总结**：
`keep-alive`组件会把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染，因此不能在经常需要变化的地方用，否则就会出现数据显示不正确的情况，总之根据实际需求来决定需不需要使用`keep-alive`

相关网址：
- [vue2.0 中keep-alive内容不刷新的问题](https://segmentfault.com/q/1010000007767010)
- [vue-router导航钩子](http://router.vuejs.org/zh-cn/advanced/navigation-guards.html)

## 给组件绑定事件无效
**现象**：
给组件绑定事件无效
**解决办法**：
加上`.native`修饰符即可

## $refs获取DOM元素的问题
$refs是在那个时候获取DOM元素的，为什么在组件的`beforeCreate`和`created`钩子函数中都可以获取到DOM元素，此时DOM不是应该还没有被渲染吗

## 拉钩中seller滚动问题
涉及到生命周期函数不同的阶段调用初始化betterScroll的初始化

## props接受数据但是在js中无法获取
**现象**：在接受数据的组建中无法通过js访问props的属性
**原因**：数据是异步获取的，当组件创建的时候，可能数据还没有获取过来，此时访问props的属性是设置的初始值，而不是最终的值
**解决办法**：通过在updated钩子函数访问props的属性，当异步获取数据之后，会自动触发updated钩子函数，此时props的属性就是最终设置的值

## v-for循环需要添加key属性
当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用 “就地复用” 策略。如果数据项的顺序被改变，Vue将不是移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。这个类似 Vue 1.x 的 track-by="$index" 。
这个默认的模式是有效的，但是只适用于不依赖子组件状态或临时 DOM 状态（例如：表单输入值）的列表渲染输出。
为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值（在这里使用简写）：
```html
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```

建议尽可能使用 v-for 来提供 key ，除非迭代 DOM 内容足够简单，或者你是故意要依赖于默认行为来获得性能提升。
因为它是 Vue 识别节点的一个通用机制， key 并不特别与 v-for 关联，key 还具有其他用途，详情请看 
https://cn.vuejs.org/v2/guide/conditional.html#用-key-管理可复用的元素

## 数组的更新
由于 JavaScript 的限制， Vue 不能检测以下变动的数组：
当你利用索引直接设置一个项时，例如： vm.items[indexOfItem] = newValue
当你修改数组的长度时，例如： vm.items.length = newLength

为了解决第一类问题，以下两种方式都可以实现和 vm.items[indexOfItem] = newValue 相同的效果， 同时也将触发状态更新：
```javascript
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
// Array.prototype.splice`
example1.items.splice(indexOfItem, 1, newValue)
```

为了解决第二类问题，你也同样可以使用 splice：
```javascript
example1.items.splice(newLength)
```

## 设置值
受现代 JavaScript 的限制（以及废弃 `Object.observe`），Vue **不能检测到对象属性的添加或删除**。由于 Vue 会在初始化实例时对属性执行 `getter/setter` 转化过程，所以属性必须在 `data` 对象上存在才能让 Vue 转换它，这样才能让它是响应的。例如：
```javascript
var vm = new Vue({  data:{  a:1  }})// `vm.a` 是响应的vm.b = 2// `vm.b` 是非响应的</pre>
```

Vue 不允许在已经创建的实例上动态添加新的根级响应式属性(root-level reactive property)。然而它可以使用 `Vue.set(object, key, value)` 方法将响应属性添加到嵌套的对象上：
```javascript
Vue.set(vm.someObject, 'b', 2)
```

您还可以使用 `vm.$set` 实例方法，这也是全局 `Vue.set` 方法的别名：

```javascript
this.$set(this.someObject,'b',2)
```

有时你想向已有对象上添加一些属性，例如使用 `Object.assign()` 或 `_.extend()` 方法来添加属性。但是，添加到对象上的新属性不会触发更新。在这种情况下可以创建一个新的对象，让它包含原对象的属性和新的属性：

```javascript
// 代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```
也有一些数组相关的问题，之前已经在[列表渲染](https://cn.vuejs.org/v2/guide/list.html#注意事项)中讲过。

## 异步更新队列

可能你还没有注意到，Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会一次推入到队列中。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际（已去重的）工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MutationObserver，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。
例如，当你设置 vm.someData = 'new value' ，该组件不会立即重新渲染。当刷新队列时，组件会在事件循环队列清空时的下一个“tick”更新。多数情况我们不需要关心这个过程，但是如果你想在 DOM 状态更新后做点什么，这就可能会有些棘手。虽然 Vue.js 通常鼓励开发人员沿着“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们确实要这么做。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。例如：
```html
<div id="example">{{message}}</div>
```
```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // 更改数据
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```
在组件内使用 vm.$nextTick() 实例方法特别方便，因为它不需要全局 Vue ，并且回调函数中的 this 将自动绑定到当前的 Vue 实例上：
```javascript
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: 'not updated'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => '没有更新'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => '更新完成'
      })
    }
  }
})
```
