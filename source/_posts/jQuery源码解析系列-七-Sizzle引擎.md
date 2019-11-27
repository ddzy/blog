---
title: jQuery源码解析系列(七) -Sizzle引擎
date: 2019-04-19 07:27:52
tags: [jQuery, ts]
categories: [frontend]
---


经过了前`六`篇文章的分析, 大致了解了`jQuery`的总体架构, 今天就来看一下非常重要的知识点————Sizzle


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章排版格式

## 二、前置

------

### 2.1 正则

由于只分析几种常用的选择器的实现原理, 所以需要点`正则`能力, 起码要对`*`、`.`、`[]`、`分组匹配`、`?:x`等...了如指掌.

## 三、是什么?

------

### 3.1 querySelector&querySelectorAll

要了解`Sizzle`的`存在意义`以及`原理`, 首先得了解`querySelector`和`querySelectorAll`这两个`API`.

> **Q**: 什么是`querySelector`&`querySelectorAll`?

***A***: `CSS3`新推出的API.

> **Q**: 它有什么用?

***A***: 用来替代传统的`document.getElementsByTagName`、`document.getElementById`方法, 来执行`更复杂`的`DOM选取`操作.

> **Q**: 如何使用其选取?

***A***: 两种用途(以`querySelector`为例)

> **Q**: 它的兼容性如何?

***A***: 截取了`MDN`上对其兼容性的描述, 如下图:

![querySelector&querySelectorAll兼容性](/images/1.png)

#### 2.1.1 基本选择器

> **PS**: 较常用, 我基本只用到过这几个😄

```ts
document.querySelector('#app');
document.querySelector('.app');
document.querySelector('span')
```

#### 2.1.2 CSS3选择器

> **PS**: 针对`CSS3`新推出的较为复杂、高级的选择器.

```ts
document.querySelector('p.text span:nth-of-type(2)');
document.querySelector('input[type="radio"]:checked');
```

### 3.2 Sizzle

上面提到了关于`querySelector`的兼容性问题, 其实, 这就是`Sizzle`的诞生原因:

- 解决低版本IE(`IE8`)不支持`querySelector`的情况

它本质上就是一个`**polyfill**`, 那么:

> **Q**: 何所谓polyfill?

我的理解是:

- 实现了某个浏览器不支持的API

这样一来, 问题就清晰多了, 其实:

- `Sizzle`实现的功能和`document.querySelector`是一样的.

## 四、为什么?

------

> **Q**: 为什么jQuery要搞一个`Sizzle`?

jQuery将处理选择器的功能交给了Sizzle, 也是为了抽取核心逻辑, 目前`Sizzle`已经是一个单独的模块, 可以供开发者使用了.

## 五、怎么做?

------

### 5.1 准备工作

源码中有关`Sizzle`的部分是一个自执行函数, 所以将它单独拎出来到外部文件, 大概两千多行的样子.

由于能力有限, 所以, 只会简单了解一下其`大致的运作原理`即可.

### 5.2 如何使用

正式开始之前, 先来了解一下`Sizzle`是如何使用的, 由于`jQuery`已经将其抽离出了一个单独的库, 所以直接引入测试项目集就行了.

在浏览器中可以看到:

![如何使用Sizzle.js](/images/2.png)

可以发现, `Sizzle`返回了匹配到的`DOM结果集`.

### 5.3 大致结构

了解了`Sizzle.js`的基础用法, 接下来就是看源码了.

我将其源码分为了以下几部分:

