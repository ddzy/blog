---
title: Web开发者应该知道的5个CSS小技巧
date: 2021-02-03 11:52:10
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://medium.com/javascript-in-plain-english/5-useful-css-tips-every-web-developer-should-know-5c19a088620e

## 更新

------

### [2021-2-3]

- Initial release

## 介绍

------

CSS 是一个优秀的样式语言，通过装饰 HTML 元素来构建多样化的 web 页面。在某些方面，合理的使用 CSS 可以大大的节省开发时间。在本文中，我将介绍一些 CSS 技巧，这些技巧是我在做了大量项目之后总结而来的。现在让我们开始吧。。。

## 1. 居中一个 div 的简单方式

------

让一个 div 居中的最简单的方式之一是使用 Flexbox。Flexbox 可以很方便的控制元素的位置和排列方式。

示例如下：

```diff
.parent {
+  display: flex;
+  align-items: center;
+  justify-content: center;
  height: 100vh;
  width: 100wh;
}

.child {
  width: 100px;
  height: 100px;
  background-color: #20126f;
}
```

在线代码如下：

<iframe height="265" style="width: 100%;" scrolling="no" title="Flexbox" src="https://codepen.io/MehdiAoussiad/embed/preview/KKgbrGx?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/MehdiAoussiad/pen/KKgbrGx'>Flexbox</a> by Mehdi Aoussiad
  (<a href='https://codepen.io/MehdiAoussiad'>@MehdiAoussiad</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

如果你想了解更多有关于 Flexbox 的知识的话，可以看我的这一篇文章：

> [通过案例详解 CSS Flexbox 布局](https://medium.com/javascript-in-plain-english/css-flexbox-explained-with-examples-85efa38e4770)

## 2. 混合模式

------

CSS 可以做很多有趣的东西，比如：`混合模式`。混合模式有两个属性：

- mix-blend-mode：定义某个元素和它的子元素如何混合
- background-blend-mode：定义某个元素的背景图片和背景颜色之间如何混合

下面的示例中，我们在 div 中包裹了一个图片和文本：

*HTML：*

```html
<div class="blend">
  <img src="imageSrc" />
   <h1>NATURE</h1>
</div>
```

*CSS：*

```diff
.blend {
  width: 100vw;
  height: 500px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.blend img {
  position: absolute;
}
.blend h1 {
  font-size: 150px;
+  mix-blend-mode: overlay;
}
```

在线代码如下：

<iframe height="314" style="width: 100%;" scrolling="no" title="Mix blend mode" src="https://codepen.io/annafromduomly/embed/preview/NWWdOPN?height=314&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/annafromduomly/pen/NWWdOPN'>Mix blend mode</a> by Anna
  (<a href='https://codepen.io/annafromduomly'>@annafromduomly</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 3. 响应式的网格

------

利用 grid 特性可以轻易地构建响应式布局，如下示例：

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 10px;
}
```

举个例子，如果你正在负责一个电商 app 项目。你的任务是实现响应式的商品列表，以此来适配多终端。在此需求下，我们可能会这么做：

- PC：每行展示三个条目
- 平板：每行展示两个条目
- 手机：每行只展示一条

具体可以看下面的代码：

*HTML：*

```html
<div class="grid">
  <div class="item">Product 1</div>
  <div class="item">Product 2</div>
  <div class="item">Product 3</div>
  <div class="item">Product 4</div>
  <div class="item">Product 5</div>
  <div class="item">Product 6</div>
</div>
```

*CSS：*

```diff
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: sans-serif;
}
body{
  height: 100vh;
  margin-top: 150px;
}
+.grid {
+  display: grid;
+  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
+  grid-gap: 10px;
+}
.item{
  background: red;
  padding: 20px;
  color: wheat;
}
```

在线代码如下所示，点击 `EDIT ON CODEPEN` 按钮，之后调整窗口大小，就可以看到效果了：

<iframe height="265" style="width: 100%;" scrolling="no" title="CSS Grid" src="https://codepen.io/MehdiAoussiad/embed/preview/zYKyyYY?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/MehdiAoussiad/pen/zYKyyYY'>CSS Grid</a> by Mehdi Aoussiad
  (<a href='https://codepen.io/MehdiAoussiad'>@MehdiAoussiad</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 4. 使用 filter 函数处理图片

------

`filter()` 函数通常适用于图像处理，可以改变图片以及元素的外观。`filter()` 有如下的取值（`blur`，`brightness`，`contrast`，`grayscale`，`hue-rotate`，`opacity`，`invert`，`sepia`，`saturate`，`drop-shadow`）。

如下示例：

```diff
.element1 {
+  filter: drop-shadow(0.25rem 0 0.75rem #ef9035);
}
// Or：.element2 {
+  filter: blur(20px);
}
```

在线代码示例如下：

<iframe height="265" style="width: 100%;" scrolling="no" title="The filter function" src="https://codepen.io/duanzhaoyang/embed/preview/PobPaVr?height=265&theme-id=light&default-tab=html,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/duanzhaoyang/pen/PobPaVr'>The filter function</a> by duanzhaoyang
  (<a href='https://codepen.io/duanzhaoyang'>@duanzhaoyang</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 5. 背面可见度

------

