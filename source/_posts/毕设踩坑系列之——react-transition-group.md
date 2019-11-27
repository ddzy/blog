---
title: 毕设踩坑系列之——react-transition-group
date: 2019-07-21 11:29:07
tags: [毕设]
categories: [frontend]
---

记录有关`react-transition-group`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

### [2019-8-13]

#### Added

- 新增问题`长列表 + 切换路由时卡顿`

## 记录

------

### 1. 阻止某个页面的路由过渡

**解决办法:**

<details>
<summary>展开代码</summary>

```jsx
<CSSTransition
  key={
    /(\/user\/.+) | (\/home\/\w+)/.test(props.location.pathname)
      ? ''
      : props.location.pathname
  }
>...</CSSTransition>
```
</details>

### 2. 长列表 + 切换路由时卡顿

**解决办法:**

将`CSSTransition`的`exit`配置项设置为`false`, 取消掉出场动画, 可减缓卡顿.

<details>
<summary>展开代码</summary>

```ts
<CSSTransition
  key={v._id}
  timeout={300}
  // 将该项设置为false
  exit={false}
  classNames={'fadeTranslateZ'}
>
  <ContentMainItem>
    <BaseChatMessage
      isSend={isSend}
      chatMessageInfo={chatMessageInfo}
    />
  </ContentMainItem>
</CSSTransition>
```
</details>