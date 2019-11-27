---
title: jQuery源码解析系列(三) -工厂函数
date: 2019-03-30 14:17:27
tags: [jQuery, ts]
categories: [frontend]
---

jQuery的入口函数, 也就是平时使用的$(xxx)诸如此类.


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 文章格式调整

## 二、简单工厂模式

------

开始之前, 先回顾一下设计模式中所谓的`简单工厂模式`:

#### 2.1 是什么?

> **PS**: 根据不同的参数, 返回不同类的实例

简单来说, 就是:

- 创建并对外暴露一个普通函数, 由于内部机制, 姑且就叫它`工厂函数`
- 接受一个目标实例化类型的参数
- 函数内部进行实例化(`new`)操作

#### 2.2 为什么?

那么大致了解了`简单工厂`的工作流程, 现在我们做个简单的`thinking`:

1. 为什么会出现这种设计模式?
2. 它解决了什么痛点?

俗话说的好——**事出必有因**, 对于上述两个思考, 我觉得可以由以下两个方面概括:

- 将对象的创建和使用解耦
  - 比如在开发中或多或少会用到插件, 那么实例化插件的过程中, 我们可能 会无意中丢失了`new`操作符, 导致后续报错, 这对于开发者是绝不能容忍的~, 而`简单工厂`解决了这个问题, 它将`实例化`的操作隐藏在了内部, 用户只需要知道`自己想要什么`, 而工厂则`去做什么`.
- 降低代码重复
  - 假设对象的创建需要经历`很多很多`的步骤, 这对于有`多个对象创建需求`的用户来说就很蛋疼了, 这时候使用`工厂模式`就能免去冗余的操作, 增强用户体验

#### 2.3 怎么做?

> **PS**: 网上也有很多关于`工厂模式`的例子, 本人[之前的项目](https://github.com/ddzy/ts-utility-plugins)也使用了该模式, 在这里还是不厌其烦的加上, 一方面为了作为参照, 另一方面也是对该设计模式的巩固.

```ts
class TypeA {
  constructor(
    private name: string,
  ) {}
}

class TypeB {
  constructor(
    private name: string,
  ) {}
}

function TypeFactory(
  type: string,
  options: {
    [key: string]: any,
  },
): TypeA | TypeB {
  switch(type) {
    case 'A': {
      return new TypeA(options);
    }
    case 'B': {
      return new TypeB(options);
    }
    default: {
      throw new Error('Parameter `type` must be provided.');
    }
  }
}
```

## 三、jQuery工厂

---

通过上面的`简单工厂模式`的回顾, 我们了解了该种设计模式的出现到底有啥用. 那么回过头来, jQuery的源码中:

```js
var jQuery = function(selector, context) {
  return new jQuery.fn.init(selector, context);
}
```

可以看到, jQuery的入口函数与`简单工厂`的实例代码类似, 名为`jQuery`的函数接收两个参数:

- selector
  - 选择器
- context
  - 范围限制

在内部进行了实例化操作, 并且jQuery原型上的`init`方法才是真正的构造函数. 那么这里可能就会有疑问了:

> **Q**: 为什么不直接暴露出`jQuery.fn.init`?

我们举例来分析:

```js
/**
 * 普通用法
*/
$(xxx).addClass(xxx).removeClass(xxx);

/**
 * 直接暴露`jQuery.fn.init()`
*/
new jQuery.fn.init(xxx).addClass(xxx).removeClass(xxx);
```

可以看到, 一个简单的`工厂函数`, 为我们省去了不必要的操作.

那么至于为什么实例化一个原型上的方法呢? 我想, 这可能是作者的个人想法吧, 毕竟`一千个人就有一千个哈姆雷特`, 可能我自己在构建的时候会使用任何其他`不同名的类`, `取其精华, 去其糟粕`, 阅读源码的过程不正是为了如此吗?

## 四、总结

------

本篇分析了jQuery工厂的创建流程, 以及为什么要设计如此这般, 下一篇文章会着重分析`jQuery构造函数`机制

## 五、示例代码

------

重构后的`ts`、`js`版本代码放置在了[Gayhub](https://github.com/ddzy/my-simple-jquery/tree/branch/dev)