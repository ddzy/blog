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

```markdown
.parent {
  **display: flex;**
  **align-items: center;**
  **justify-content: center;**
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

```markdown
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
  **mix-blend-mode: overlay;**
}
```

在线代码如下：

<iframe height="314" style="width: 100%;" scrolling="no" title="Mix blend mode" src="https://codepen.io/annafromduomly/embed/preview/NWWdOPN?height=314&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/annafromduomly/pen/NWWdOPN'>Mix blend mode</a> by Anna
  (<a href='https://codepen.io/annafromduomly'>@annafromduomly</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 3. 响应式的网格

------

