---
title: jQuery源码解析系列(六) -拓展机制
date: 2019-04-17 07:20:18
tags: [jQuery, ts]
categories: [frontend]
---

jQuery为开发者提供了`快速便捷`的DOM操作, 那么如果我们想添加自己的方法到jQuery原型或者构造函数上, 我们该怎么做呢? jQuery暴露出了`extend`API来解决这个问题, 这篇文章就来观摩一下其`拓展机制`.


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章格式🎉

## 二、前置

------

#### 2.1 深拷贝vs浅拷贝

> **PS**: 关于`深`、`浅`拷贝的知识, 网上的各种博客已经层出不穷了, 对其实`现方式`以及`基本原理`都讲解的很清楚. 这里还是`苦口婆心`地提一下, 它们的主要区别, 也是为了接下来地阅读作个铺垫吧.

那么, 问题来了:

> **Q**: 两者的`主要`区别是什么?

- 对于`数组`, 两者是一样的功能
- 对于`键值对对象`
  - `shallowCopy`只拷贝`Object.keys()`, 也就是第一层`键值`
  - `deepCopy`则反之

一个简单的例子:

```js
const obj = {
  b: 1,
  c: 2,
  d: {
    e: 3,
    f: [],
    g: {
      h: {
        ...
      },
    },
  },
};

// shallowCopy: {b, c, d,}
// deepCopy: {b: 1, c: 2, d: { e: 3, f: [], g: { h: {} } }}
```

关于它们的区别, 还可以参考我之前的文章:

> **PS**: [前端基础重拾系列之——深浅拷贝](https://blog.yyge.top/blog/2019/03/05/%E5%89%8D%E7%AB%AF%E5%9F%BA%E7%A1%80%E9%87%8D%E6%8B%BE%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%B7%B1%E6%B5%85%E6%8B%B7%E8%B4%9D/)

## 三、用法

------

### 3.1 *jQuery.extend*&j*Query.fn.extend*

jq提供了两种方式, 供我们自定义拓展:

- jQuery.extend(...)
  - 拓展`Static`, 也就是`静态方法`
- jQuery.fn.extend(...)
  - 拓展`Non-static`(**实例方法**)

### 3.2 如何使用?

分析`拓展机制`前, 先做一件事情, 那就是如何去使用它, 俗话说`知其意,悟其理,守其则,践其行`, 了解了它是`如何使用`, 再去`深究其原理`, 这也是我写文章的一贯原则.

> **PS**: 自己用`jQuery`很少, 所以对`extend`API也基本没用过, 但是这并不影响对其理解, 毕竟知识是相通的, 所谓的框架只不过是`换了身皮`.

一般来说, 开发者可以根据`可以传递的参数个数不同`, 通过下述三种方式使用(以`jQuery.fn.extend`)为例:

#### 3.2.1 使用方式一

> **PS**: **给jq原型添加方法**. jQuery的各种`plugin`就是通过如此来挂载

```js
$.fn.extend({
  sayHello: function() {
    // Do something
  },
});
```

还有另一种方式, 那就是通过`直接赋值`的方式来拓展:

```js
$.fn['sayHello'] = function() {
  // Do something
};
```

#### 3.2.2 使用方式二

> **PS**: 合并`后续`对象到`首个`对象, 纯粹返回一个拓展后的对象, 此时与`jQuery`没有任何关系.

```js
const origin = {};

const newOrigin = $.fn.extend(origin, { name: '' });
```

#### 3.2.3 使用方式三

> **PS**: 是否执行深层的`copy`操作.

```js
const origin = {};

const newOrigin = $.fn.extend(true, origin, {
  name: 'duan',
  skill: {
    hobby: {
      favorite: {},
    },
  },
});

// { xxx, skill: { hobby: { favorite: {} } } }
```

## 四、细说

------

> **PS**: 对于内部的全部代码逻辑, 我觉得并没有必要细看, 所以这里只选取了一种情况————`单个参数`, 也就是对于拓展`jQuery原型`or`静态方法`的处理.

### 4.1 贴源码

先来贴一下部分源码:

```js
jQuery.extend = jQuery.fn.extend = function() {
  var target = arguments[0] || {};
  var i;
  var option;
  var length = arguments.length;
  var name;
  var src;
  var copy;

  // *原文注释*: Extend jQuery itself if only one arguments is passed
  // *人话*: 只传递一个参数时, 拓展jQuery静态方法或实例方法
  if(i === length) {
    target = this;
    i--;
  }

  // 拷贝
  for(; i < length; i++) {
    options = arguments[i];

    for(name in options) {
      copy = options[name];

      if(copy !== undefined) {
        target[name] = copy;
      }
    }
  }
}
```

### 4.2 析源码

对于单个参数的处理逻辑很简单:

- 将`target`赋值为this, this有两种情况
  - jQuery构造函数
  - jQuery实例
- `for-in`遍历`options`
- 给`target`赋值
  - 过滤掉`target`的值为`undefined`的情况

## 五、总结

------

jQuery提供的`extend`极大地方便了开发者, 同样的, `var`框架同样有对应的拓展方法, 这里就不说了(**其实是太菜了**).

当然, 最近也在挤时间看`react`源码, 感觉其复杂度高了几个层级, 所以还是一步一步地走下去吧!

## 五、示例代码

------

使用`ts`重构了我之前写的`jQuery类库`, 项目地址[戳这里](https://github.com/ddzy/my-simple-jquery)