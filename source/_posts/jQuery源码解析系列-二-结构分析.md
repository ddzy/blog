---
title: jQuery源码解析系列(二) -结构分析
date: 2019-03-28 16:44:47
tags: [jQuery, ts]
categories: [frontend]
---

上一篇文章主要说了一下源码阅读的心路历程, 当然可能是废话, 但是对我而言是很有意义的. 这篇文章, 将会花一点点时间对`jQuery`的源码结构做个简单了解.


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 文章排版调整

## 二、前置

------

本次源码解析采用我最钟情的版本:

- **jQuery-3.2.1**

这个版本算是陪伴我很久了, 从18年2月至今, 一直用的它

## 三、分析

------

首先, 要吐槽一下, jq源码并不是用ts来构建的, 对于源码阅读者来说很不友好, 毕竟在VS Code中不能作跳转和提示. 但是, '鸡贼'的我找到了`jquery.d.ts`来作为对照, 可以比对参数类型, 还算有点补救措施.

步入正题, 使用VS Code的折叠快捷键快速`堆叠`所有函数, 可以清楚的看到, jq的内部组成大致是这样的:

```javascript
((env, fn) => {

  ...
  // environment判断
  // 由于我们只在浏览器环境中测试, 所以这一块先忽略
  ...

})(window || this, (window, isGlobal) => {

  // 1. 缓存常用方法

  var push = [].push
  var class2Type = {};
  ...


  // 2. jQuery工厂函数

  var jQuery = function(selector, context) {
    ...
  }


  // 3. jQuery构造函数
  var init = jQuery.fn.init = function(selector, context, root) {
    ...
  }


  // 4. jQuery原型

  jQuery.fn = jQuery.prototype = {
    ...
  }


  // 5. jQuery拓展机制

  jQuery.extend = jQuery.fn.extend = function() {
    ...
  }
  jQuery.extend(
    ...
  )


  // 6. Sizzle引擎

  var Sizzle = (function(window) {
    ...
  })(window);


  // 7. jQuery事件队列

  jQuery.Callbacks = function(options) {
    ...
  }


  // 8. 缓存模型

  function Data() {
    ...
  }
  Data.prototype = {
    ...
  };


  // 9. jQuery事件模型-Event

  jQuery.event = {
    ...
  }
  jQuery.Event = function(src, props) {
    ...
  }
  jQuery.Event.prototype = {
    ...
  }


  // 10. jQuery动画缓动机制

  function Tween(elem, options, prop, end, easing) {
    ...
  }
  Tween.prototype = {
    ...
  }


  // 11. jQuery网络请求
  jQuery.extend({
    ajax: function(url, options) {
      ...
    }
  })

});
```

这里我将其分为`12`个区块, 它们是jq的核心躯干. 后续的文章也会对这若干个区块`逐个击破`.

## 四、附加

------

#### 4.1 折叠所有函数

<kbd>Ctrl + K + 0</kbd>

#### 4.2 展开所有函数

<kbd>Ctrl + K + J</kbd>


## 五、示例代码

------

仓库地址: [Here](https://github.com/ddzy/my-simple-jquery/tree/master)