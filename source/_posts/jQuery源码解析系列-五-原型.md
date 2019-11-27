---
title: jQuery源码解析系列(五) -原型
date: 2019-04-16 13:34:17
tags: [jQuery, ts]
categories: [frontend]
---

继上一篇文章说到了jq的构造函数, 本节主要是深究一下jquery的原型机制.


<!--more-->


## 一、更新

------

### [2019-4-21]

#### [Changed]

- 改进文章格式

## 二、前置

------

#### 2.1 堆内存

关于`堆内存`, 我将它提到了文章开头,  因为`jQuery`作者巧妙的运用了这一点.

> **Q**: 那么什么是堆内存? 它有什么用?

这里就不再详细说了, 网上的资料非常详细, 只需明白一点:

- js中`堆内存`存储了一个指向`对象池`中`特定对象`的指针, 由此导致`多路`对于该对象的操作, 都会直接修改其本身.

## 三、原型

------

由于源码较少, 直接贴出:

```js
  function jQuery() {
    return new jQuery.fn.init();
  }

  var init = jQuery.fn.init;

  // Give the init function the jQuery prototype for later instantiation
	init.prototype = jQuery.fn;
```

可以看到上述源码内部的注释, 它的意思是:

> **PS**: 将`jQuery`的原型赋值给`init`的原型, 便于后续操作

作者将`init.prototype`和`jQuery.prototype`指向了同一个对象.

> **PS**: 那么, 这么做有什么好处呢?

不急, 先来回想一下, 我们是怎么用jQuery的? 我想, 肯定是这样吧

```js
$(xxx).xxx().xxx()
```

粗看好像没什么问题, 但是细想之下, 平时使用插件的时候, 是这么用的:

```js
const xxx = new Plugin({});
```

对比之下, 两者区别在于:

- jQuery不用进行`new`, 也就是实例化操作

结合前几篇文章的分析, 我们知道那是因为jQuery内部帮我们实现了实例化.

那么, 我们可以大胆地做个猜测, 他可能是这么实现的吧:

```js
function jQuery(selector) {
  return new jQuery(selector);
}

jQuery.prototype = {...};
```

但是, 细心的我们会发现, 该操作不但没有解决问题, 还会出现`Maximum call stack size exceeded`的报错, 很显然, 这是一个`没有终止条件的递归操作`.

由此, jQuery作者很轻松的想到了使用`原型赋值`的方式, 来解决递归爆栈的问题, 不禁感叹, 真是🐂🐂!

## 四、总结

------

感觉啥都没说, 这一节就写完了, 不得不佩服jQuery作者`John Resig`的高明之处, 一个简简单单的`工厂函数`, 能玩的如此美妙!

## 五、示例code

------

为了加深理解以及熟练`ts`, 已用`ts`重构代码, 详情见[Github](https://github.com/ddzy/my-simple-jquery/tree/master).