> **PS**: 标出的位置均位于`origin/Sizzle.js`中, [文件地址](https://github.com/ddzy/my-simple-jquery/blob/branch/dev/origin/Sizzle.js)

```js
/**
 * Sizzle.js
 */


// 第一部分: 正则组合(71 - 171)


// 第二部分: `Sizzle`主函数(217 - 351)


// 第三部分: `select`选择函数(2128 - 2196)


// 第四部分 `compile`编译函数(2089 - 2117)


// 第五部分 其它工具函数

```

划分好了`基本结构`, 那么接下来就照着它来进行下一步——`**简单分析**`.

> **PS**: 力荐`VS Code`插件——`**Bookmarks**`, 源码阅读必备!

- 市场截图:

![bookmarks插件名称](/images/3.png)

- 使用截图

![bookmarks插件使用](/images/4.png)

### 5.4 简单分析

#### 5.4.1 正则组合

> **总览**: 定义一系列针对`CSS2`、`CSS3`选择器规范的`标准正则`, 组装为`正则表达式组`, 塞入`matchExpr`对象.

先来梳理几个问题:

> **Q**: 何为`标准正则`?

***A***: 这只是我取的名字, 因为其根据`CSS`选择器规范来指定.

> **Q**: `组装`的过程?

***A***: 本质就是字符串的拼接.

> **Q**: CSS选择器的种类?

***A***: `id选择器(#)`、`类选择器(.)`、`标签选择器(tag)`、`属性选择器(input[type=""])`、`伪类选择器(:hover、:nth)`、`组合选择器(+、~、>、' ')`

接着, 继续往下看:

> **PS**: 由于代码太多, 因此我挑选了几个贴出来

```js
// 匹配空白字符
var rwhitespace = /[\x20\t\r\n\f]+/g;
// 匹配以`组合选择器`开头的字符串
var rcombinators = /^[\x20\t\r\n\f]*([>+~]|[\x20\t\r\n\f])[\x20\t\r\n\f]*/;
// 测试选择器是否符合`CSS`标准
// http://www.w3.org/TR/CSS21/syndata.html#value-def-identifier
var ridentifier = /^(?:\.|[\w-]|[^\0-\xa0])+$/;

var matchExpr = {
  'ID': /^#((?:\.|[\w-]|[^\0-\xa0])+)/,
  'CLASS': /^\.((?:\.|[\w-]|[^\0-\xa0])+)/,
  'TAG': /^((?:\.|[\w-]|[^\0-\xa0])+|[*])/,
  'ATTR': /\[[\x20\t\r\n\f]*((?:\\.|[\w-]|[^-\xa0])+)(?:[\x20\t\r\n\f]*([*^$|!~]?=)[\x20\t\r\n\f]*(?:'((?:\\.|[^\\'])*)'|"((?:\\.|[^\\"])*)"|((?:\\.|[\w-]|[^-\xa0])+))|)[\x20\t\r\n\f]*\]/,
  "PSEUDO": ...,
  "CHILD": ...,
  ...
};
```

可以看到, 终究是噩梦般的正则, 特别是`ATTR`, 抄都抄错了...

上述几个正则都可以从字面意思上看出它们的用途, 干巴巴的说可能很枯燥, 所以还是去`console`试一下比较好

> PS: 我这里就不贴`测试正则`的截图了, 可以自行尝试, 毕竟与后续的编译相挂钩.

总结一下, jQuery通过定义一系列`匹配CSS选择器类型`的正则, 将诸如:

```js
const selector = '#app .post span.text > input[type="text"]';
```

此类的复杂选择器拆分为小的`chunk`:

- '#app'
- '&nbsp;&nbsp;'
- '.post'
- '&nbsp;&nbsp;'
- 'span.text'
- '>'
- 'input\[type = "text"\]'

对于`chunk`的操作, 后续会说到.

#### 5.4.2 Sizzle主函数

> **PS**: 满怀期待的守在电脑前, 准备欣赏**`海军70周年庆`**, 结果...听说是由于大雾取消了直播, 很可惜.

> **PS**: 但是文章不能不写, 因为今天的任务还未完成, 趁着有精力赶紧把`Sizzle主函数`相关的内容写一下.

##### 5.4.2.1 debugger调试

> PS: 对于经常阅读源码的码农来说, 灵活的应用`DevTools`是至关重要的, 对于简单的库, 可以追踪`变量引用`、`函数堆栈`信息, 比起**傻傻的翻源码**方便多了(`但是对于大型框架并不适用`).

这里简单演示一下:

![演示使用debugger调试](/images/5.gif)

##### 5.4.2.2 内部逻辑

照例, 先贴出一部分`比较重要`的源码:

```js
function Sizzle(selector, context, results, seed) {

  // [MARK]: nodeType默认是`9`, 也就是`document`
  var nodeType = context ? context.nodeType : 9;
  var results = results || [];

  // [MARK]: 如果selector不是字符串, 直接return
  if(typeof selector !== 'string') {
    return results;
  }

  /**
   * [MARK]: 检测能否直接使用原生API, 分两种情况👇
   *
   *    1) 对于简单的单选择器
   *      a) getElementById
   *      b) getElementsByTagName
   *      c) getElementsByClassName
   *
   *    2) 对于复杂的组合选择器
   *      a) querySelectorAll
   */
  if( !seed ) {
    ......
    if(nodeType !== 11 && (
      match = rquickExpr.exec(selector)
    )) {
      // [TODO]: ID选择器
      if(match[1]) {
        ......
        if(document.getElementById(match[1])) {
          results.push(context.getElementById(match[1]));
          return results;
        }
      }else {
        return results;
      }

      // [TODO]: 标签选择器
      else if(
        match[2]
      ) {
        push.apply(results, context.getElementsByTagName( selector ));
        return result;
      }

      // [TODO]: class选择器
      else if
        match[3]
        && support.getElementsByClassName
        && context.getElementsByClassName
      ) {
        push.apply(results, context.getElementsByClassName( match[3] ));
        return results;
      }
    }
  }

  /**
   * [MARK]: 如果支持`querySelectorAll`, 直接使用它
   */
  if(support.qsa) {
    ......
    try {
      push.apply(results, newContext.querySelectorAll( newSelector ))
      return results;
    } catch( qsaError ) {
      // [TODO]: 疑问①
      nonnativeSelectorCache( selector, true );
    } finally {
      ......
    }
  }

  // 对于其它的
  // [TODO]: 疑问②
  return select(
    selector.replace( rtrim, '$1' ),
    context,
    results,
    seed,
  );
}
```

上述就是我抽取出来的部分逻辑代码, 可以看到, 其内部主要进行了以下几种操作:

1. 非法值过滤
2. 简单选择器匹配
3. querySelectorAll检测
4. select主操作函数

可以用一句话概括其处理逻辑:

> 能用浏览器**原生API**, 就不用**Sizzle**了

##### 5.4.2.3 两个疑问

> **Q**: 源码中的`nonnativeSelectorCache`函数是什么?

***A***: 对应`createCache`闭包函数, 为`Sizzle`的缓存机制, 将`selector`以`Key-value-pair`的形式存储于`createCache`内部维护的`keys`数组中.

> **PS**: `createCache`源码可参考[这里](https://github.com/ddzy/my-simple-jquery/blob/branch/dev/origin/Sizzle.js#L359)

> **Q**: 源码中`select`方法起何种作用?

***A***: 下一区块再议

#### 5.4.3 select主操作函数

#### 5.4.4 filters过滤器

#### 5.4.5 tokenize令牌化

#### 5.4.6 compile编译器

## 六、我学到了

------

### 6.1 有用的正则

#### 6.1.1 匹配空白字符

```js
const rwhitespace = /[\x20\t\r\n\f]+/g;
```

#### 6.1.2 匹配简单选择器

> **PS**: 形如`#app`、`.text`、`span`等单选择器.

```js
const rsimpleselector = /^(?:#([\w-]+)|(\w+)|\.([\w-]+))$/;
```

## 七、示例代码

------

示例代码参考[仓库地址](https://github.com/ddzy/my-simple-jquery)