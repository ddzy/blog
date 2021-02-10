---
title: 【译】深度解析CSS是如何工作的
date: 2021-02-05 08:52:50
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/how-does-css-work-92fe7116916d

<!-- more -->

## 更新

------

### [2021-2-5]

- Initial release

### [2021-2-9]

#### Added

- 完成 `概述` 章节
- 完成 Level1 的 `初始值` 章节

## 概述

------

> 副标题（补充）：理解浏览器默认的样式行为

当你为网站中的某个 DOM 元素编写 CSS 样式之前，浏览器会给这个 DOM 元素添加一个默认的样式。

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

第二个有趣的特性就是：每个 CSS 属性都有一个 “继承行为”。也就是说，假设我们给某个 HTML 元素设置了一些特定的样式，那么该元素的后代都会继承这些样式。

一般情况下，以下两种类型的 CSS 属性会具有继承的行为：

 - 文本类型：`font-family`、`font-size`、`color`、`text-align`
 - 排版类型：[CSS 排版属性介绍](https://medium.com/cssclass-com/css-basics-for-typography-160025e3aeca)

**想象一下：**当你给 `<body>` 元素设置了 `font-size: 20px` 属性，那么 `<body>` 元素的所有后代元素都会继承这个 `font-size`，除非你给某个后代元素重新设置了 `font-size`。

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

例如在下图中，`font-size` 属性是可继承的，所以会高亮显示。反之，`padding` 是不可继承的，所以会置灰显示：

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

### HTML 元素的特殊样式由谁来决定？

## Level3——Normalize CSS

------

### “Normalize CSS” 是什么？它是如何工作的？

### Normalize.css 项目

## Level4——CSS Reset

------

### “CSS Reset” 是什么？它是如何工作的？

### 综合理解 “Normalize CSS” 和 “CSS Reset”

### 总结上述的 4 个 Level

## Part5——CSS 样式重置关键字

------

### “inherit” 和 “initial”

### “initial” 产生的问题

### CSS 样式重置关键字总结

### 定义一个标准的样式

## Part6——“all” 属性

------

## “CSS Reset 的前景”

------

## 总结

------