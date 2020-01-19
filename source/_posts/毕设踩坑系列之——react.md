---
title: 毕设踩坑系列之——react
date: 2019-07-21 11:26:38
tags: [毕设]
categories: [frontend]
---

记录有关`react`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

### [2020-1-15]

#### Added

- 新增问题: `Warning: React.createElement: type is invalid -- expected a string (for built-in components) or a class/function (for composite components) but got: undefined. You likely forgot to export your component from the file it's defined in, or you might have mixed up default and named imports.`

## 记录

------

### 1. 生命周期相关

#### 1.1 `componentDidMount`执行两次的问题

**原因**: 使用`react-transition-group`作路由过渡, 每次enter和leave的时候, componentDidMount会执行两次

**解决**: 重构`RouterConfig`

<details>
<summary>展开代码</summary>

```jsx
...
<CSSTransition exit={false}>
  <Switch location={props.location}>
    {...}
  </Switch>
</CSSTransition>
...
```
</details>

#### 1.2 Can't setState (or forceUpdate) on an unmounted component, This is a no-op, but it indicates a memory leak in your application.

**原因**: 未及时清理副作用

**解决**: `componentWillUnmount` 中清理 `定时器` | `监听器` | `setState` 等.

### 2. 其它

#### 2.1 Warning: React.createElement: type is invalid -- expected a string (for built-in components) or a class/function (for composite components) but got: undefined. You likely forgot to export your component from the file it's defined in, or you might have mixed up default and named imports.

**产生原因**:

**解决办法**: