---
title: 【译】深度解析CSS是如何工作的
date: 2021-02-05 08:52:50
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/how-does-css-work-92fe7116916d

本文已归纳在 [Github](https://github.com/ddzy/translations)。

<!-- more -->

## 更新

------

### [2021-2-5]

- Initial release

### [2021-2-9]

#### Added

- 完成 `概述` 章节
- 完成 Level1 的 `初始值` 章节

### [2021-2-18]

#### Added

- 完成 Level3 章节

### [2021-2-19]

#### Added

- 完成剩余章节

### [2021-2-22]

#### Changed

- 更改文末的文章链接

## 概述

------

> 副标题（补充）：理解浏览器默认的样式行为

在你为网站中的某个 DOM 元素编写 CSS 样式之前，浏览器会给这个 DOM 元素添加一个默认的样式。

你有没有想过这些默认样式是由谁来决定的？

在这篇文章中，我将教会你：

- 浏览器中不同级别的 CSS
- 每一级 CSS 是如何工作的
- 我们该如何去控制

![1.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/1.png)

在我刚开始学习 CSS 的时候，我还以为所有的 CSS 默认样式都来自于浏览器自身的 “User-Agent Stylesheet” 文件。但是后来，我发现我错了，这只是冰山一角。

让我们开始吧！

## Level1——CSS 属性的默认样式

------

每个 CSS 属性都有一个 **规范**。这些规范也是 W3C 标准的一部分。

### 初始值

每个 CSS 属性都有一个初始值。这个初始值不依赖于该 CSS 属性所应用的 HTML 元素。也就是说，对于所有的 HTML 元素，如果具有某个 CSS 属性的话，那么这个属性都会有一个初始值，并且初始值都是一样的。

我相信绝大多数 web 开发者都不知道这一点。

**以 CSS 的定位属性（`position`）为例：**

```css
.some-class{
  position: absolute;
  top: 20px;
  left: 20px;
}
```

**你知道 position 属性的默认值是什么吗？**

很多 web 开发者都回答不出来。正确的答案是：`static`。

类似的还有其它与定位相关的属性，比如：`top`/`bottom`/`left`/`right`，它们的初始值都是 `auto`。

**请记住** —— 在你开始编写样式之前，这些 CSS 属性的基本样式就已经存在了。。。

下面是 CSS 定位相关属性的默认值：

```css
{
  position: static;
  top: auto;
  bottom: auto;
  left: auto;
  right: auto;
}
```

想知道每个 CSS 属性的初始值是什么？最佳途径就是查阅 MDN 文档：

> 中文文档（查阅 “规范” 部分）：https://developer.mozilla.org/zh-CN/docs/Web/CSS/position#specifications

> 英文文档（查阅 “Formal Definition” 部分）：https://developer.mozilla.org/en-US/docs/Web/CSS/position#formal_definition

如下图所示：

[![2.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/2.png)](https://developer.mozilla.org/en-US/docs/Web/CSS/position#formal_definition)

### 继承

第二个有趣的特性就是：部分 CSS 属性具有 “继承行为”。也就是说，假设我们给某个 HTML 元素设置了一些特定的样式，那么该元素的后代都会继承这些样式。

一般情况下，以下两种类型的 CSS 属性会具有继承的行为：

 - 文本类型：`font-family`、`font-size`、`color`、`text-align`
 - 排版类型：[CSS 排版属性介绍](https://medium.com/cssclass-com/css-basics-for-typography-160025e3aeca)

**想象一下**：当你给 `<body>` 元素设置了 `font-size: 20px` 属性，那么 `<body>` 元素的所有后代元素都会继承这个 `font-size`，除非你给某个后代元素重新设置了 `font-size`。

但是如果你给 `<body>` 元素设置了 `padding: 20px` 属性，那么其后代元素都不会继承 `padding`，为什么呢？因为 `padding` 属性是没有继承行为的，也就是说，它是不能继承的。

> **实际上，我们可能在没有意识到的情况下，就使用了继承。。。**

**HTML：**

```html
<body>
  Some text in the body HTML element
  <div> some text on the div HTML element </div>
</body>
```

**CSS：**

```css
body {
  font-size: 20px; /* body 和 div 都会继承这个样式 */
  padding: 20px;   /* 只对 body 自身有效 */
}
```

[![3.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/3.png)](https://codepen.io/elad2412/pen/6c47b9d2c1dd836e2d7281d7640ac552)

**那么问题来了，我们在浏览器进行调试的时候怎么才能知道某个 CSS 属性是否可以继承呢？**

实际上，在我们使用 “审查元素” 功能查看 `<div>` 元素的样式的时候，浏览器的开发者工具会展示哪些样式是从 `<body>` 继承而来的。

例如在下图中，`font-size` 属性是可继承的，所以会高亮显示；反之，`padding` 是不可继承的，会置灰显示：

[![4.jpeg](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/4.jpeg)](https://codepen.io/elad2412/pen/4bd977d6645fbe70921c4c01c1b23fba)

**检查继承行为的正式方式**

要想了解一个 CSS 属性是否可继承，除了自己尝试之外，另一种方式就是查阅 MDN 文档中的 “规范” 部分。

**如下图所示：**仔细对比左右两块区域的差距即可：

![5.jpeg](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/5.jpeg)

## Level2——User-Agent-Stylesheet

------

> “User-Agent-Stylesheet”：浏览器默认样式表

在 Level1 章节中，我们了解到：

- 所有的 HTML 元素上的所有 CSS 属性具有相同的初始值
- 所有的 CSS 属性有且只有一个初始值

在本章节，我将介绍 **“User-Agent-Stylesheet”** —— 这是一种区别于不同类型的 HTML 元素的特殊样式表。

**“User-Agent-Stylesheet”** 其实就是一个 CSS 文件，区别在于不同的浏览器厂商实现了不同的版本。与 Level1 章节中介绍的 “CSS 属性的默认值” 截然相反的是：后者表明 CSS 属性的初始值和 HTML 元素没啥关系；而前者则通过为不同的 HTML 标签创建样式，从而关联 HTML 和 CSS。

**举个例子：**`display` 属性的初始值无论在什么情况下，都是 `inline`。但是在我们的认知中，`<div>` 元素默认的样式是：`display: block`。产生这种变化的根本原因就在于 “User-Agent-Stylesheet”。

**验证上述例子的最好方式就是审查 `<span>` 和 `<div>` 元素：**

- **当我们审查 `<span>` 元素的时候**，貌似看不到任何样式。这是因为开发者工具是不会显示 “CSS 属性的初始值（Level-1）” 的；相反，会显示默认样式表（“User-Agent-Stylesheet”（Level 2））。**你想一下：** 每个 CSS 属性都有一个初始值（CSS 属性的默认样式（Level-1）），当你调试的时候，肯定不想让所有的初始值都显示出来。`display` 的初始值是 `inline`。因此它不会出现在浏览器的 “审查元素” 调试栏中。
  ![6.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/6.png)
- **当我们审查 `<div>` 元素的时候**，可以看到 `display: block` 样式显示出来了，这是因为 “User-Agent-Stylesheet（Level 2）” 文件中的样式值覆盖了 `display` 属性的初始值 `inline`（“CSS 属性的初始值（Level 1）”）。
  ![7.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/7.png)

### HTML 元素的特定样式由谁来决定？

HTML 元素的默认样式是由 W3C/WHATWG 的 web 标准来决定的。然而，与 Level-1 提到的 “CSS 属性的初始值” 不同的是：所有浏览器都可以根据这个标准来 “添油加醋”，进而定义了不同的默认样式。。。

下图所示的是：根据 CSS2.2 标准，针对 HTML4 而制定的 CSS 默认样式表：

[![8.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/8.png)](https://www.w3.org/TR/CSS22/sample.html)

（附上其它版本的 CSS 标准：[CSS2.1](https://www.w3.org/TR/CSS21/sample.html)，[CSS2.2](https://www.w3.org/TR/CSS22/sample.html)，[CSS3](https://html.spec.whatwg.org/multipage/rendering.html)）。

## Level3——Normalize CSS

------

因为不同浏览器都实现了不同的 “User-Agent-Stylesheet（Level-2）”，所以后来又出现了 **“Normalize CSS”**。**“Normalize CSS”** 可以让样式在不同浏览器上具有相同的显示效果。

### “Normalize CSS” 是什么？它是如何工作的？

**“Normalize CSS”** 指的是：通过创建一份默认的 HTML 样式来覆盖 “User-Agent-Stylesheet（Level 2）”，使得不同的浏览器中的 HTML 元素的默认样式保持一致。而 **[normalize.css](https://necolas.github.io/normalize.css/)** 则是 **“Normalize CSS”** 的一个产物。

**如下所示的样式，是 [normalize.css](https://necolas.github.io/normalize.css/) 中的一部分：**

```css
/**
 * Render the `main` element consistently in IE.
 */
main {
  display: block;
}
```

### Normalize.css 项目

由 Nicolas Gallagher 创建的 **[normalize.css](https://necolas.github.io/normalize.css/)** 是目前最受欢迎的 Normalize.css 项目。

## Level4——CSS Reset

------

在很多场景下，我们可能并不想使用浏览器提供的默认样式（“User-Agent-Stylesheet（level-2）”），比如：`font-size` 和 `margin`，于是 **“CSS Reset”** 便应运而出了。

### “CSS Reset” 是什么？它是如何工作的？

**“CSS Reset”** 指的是：强制覆盖浏览器默认样式表（“User-Agent-Stylesheet（Level-2）”），重置浏览器默认样式表中所有属性的值。以 `<h1>` 到 `<h6>` 标签为例：

```css
h1, h2, h3, h4, h5, h6{
  margin: 0;
  font-size: inherit;
  font-weight: inherit;
}
```

**“CSS Reset”** 类型的样式文件有很多，比较知名的比如：[Eric Meyer](https://meyerweb.com/eric/tools/css/reset/)。但是我更倾向于使用自己的 “CSS Reset” 文件，因为我可以按需来定义样式：

```css
/****** Elad Shechter's RESET *******/
/*** box sizing border-box for all elements ***/
*,
*::before,
*::after{ box-sizing:border-box; }

a{ text-decoration:none; color:inherit; cursor:pointer; }
button{ background-color:transparent; color:inherit; border-width:0; padding:0; cursor:pointer; }
figure{ margin:0; }
ul, ol, dd{ margin:0; padding:0; list-style:none; }
h1, h2, h3, h4, h5, h6{ margin:0; font-size:inherit; font-weight:inherit; }
p{ margin:0; }
cite { font-style:normal; }
fieldset{ border-width:0; padding:0; margin:0; }
```

这里有个误区：**“CSS Reset”** 并不会重置 “User-Agent-Stylesheet（Level-2）” 中所有的样式，比如 `<div>`、`ul`、`section`、`article` 等标签的 `display: block` 属性。

正因为如此，我们可以理解为：**“CSS Reset”** 重置了浏览器默认样式表中大概 80% 的样式。。。

### “Normalize CSS” 和 “CSS Reset” 结合使用

我自己的项目中同时使用了 **“normalize.css”** 和自定义的 **“Reset CSS”**：

- 前者会将所有浏览器的样式纳入 “User-Agent-Stylesheet” 规范
- 后者是我自己创建的 “reset.css”，移除了一些无用的样式

以 “Sass” 为例：

```scss
@import "resets/normalize.scss";
@import "resets/reset.scss";
```

![9.jpeg](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/9.jpeg)

想深入了解 “Normalize CSS” 和 “CSS Reset”，可以阅读我的这篇文章：[Normalize CSS 和 CSS Reset，我该选哪个？](https://medium.com/@elad/normalize-css-or-css-reset-9d75175c5d1e)。

### 总结上述的 4 个 Level

在前面的章节中，我们介绍了**浏览器默认的 CSS 级别**：

- **“CSS 属性的默认样式”（Level-1）**—— 给所有 CSS 属性创建默认值。
- **“User-Agent-Stylesheet”（Level-2）**—— 给某些 HTML 元素添加特定的默认样式。

紧接着，我们介绍了如何控制 CSS 的基础样式：

- **“Normalize CSS”（Level-3）**—— 保持不同浏览器中的 “User-Agent-Stylesheet”（Level 2）的效果一致的样式。
- **“CSS Reset”（Level-4）**—— 覆盖 “User-Agent-Stylesheet”（Level-2）中所有的默认样式。

上述的四个基本的 CSS 级别组成了我们项目中的样式，如下图所示：

![10.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/10.png)

在下面的两个章节，我们将会介绍：

- “CSS 样式重置关键字”（Part-5）
- 重置属性 —— “all”（Part-6）

## Part5——CSS 样式重置关键字

------

CSS 已经提供了一些样式重置关键字：`initial`、`inherit`、`unset`、`revert`，直到最近的个把月才被各个浏览器广泛支持。

![11.jpeg](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/11.jpeg)

### “inherit” 和 “initial”

在本文的开头，我已经提到了有关于 CSS 属性的默认样式（Level-1）以及具有或者不具有继承性的 CSS 属性。

#### ‘inherit’

如果某个 CSS 属性是可继承的属性，那么我们可以为该属性设置一个 `inherit` 值，使得该属性**保持默认的继承样式**。

**举个例子**：我的项目中使用了 `<h1>`。。。`<h6>` 标签，然而我并不想要这些标签的某些默认样式，因此我可以把 `font-size` 和 `font-weight` 属性的值设为 `inherit`，这样就可以利用继承的特性，继承其父级元素对应属性的值。

**代码如下：**

```css
h1, h2, h3, h4, h5, h6 {
  font-size: inherit;
  font-weight: inherit;
}
```

#### ‘initial’

对于那些不具有继承性的 CSS 属性，我们可以把该属性的值设为 `initial`，恢复该属性的值为初始状态。

**举个例子：**

```css
.foo{
  position: absolute;
  top: 20px;
  left: 20px;
}
.bar .foo{
  position: initial;  /* = static */
  top: initial;       /* = auto */
  left: initial;      /* = auto */
}
```

#### ‘unset’

综上所述，对于 “可继承的属性”，我们把属性的值设为 `inherit`；而对于 “不可继承的属性”，我们则把属性值设为 `initial`，两者的根本目的就是将 CSS 属性的值重置为默认状态。

而 ‘unset’ 关键字，可以根据某个 CSS 属性是否可继承来自动重置该属性的值：

- **可继承的属性** —— `unset` 的值就等同于 `inherit`
- **不可继承的属性** —— `unset` 的值等同于 `initial`

举个例子，`position: unset` 的效果等同于 `position: initial`；但 `font-size: unset` 的效果则等同于 `font-size: inherit`。

![12.jpeg](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/12.jpeg)

通过这种方式，你可以使用 `unset` 关键字去重置所有的 CSS 属性。

举个例子：

```css
.common-content * {
  /* 可继承的属性 */
  font-size: unset;
  font-weight: unset;

  /* 不可继承的属性 */
  border-width: unset;
  background-color: unset;
}

/* 等同于 */

.common-content * {
  /* 可继承的属性 */
  font-size: inherit;
  font-weight: inherit;

  /* 不可继承的属性 */
  border-width: initial;
  background-color: initial;
}
```

### 重置样式产生的问题

在某些情况下，重置一个 “不可继承的属性” 的值，可能会产生一些问题。例如：`<div>` 属性的默认值是 `block`，但是使用 `unset` 关键字重置之后，就变成了 `inline`：

```css
display: unset; /* = initial = inline */
```

这是为什么呢？回顾一下文章伊始提到的：“CSS 属性的默认样式”（Level-1）和 “User-Agent-Stylesheet”（Level-2）两者之间的区别。从这我们可以得知：`display` 属性的初始值是 `inline`，这没错，只是由于浏览器默认样式表（“User-Agent-Stylesheet（Level-2）”）中对 `display` 样式进行了覆盖，对其重新赋值为 `block`。

因为 `initial` 的样式不取决于 “User-Agent-Stylesheet”（Level-2），所以 `display: initial` 等同于 `display: inline`：

```css
div{
  display: initial; /* = inline */
}
```

为了解决上述的问题，我们可以使用 `revert` 样式重置关键字：`revert` 可以将某个属性的值重置为浏览器默认样式表（“User-Agent-Stylesheet（Level-2）”）中对应的样式。

#### ‘revert’

`revert` 是最灵活的 CSS 样式重置关键字，`revert` **会进行如下三个操作：**

- 对于 “可继承的属性” —— 把 CSS 属性的值设为 `inherit`
- 对于 “不可继承的属性”，判断 “User-Agent-Stylesheet”（Level-2）中是否存在该属性的样式
  - 情况①：如果存在，则直接使用 “User-Agent-Stylesheet” 中对应的样式
  - 情况②：反之，把 CSS 属性的值设为 `initial`

代码如下：

```css
div{
  font-size: revert; /* = inherit */
  display: revert;   /* = block  (Level-2) */
  position: revert;  /* = static (Level-1) */
}
```

### CSS 样式重置关键字总结

刚开始看到这些 CSS 样式重置关键字的时候，你可能会很困惑，但是当你了解浏览器的基本逻辑之后就会发现，原来是这么回事。

我为此专门花了一份流程图，帮助各位开发者理解 **CSS 的各种样式重置关键字是如何工作的：**

![13.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/13.png)

### 定义一个标准的样式

我们可以通过把诸如 `font-size` 之类的 “可继承的属性” 的值设置为 `initial`，以此来重置属性的值。通过这种方式，我们可以获取到浏览器默认的 `font-size` 大小，并且浏览器默认的 `font-size` 是不受外界影响的，**比如：**

```css
.common-content p {
  font-size: initial;
  /* = 'medium' value - browser default size */
}
```

与此相反的是，我们可以让一个诸如 `padding` 等本来属于 “不可继承的属性” 变为可继承。举个例子：位于内部的 HTML 元素会继承其父元素的 `padding` 属性：

```css
.box{
  padding: 40px;
}
/* <div class="box"> 的后代 <div> 元素都会继承 padding: 40px */
div {
  padding: inherit;
}
```

**在线示例：**

<iframe height="265" style="width: 100%;" scrolling="no" title="inherit css style" src="https://codepen.io/elad2412/embed/preview/hdypx?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/elad2412/pen/hdypx'>inherit css style</a> by Elad Shechter
  (<a href='https://codepen.io/elad2412'>@elad2412</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> PS：仅在有必要的情况下使用上述方式（不推荐）

## Part6——“all” 属性

------

可以使用 `all` 属性来重置某个 HTML 元素上的所有样式：

```css
all: revert;
```

`all` 属性会一次性重置所有的属性，避免一个个去重置。目前较为普遍的用法就是：`all: unset` 和 `all: revert`（“CSS 样式重置关键字” —— Part 5）:

```css
/* 推荐 */
.common-content *{
  all: revert;
}
/* 不推荐 */
.common-content *{
  border: revert;
  font-size: revert;
  display: revert;
  position: revert;
  top: revert;
  left: revert;
}
```

## “CSS Reset 的前景”

------

我们可以假设在若干月后，当诸如 “Samsung Internet” 此类的非主流浏览器完全支持 `revert` 关键字的时候，我们又有一种新的方式去创建 “CSS Reset”（Level-4）。

使用 `all` 属性搭配 `unset/revert`，一切将会变得更简单：

以后的 CSS Reset 可能像下面这样：

```css
/* 移除浏览器默认样式表（“User-Agent-Stylesheet”）中除了 ‘display’ 之外的所有样式 */
* {
  all: unset;
  display: revert;
}
/* 给 box-sizing 设置一个普遍使用的值 */
*, *::before, *::after{
  box-sizing: border-box;
}
```

> PS：**“Samsung Internet”** 基于 “Chrome” 引擎，但是更新较为缓慢。

## 总结

------

在本文中，我讲解了很多关于 CSS 的知识：

- 浏览器中的 CSS 是如何构建的
- 如何在项目中按需自定义 CSS
- 如何通过你自己的方式来管理 CSS 属性

**与本文主题相关的其它文章：**

- [Normalize CSS 和 CSS Reset，我该如何选择？](https://yyge.top/blog/2021/02/22/%E3%80%90%E8%AF%91%E3%80%91NormalizeCSS%E5%92%8CResetCSS%EF%BC%8C%E6%88%91%E8%AF%A5%E5%A6%82%E4%BD%95%E9%80%89%E6%8B%A9%EF%BC%9F/)
- [理解 CSS 的样式重置关键字：“Initial”、“Inherit”、“Unset”](https://yyge.top/blog/2021/02/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/)

**本文也参考了其它的文章：**

- [理解 User Agent Style Sheets](https://meiert.com/en/blog/user-agent-style-sheets/)
- [StackOverflow —— 浏览器默认的 CSS](https://stackoverflow.com/questions/6867254/browsers-default-css-for-html-elements)