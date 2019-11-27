---
title: 毕设踩坑系列之——antd
date: 2019-07-21 11:28:47
tags: [毕设]
categories: [frontend]
---

记录有关`antd`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

### [2019-7-22]

#### Added

- 新增问题`Type '"primary"' is not assignable to type 'undefined'.` && `Types of property 'type' are incompatible.`

### [2019-7-24]

#### Added

- 新增问题`Tabs、Collapse等组件的'defaultActiveKey'与路由'pathname`不匹配.`

### [2019-7-28]

#### Added

- 新增问题`路由跳转, 不重新刷新页面的情况下, antd的TabPane无法与pathname同步`.

## 记录

------

### 1. 父组件处理子组件的表单数据

**解决**:

<details>
<summary>展开代码</summary>

```ts
export default withRouter(Form.create({
  onFieldsChange(props, changedFields) {
    props.onCollectionInputChange(changedFields);
  },
  mapPropsToFields(props) {
    return {
      collection_input: Form.createFormField({
        ...props.collectionInputValue,
        value: props.collectionInputValue.value || '',
      }),
    };
  },
}))(components);
```
</details>

### 2. Types of property 'type' are incompatible && Type '"primary"' is not assignable to type 'undefined.

**相关依赖:**

- @types/antd@1.0.0
- @types/react@16.8.23
- @types/react-dom@16.8.4

**问题原因**:

`antd`的类型包与最新的`react`不兼容

**解决方法**:

看到了`antd`的`issue`:

https://github.com/ant-design/ant-design/issues/15700#issuecomment-477333557

只需要升级`@types/react`类型包到`16.8.8`版本即可.

### 3. `Tabs、Collapse等组件的'defaultActiveKey'与路由'pathname`不匹配.

**相关依赖:**

- antd@3.12.3

**问题原因:**

`defaultActiveKey`默认指向当前的第一个`tab-pane`, 但是我的需求是`tab-pane`要与路由`pathname`对应, 这也就导致, 当我在另外一个路由刷新时, `defaultActiveKey`一直指向第一个.

**解决办法:**

只需做个简单的处理即可

<details>
<summary>展开代码</summary>

```tsx
  function handleTabsDefaultActiveKey() {
    // 当前的tab-pane的key
    const basePathname = ['interfaces', 'friends', 'groups', 'collections'];
    const currentPathname = props.location.pathname;
    const processedPathname = basePathname.find((v) => currentPathname.includes(v));

    return processedPathname ? processedPathname : basePathname[0];
  }
```
</details>

然后直接使用

<details>
<summary>展开代码</summary>

```tsx
  <Tab defaultActiveKey={handleTabsDefaultActiveKey()}>
    <Tab.pane />
  </Tab>
```
</details>

### 4. 路由跳转, 不重新刷新页面的情况下, antd的TabPane无法与pathname同步

**相关依赖:**

- antd@3.12.3

**问题原因:**

与上述第`三`个问题类似, 不同的是, 这次的需求是, 需要在另外一个`tabPane`来切换到另一个`tabPane`的路由, 但是出现的情况是`url`变了, 但是`antd`的`tabs`却没有跳转到对应的`tabpane`, 很蛋疼.

**解决办法:**

很简单, 只需要模拟点击`tabpane`就行了, 可以用`MouseEvent`自定义`click`事件, 通过`dom`触发. 我将其封装成了工具方法:

<details>
<summary>展开代码</summary>

```tsx
/**
 * 自定义模拟鼠标事件
 * @param element DOM元素
 * @param type 鼠标事件类型
 * @param callback 处理器
 */
export function customMouseEvent(
  element: Element,
  type: 'click' | 'dblclick' | 'mouseup' | 'mousedown',
  callback?: (event: MouseEvent) => void,
): void {
  if (element['click'] && typeof element['click'] === 'function') {
    element['click']();
  } else {
    const event = new MouseEvent(type, {
      bubbles: true,
    });

    element.dispatchEvent(event);
  }

  element.addEventListener(type, (e) => {
    callback && callback(e as MouseEvent);
  });
}
```
</details>