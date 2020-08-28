---
title: vue摸摸手系列之——双向绑定的两种实现
date: 2020-08-27 15:42:56
tags: [vue, js]
categories: [frontend]
---

写过一小段时间的 Vue, 发现在特定的场景下, 其可以灵活地操控 JS 对象、输入框、展示视图三者的关系. 所以使用 ES5 的 defineProperty 和 ES6 的 Proxy 分别简单的实现这种特性.


<!-- more -->


## 更新

------

### [2020-8-27]

- Initital release

## 几个要点

------

- 输入框变化 -> JS 对象改变 -> 视图改变
- JS 对象变化 ->输入框改变 -> 视图改变

## defineProperty

------

```html
<p id="userInputShow"></p>
<input type="text" id="userInput">
```

```js
const data = {
  value: '',
};
const oInputShow = document.querySelector('#userInputShow');
const oInput = document.querySelector('#userInput');

Object.defineProperty(data, 'value', {
  configurable: true,
  enumerable: true,
  get() {
    return this.value;
  },
  set(value) {
    oInputShow.innerHTML = value;
    oInput.value = value;
  },
});

oInput.addEventListener('input', (e) => {
  data.value = e.target.value;
});
```

## Proxy

------

```js
const data = {
  value: '',
};
const oInput = document.querySelector('#userInput');
const oInputShow = document.querySelector('#userInputShow');

const proxy = new Proxy(data, {
  get(obj, prop) {
    return obj[prop];
  },
  set(obj, prop, v) {
    obj[prop] = v;
    oInputShow.innerHTML = v;
    oInput.value = v;
  },
});
oInput.addEventListener('input', (e) => {
  proxy.value = e.target.value;
});
```