---
title: 【译】理解CSS关键字：“Initial”，“Inherit”和“Unset”
date: 2021-02-20 08:33:20
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/understanding-the-initial-inherit-and-unset-css-keywords-2d70b7121695

本文已归纳在 [Github](https://github.com/ddzy/translations)。

<!-- more -->

## 更新

------

### 2021-2-20

- Initial release

## 概述

------

CSS 中有各种各样的关键字。本文我将着重介绍这几个关键字：`initial`、`inherit` 以及较新的 `unset`。

我确信绝大多数开发者都见过这几个关键字，但是对于它们的原理确知之甚少。

![1.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/1.png)

在此之前的一段时间，我只知道这些关键字是用来重置 CSS 样式的，但是如果它们都起到了重置的效果，又为什么会有这么多种呢？它们之间的区别又是什么？**于是，我决定深入探索它们的原理**。

## 浏览器的基础样式

------

在开始讲解这些 CSS 关键字之前，我们首先要了解浏览器的基础样式是从哪儿来的。

### CSS 属性的初始值

每个 CSS 属性都有一个初始值，这个值和 HTML 元素没有任何关系。

如下图所示的是从 MDN 上摘取的 `initial` 的例子：

![2.jpeg](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/2.jpeg)

### 浏览器默认（User-Agent）样式表

在所有的 CSS 属性都设置完初始样式之后，紧接着浏览器会加载自身的样式表。该样式表与 CSS 属性的初始值没有任何关系。

**user-agent 样式示例如下：**

![3.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/3.png)

HTML 元素是没有初始的样式值的！上图中 `<h1>` 标签的基础样式是从 user agent stylesheet 中获取的，而不是 CSS 属性的初始值 `initial`。

**下面，让我们开始讲解这些 CSS 关键字吧！**

## Inherit 关键字

------

`inherit` 关键字告诉浏览器去寻找离当前 HTML 元素最近的父级元素，然后继承该父级元素对应的 CSS 属性值。如果父级元素的 CSS 属性值也是 `inherit` 的话，会一层层的向上查找，直到最后一个 DOM 元素，如果还是没有找到对应的 CSS 属性值的话，就会使用浏览器默认样式表（user-agent-stylesheet）中的属性值，如果没有 user-agent 样式表，最后会使用初始值 `initial`。

<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="elad2412" data-slug-hash="hdypx" data-preview="true" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="inherit css style">
  <span>See the Pen <a href="https://codepen.io/elad2412/pen/hdypx">
  inherit css style</a> by Elad Shechter (<a href="https://codepen.io/elad2412">@elad2412</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## Initial 关键字

------

在学习 `initial` 关键字之前，我们首先要搞清楚：每个 CSS 属性都有一个初始值，该初始值和 user-agent 样式没有任何关系。 User-agent 是浏览器针对 HTML 元素所定义的基本样式表。我们倾向于把 user-agent 样式表理解为是 HTML 自带的，但实际上不是的。

`initial` 关键字告诉浏览器去使用 CSS 属性的初始值，**举个例子：**

- `color` 属性的 `initial` 值总是 `black`

上述行为可能会产生一个困惑：CSS 属性的初始值并不一定和浏览器默认样式表中相应的属性值相同。比如：所有 HTML 元素的 `display` 属性的 `initial` 值都为 `inline`，因此如果把 `<div>` 元素的 `display` 属性值设为 `initial`，那么实际上此时的 `initial` 就是 `inline` 而不是 user-agent 中的属性值 `block`。

举个例子：

```css
div.box{
  background-color: red;
  display: initial; /* initial 的值为 `inline` 而不是 `block` */
}
```

[CodePen 在线示例：div 元素的 display 属性的 Initial 值](https://codepen.io/elad2412/pen/KKKqMyZ)

![4.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/4.png)

## Unset 关键字

------

`unset` 关键字比较特别，它会根据不同类型的 CSS 属性来进行重置。**CSS 属性有两种类型：**

- **可继承的属性** —— 当前 HTML 元素的 CSS 属性会影响它的后代元素。**所有文本类型的 CSS 属性都有这个继承行为**。比如：如果你给某个 HTML 元素设置了 `font-size` 属性，那么该元素的所有后代元素都会继承这个 `font-size`，除非你给某个后代元素又重新设置了 `font-size`。
  ![5.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/5.png)
- **不可继承的属性** —— 当前 HTML 元素的 CSS 属性只对自身有效。**除了文本类型之外的其它 CSS 属性都是不可继承的**。比如：给某个 HTML 元素设置一个 `border` CSS 属性，该元素的后代元素不会同步的获得这个属性。
  ![6.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/6.png)

对于可继承的属性，`unset` 的效果和 `inherit` 一样。例如：对于文本类型的 CSS 属性 `color`，`unset` 会一层层的向上查找，直到最后一个 HTML 元素，如果没有 `color` 属性值，那么就会查找 user-agent 样式表，如果还是没有，最后就直接使用 `initial` 初始值了。

对于不可继承的属性，`unset` 的效果和 `initial` 一样，直接使用该 CSS 属性的初始值。例如：`border-color: unset` 就等同于 `border-color: initial`。

```css
.some-class{
  color: unset; /* = inherit */
  display: unset; /* = initial = inline */
}
```

### 为什么要使用 Unset？

既然已经有了 `initial` 和 `inherit` 关键字，为啥还要用 `unset` 呢？

如果只是重置单个 CSS 属性的值，那么就没必要使用 `unset`，使用 `initial` 和 `inherit` 就可以了。

但是由于全新的 CSS 属性 `all` 的出现，让我们可以一次性重置所有属性的值，包括**可继承属性**和**不可继承属性**！

把 `all` 的值设为 `unset`，就可以实现：所有的可继承属性的值变为 `inherit`，所有的不可继承属性的值变为 `initial`。

**这是 `unset` 这个关键字存在的唯一原因！除此之外，你完全可以用 `inherit` 和 `initial` 关键字替代 `unset`**。

如果我们一个个的书写重置属性，**代码会像下面这样：**

```css
/* Bad */
.common-content * {
  font-size: inherit;
  font-weight: inherit;
  border-width: initial;
  background-color: initial;
}
```

如果我们使用 `all` 属性搭配 `unset` 属性值，会影响当前所有的 CSS 属性，**代码如下：**

```css
/* Good */
.common-content *{
  all: unset;
}
```

我为此专门写了一个例子，详情可见：[`all: unset` 在线示例](https://codepen.io/elad2412/pen/QWWgKbB)。

## Revert 关键字

------

如果我们想把某个 CSS 属性的值重置为 user-agent 样式表中对应的样式值而不是该属性的初始值，我们应该怎么做呢？举个例子：怎么把 `<div>` 元素的值重置为 `block`（user-agent 样式），而不是 `inline`（初始值）？

![7.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/7.png)

为了解决这个问题，又出现了新的 CSS 关键字：`revert`。`revert` 和 `unset` 非常像，唯一的区别在于 `revert` 会把 CSS 属性值重置为 user-agent 中对应的值，**举个例子：**

```css
div {
  display: revert; /* = block */
}
h1 {
  font-weight: revert; /* = bold */
  font-size: revert; /* = 2em */
}
```

也可以一次性重置当前 HTML 元素的所有 CSS 属性：

```css
/* Good */
.common-content * {
  all: revert;
}
```

`revert` 相较于 `unset` 更加有用，但是浏览器兼容性不太好，目前只能在 Firefox 和 Safari 上正常工作。

## 浏览器兼容性

------

- `inherit` —— 包括 IE11 在内的所有浏览器都支持
- `initial` & `unset` —— 除了 IE11 之外的所有浏览器都支持
- `revert` —— 目前只支持 Firefox & Safari