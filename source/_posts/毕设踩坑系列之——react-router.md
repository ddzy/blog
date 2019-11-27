---
title: 毕设踩坑系列之——react-router
date: 2019-07-21 11:27:04
tags: [毕设]
categories: [frontend]
---

记录有关`react-router`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

### [2019-7-22]

#### Added

- 新增问题`react-router-dom-v4嵌套组件, href改变, 但是子组件不渲染`

## 记录

------


### 1. `Link` 点击多次, 组件多次渲染的问题

**解决**: 利用`shouldComponentUpdate`判断前后的`pathname`是否相同, 来进行优化.

<details>
<summary>展开代码</summary>

```ts
...
public static getDerivedStateFromProps(
  nextProps: IAdminProps,
) {
  return {
    pathname: nextProps.location.pathname,
  };
}

public readonly state = {
  pathname: '',
};

public shouldComponentUpdate(
  nextProps: IAdminProps,
): boolean {
  const currentPathname: string = this.state.pathname;
  const nextPathname: string = nextProps.location.pathname;

  return nextPathname !== currentPathname;
}
...
```
</details>

### 2. Failed prop type: Invalid prop `component` of type `object` supplied to `Route`, expected `function`

**解决**:

<details>
<summary>展开代码</summary>

```jsx
<Route
  component={
    () => <App />
  }
/>
```
</details>

**也可以:**

<details>
<summary>展开代码</summary>

```jsx
<Route
  render={
    () => <App />
  }
/>
```
</details>

### 3. react-router-dom-v4嵌套组件, href改变, 但是子组件不渲染

**相关依赖:**

- redux@4.0.0
- react-redux@6.0.0
- react-router-dom@4.3.1

**产生原因:**

困扰了我8个月的问题😂😂😂...

今日无意中在`react-router-dom`官网找到原因, 粘贴一下原文:

https://reacttraining.com/react-router/core/guides/redux-integration

<details>
<summary>展开代码</summary>

```markdown
Generally, React Router and Redux work just fine together. Occasionally though, an app can have a component that doesn’t update when the location changes (child routes or active nav links don’t update).

一般情况下, `react-router`和`redux`是绝妙的搭配. 但是有时会出现突发情况, 当`location`发生改变时, `react`应用可能不会发生更新(特别是`子路由`和`activeLink`锚点).

This happens if:

1. The component is connected to redux via connect()(Comp).
2. The component is not a “route component”, meaning it is not rendered like so: <Route component={SomeConnectedThing}/>

1. 组件通过`connect()(Comp)`连接`redux`时
2. 当通过`<Route component={SomeConnectedThing} />`定义路由, 此时`SomeConnectedThing`并不是一个`route`组件.

The problem is that Redux implements shouldComponentUpdate and there’s no indication that anything has changed if it isn’t receiving props from the router. This is straightforward to fix. Find where you connect your component and wrap it in `withRouter`.

产生这种问题的根本原因是`redux`内部实现了`shouldComponentUpdate`, 如果它的`props`没有变化, 那么就不会产生更新, 从而子组件不会重新渲染. **解决办法是使用`withRouter`包裹该组件**.
```
</details>

**解决办法:**

- 如果在`component`中传递的不是当前组件的子组件, 需要在其每个孩子组件加上`withRouter`, 因为当前组件发生更改了, 其子组件的`props`并没有发生变化, 后续同理.

- 还有另一种办法, 就是不要用`React.memo`, 或者`PureComponent`.