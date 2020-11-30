---
title: 关于 React 的一些疑点思考
date: 2020-11-30 08:39:14
tags: [react, js, fiber]
categories: [frontend]
---

对于 React, 有一些比较难理解的知识点, 在通过阅读源码时, 都可以大概了解, 这里将常见的问题归纳起来(仅代表个人思考).

<!-- more -->

## 更新

------

### [2020-11-30]

- Initial release

## 问题总结

------

### 什么是虚拟 DOM?

虚拟 DOM, 实际上就是使用 JS 在其内部维护了一个对象, 是真正的 DOM 对象到 JS 对象的映射. 比如说一个 div 标签可以这么表示:

```js
{
  tag: 'div',
  attr: [],
  children: [
    {
      tag: xxx,
    }
  ],
}
```

诸如 React、Vue 这样的库, 都在内部维护了一个虚拟 DOM 树, 每次发生更改, 都会使用 `diff` 算法比对新老 DOM 树之间的差异, 找出更新的部分, 在 `commit` 阶段应用到真正的 DOM 上. 但是需要注意, React16 之后的 `diff` 算法比对的是 `Fiber`.

### React 组件的生命周期?

![React16生命周期流程图](https://raw.githubusercontent.com/ddzy/react-reading-sources/master/images/7-react16%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

### 受控组件 & 非受控组件?

- 受控组件, 就是说一个组件的数据、行为都由框架本身来管控
- 就拿最基本的 `Input` 输入框组件, 如果通过 `onChange`、`onInput` 等事件钩子来获取输入框的值, 并且将值保存到 state 中维护, 这时就是受控的; 反之, 直接通过 `document.querySelector('input').value` 的形式来获取值, 则是非受控的
- React 推荐使用受控组件

### shouldComponentUpdate 的作用?

- 在 ClassComponent 类组件进行更新之前, 通过该生命周期来判断当前组件是否需要进行更新
- React 提供了 `PureComponent` 和 `memo` 两个方法, 可以实现和 `shouldComponentUpdate` 一样的功能, 底层都是通过一个浅比较函数来实现的

### ref 和 forwardRef 的作用?

- 通过 ref 可以获取到某个组件的实例(ReactElement => ReactElement 实例、DOM => DOM 元素实例)
- 由于 `FunctionComponent` 是无状态组件, 并且不能被实例化, 所以可以使用 `forwardRef` 去强制传递一个 ref

### key 属性的作用?

- React 在渲染一个列表元素的时候, 需要给每个列表元素添加一个唯一的 key 属性
- 这个 `key` 会在创建 React 组件实例的时候变为 `ReactElement` 上的一个属性字段
- 本质上就是为了 `diff` 算法, 如果组件指定了一个 key 属性, 那么 React 会将这个 key 当作当前组件的匹配标识. 在下次进行 `Fiber-Diff` 的时候, 如果老的 `Fiber` 上具有 key 属性, 并且和新的 key 相同, 那么就不会销毁并创建组件

### 组件通信方式?

![React 组件通信方式](https://raw.githubusercontent.com/ddzy/react-reading-sources/master/images/8-react%E7%BB%84%E4%BB%B6%E9%80%9A%E4%BF%A1%E6%96%B9%E5%BC%8F.png)

### setState 是同步还是异步的?

![setState 是同步还是异步的](https://raw.githubusercontent.com/ddzy/react-reading-sources/master/images/9-setState%E6%98%AF%E5%90%8C%E6%AD%A5%E8%BF%98%E6%98%AF%E5%BC%82%E6%AD%A5.png)