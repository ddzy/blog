---
title: 深度探索styled-components工作原理
date: 2020-12-15 16:26:15
tags: [react, styled-components, css]
categories: [frontend, translation]
---

原文链接:

https://medium.com/styled-components/how-styled-components-works-618a69970421

<!-- more -->

## 更新

------

### [2020-12-25]

- Initial release

### [2020-12-28]

#### Changed

- 更改文章标题, 避免 hexo 部署的问题

## 概述

------

现在的前端开发特别是 React 社区, CSS-in-JS 越来越常见了. [styled-components](https://www.styled-components.com/) 凭借着以下几种特性 [脱颖而出](https://github.com/tuchk4/awesome-css-in-js#libraries):

- 它基于 [标记模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates) 语法
- 以编写 React 组件的形式来定义样式
- 解决 CSS 模块化的问题
- 提供了 CSS 不具备的功能, 比如嵌套
- 上述特性都无需配置

开发者不再需要费尽脑汁去想 CSS 的类名. 那么, 上面所说的种种, 是如何实现的呢?

**注意:** 如果你不熟悉 styled-components, 请先阅读 [这篇文档](https://www.styled-components.com/docs)

## 魔性语法

------

我们使用 styled-components 创建一个简单的按钮:

```js
const Button = styled.button`
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
  font-size: 1rem;
`;
```

![img](https://miro.medium.com/max/306/1*8wJLnE1QXl8jtkbp2AWQmw.png)

[在线示例](https://jsfiddle.net/gevgeny/r07th16o/?utm_source=website&utm_medium=embed&utm_campaign=r07th16o)

`styled.button` 只是 `styled('button')` 的简写, `styled` 方法接收一个 [可用的标签名称](https://github.com/styled-components/styled-components/blob/v3.3.3/src/utils/domElements.js#L4) 作为参数. 如果你熟悉标记模板的话, 你就会知道, 其实 `button` 只是一个函数, 可以接收一个字符串数组作为参数. 看一下下面的代码:

```js
const Button = styled('button')([
  'color: coral;' +
  'padding: 0.25rem 1rem;' +
  'border: solid 2px coral;' +
  'border-radius: 3px;' +
  'margin: 0.5rem;' +
  'font-size: 1rem;'
]);
```

[在线示例](https://jsfiddle.net/gevgeny/o3zhc7xw/)

现在你可以看到其实 `styled` 就是一个组件工厂, 我们可以想象一下它是怎么实现的.

## 重构 styled-components

------

```js
const myStyled = (TargetComponent) => ([style]) => class extends React.Component {
  componentDidMount() {
    this.element.setAttribute('style', style);
  }

  render() {
    return (
      <TargetComponent {...this.props} ref={element => this.element = element } />
    );
  }
};

const Button = myStyled('button')`
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
  font-size: 1rem;
`;
```

[在线示例](https://jsfiddle.net/gevgeny/dxc6gumn/?utm_source=website&utm_medium=embed&utm_campaign=dxc6gumn)

上面的代码实现看起来很简单——`myStyled` 工厂函数基于给定的标签名创建了一个新的组件, 在组件挂载之后设置行内样式. 但是如果我们的组件样式依赖于某个  props   呢?

```javascript
const primaryColor = 'coral';

const Button = styled('button')`
  background: ${({ primary }) => primary ? 'white ' : primaryColor};
  color: ${({ primary }) => primary ? primaryColor : 'white'};
  padding: 0.25rem 1rem;
  border: solid 2px ${primaryColor};
  border-radius: 3px;
  margin: 0.5rem;
`;
```

为了在组件挂载或者组件的 props 更新的时候计算样式中的相关插值, 我们需要更新上面代码的实现:

```javascript
const myStyled = (TargetComponent) => (strs, ...exprs) => class extends React.Component {
  interpolateStyle() {
    const style = exprs.reduce((result, expr, index) => {
      const isFunc = typeof expr === 'function';
      const value = isFunc ? expr(this.props) : expr;

      return result + value + strs[index + 1];
    }, strs[0]);

    this.element.setAttribute('style', style);
  }

  componentDidMount() {
    this.interpolateStyle();
  }

  componentDidUpdate() {
    this.interpolateStyle();
  }

  render() {
    return <TargetComponent {...this.props} ref={element => this.element = element } />
  }
};

const primaryColor = 'coral';

const Button = myStyled('button')`
  background: ${({ primary }) => primary ? primaryColor : 'white'};
  color: ${({ primary }) => primary ? 'white' : primaryColor};
  padding: 0.25rem 1rem;
  border: solid 2px ${primaryColor};
  border-radius: 3px;
  margin: 0.5rem;
  font-size: 1rem;
`;
```

[在线示例](https://jsfiddle.net/gevgeny/et4wu96y/?utm_source=website&utm_medium=embed&utm_campaign=et4wu96y)

上述代码最棘手的部分在于如何得到样式字符串:

```javascript
const style = exprs.reduce((result, expr, index) => {
  const isFunc = typeof expr === 'function';
  const value = isFunc ? expr(this.props) : expr;

  return result + value + strs[index + 1];
}, strs[0]);
```

我们把所有的字符串片段拼接得到一个一个的 result; 如果某个插值是函数类型, 那么就会把组件的 props 传递给它, 同时调用它.

上面这个简单的工厂看起来很像 styled-components 提供的, 但是实际上 styled-components 的底层实现更加有意思: **它不用内联样式**. 让我们走近 styled-components 以了解当导入并且创建组件的时候究竟发生了什么.

## `styled-components` 底层原理

------

### 引入 styled-components

当你首次引入 styled-components 库的时候, 它内部会创建一个 `counter` 变量, 用来记录每一个通过 `styled` 工厂函数创建的组件.

### 调用 styled.tag-name 工厂函数

```javascript
const Button = styled.button`
  font-size: ${({ sizeValue }) => sizeValue + 'px'};
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
  &:hover {
    background-color: bisque;
  }
`;
```

styled-components 创建新组件的同时会给该组件创建一个 `componentId` 标识符. 代码如下:

```javascript
counter++;
const componentId = 'sc-' + hash('sc' + counter);
```

第一个创建的 styled-components 组件的 `componentId` 为 `sc-bdVaJa`

一般情况下 styled-components 会使用 [MurmurHash](https://www.wikiwand.com/en/MurmurHash) 算法创建唯一的标识符, 接着将 [哈希值转化为乱序字母组成的字符串](https://github.com/styled-components/styled-components/blob/v3.3.3/src/utils/generateAlphabeticName.js#L13).

一旦创建好标识符, styled-components 会将 `<style>` 元素插入到 `<head>` 内部, 并且插入一条带有 `componentId` 的注释, 就像下面这样:

```html
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
</style>
```

创建好新组件之后, `componentId` 和 `target` 都会以静态属性的形式存储于 `button` 这个组件上:

```javascript
StyledComponent.componentId = componentId;
StyledComponent.target = TargetComponent;
```

可以看到, 仅仅创建一个 styled-components 组件, 并不会消耗太多性能. 甚至如果你定义了成百上千的组件而不去使用它们, 你最终得到的也只是一个或多个带有注释的 `<style>` 元素.

通过 `styled` 工厂函数创建的组件有个很重要的点: 它们都继承了一个隐藏的 `BaseStyledComponents` 类, 这个类实现了一些生命周期方法. 让我们看一下.

`componentWillMount()`

我们给 `Button` 组件创建一个实例并挂载到页面上:

```js
ReactDOM.render(
  <Button sizeValue={24}>I'm a button</Button>,
  document.getElementById('root')
);
```

`BaseStyledComponent` 组件的 `componentWillMount()` 生命周期被调用了, 这释放了一些重要信号:

1. **解析标记模板**: 这个算法和我们实现过的 `myStyled` 工厂很相似. 对于 `Button` 组件的实例:

```html
<Button sizeValue={24}>I'm a button</Button>
```

我们得到了如下所示的 CSS 样式字符串:

```css
font-size: 24px;
color: coral;
padding: 0.25rem 1rem;
border: solid 2px coral;
border-radius: 3px;
margin: 0.5rem;
&:hover {
  background-color: bisque;
}
```

2. **生成 CSS 类名**: 每个组件实例都会有一个唯一的 CSS 类名, 这个类名也是基于 [MurmurHash](https://www.wikiwand.com/en/MurmurHash) 算法、`componentId` 以及 `evaluatedStyles` 字符串生成的:

```javascript
const className = hash(componentId + evaluatedStyles);
```

所以我们的 `Button` 实例生成的 `className` 是 `jsZVzX`.

之后这个类名会保存到组件的 state 上, 字段名为 `generatedClassName`.

3. **预处理 CSS**: 我们使用流行的 CSS 预处理器——[stylis](https://github.com/thysultan/stylis.js), 提取 CSS 字符串:

```javascript
const selector = '.' + className;
const cssStr = stylis(selector, evaluatedStyles);
```

下面是 `Button` 实例最终的 CSS 样式:

```css
.jsZVzX {
  font-size: 24px;
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
}
.jsZVzX:hover{
  background-color: bisque;
}
```

4. **将 CSS 字符串注入到页面上**: 现在可以将 CSS 注入到 `<style>` 标签内部的带有组件标识注释的后面:

```css
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
  .sc-bdVaJa {} .jsZVzX{font-size:24px;color:coral; ... }
  .jsZVzX:hover{background-color:bisque;}
</style>
```

正如你看到的, styled-components 也将 `componentId`(`.sc-bdVaJa`) 注入到页面上, 并且没有给 `.sc-bdVaJa` 定义样式.

`render()`

当完成 CSS 的相关工作后, styled-components 只需要去创建组件的类名(`className`)即可:

```js
const TargetComponent = this.constructor.target; // In our case just 'button' string.
const componentId = this.constructor.componentId;
const generatedClassName = this.state.generatedClassName;

return (
  <TargetComponent
    {...this.props}
    className={this.props.className + ' ' + componentId + ' ' + generatedClassName}
  />
);
```

styled-components 给渲染的元素(`TargetComponent`)添加了 3 个类名:

1. `this.props.className` —— 从父组件传递过来的类名, 是可选的.
2. `componentId` —— 一个组件唯一的标识, 但是要注意不是组件实例. 这个类名没有 CSS 样式, 但是当需要 [引用其它组件](https://www.styled-components.com/docs/advanced#referring-to-other-components) 的时候, 可以作为一个嵌套选择器来使用.
3. `generatedClassName` —— 具有 CSS 样式的组件的唯一前缀

很棒! 最终渲染出来的 HTML 是这样的:

```html
<button class="sc-bdVaJa jsZVzX">I'm a button</button>
```

`componentWillReceiveProps()`

现在让我们尝试着在 Button 组件挂载完成之后更改它的 props. 需要做的是给 `Button` 组件添加一个交互式的事件:

```js
let sizeValue = 24;

const updateButton = () => {
  ReactDOM.render(
    <Button sizeValue={sizeValue} onClick={updateButton}>
      Font size is {sizeValue}px
    </Button>,
    document.getElementById('root')
  );
  sizeValue++;
}

updateButton();
```

[在线示例](https://jsfiddle.net/gevgeny/0ezu72Ly/)

你点击一次按钮, `componentWillReceiveProps()` 会被调用, 并且 `sizeValue` 会自增, 之后的流程和 `componentWillMount()` 一样:

1. 解析标记模板
2. 生成新的 CSS 类名
3. [stylis](https://github.com/thysultan/stylis.js) 预处理样式
4. 将 CSS 注入到页面上

在多次点击按钮之后查看浏览器开发者工具, 可以看到:

```html
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
  .sc-bdVaJa {}
  .jsZVzX{font-size:24px;color:coral; ... } .jsZVzX:hover{background-color:bisque;}
  .kkRXUB{font-size:25px;color:coral; ... } .kkRXUB:hover{background-color:bisque;}
  .jvOYbh{font-size:26px;color:coral; ... } .jvOYbh:hover{background-color:bisque;}
  .ljDvEV{font-size:27px;color:coral; ... } .ljDvEV:hover{background-color:bisque;}
</style>
```

是的, 所有类只有 `font-size` 属性不同, 并且无用的 CSS 类都没有被移除. 这是为什么? 因为移除无用的类会增加性能开销, 具体可以看 [这篇文章](https://github.com/styled-components/styled-components/issues/1431#issuecomment-358097912).

这里有个小的优化点: 可以添加一个 `isStatic` 变量, 在 `componentWillReceiveProps()` 检查这个变量, 如果组件不需要插入样式的话, 直接跳过, 从而避免不必要的样式计算.

## 性能优化技巧

------

了解了 styled-components 底层是如何工作的, 之后才能更好的专注于性能优化.

这是一个有彩蛋的 [按钮例子](https://jsfiddle.net/gevgeny/0ezu72Ly/)(*提示: 点击按钮超过 200 次, 你会在控制台看到 styled-components 的隐藏信息. 不是开玩笑的哦*!😉).

下面就是隐藏的彩蛋:

```plain
styled.button 组件生成了超过 200 个类名
需要频繁更改样式的话, 可以考虑使用 attrs 方法
Example:
  const Component = styled.div.attrs({
    style: ({ background }) => ({
      background,
    }),
  })`width: 100%;`
<Component />
```

重构后的 `Button` 组件是这样的:

```js
const Button = styled.button.attrs({
  style: ({ sizeValue }) => ({ fontSize: sizeValue + 'px' })
})`
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
  &:hover {
    background-color: bisque;
  }
`;
```

然而, 并不是所有的动态样式都应该采取这种方式. **我自己的规则是对于起伏比较大的数值, 使用 style 属性**. 比如:

- 像 [<u>word cloud</u>](https://www.jasondavies.com/wordcloud/) 这种可以高度定制 `font-size` 的组件
- 从服务端获取的具有不同颜色的标签列表

但是, 如果你的按钮是多样化的, 比如 default、primary、warn等, 还是使用样式字符串比较好.

在下面的例子里面, 我使用的是开发版本的 styled-components 包, 而你应该使用速度更快的生产版本. 在 React 项目里面, styled-components 的生产包禁用了很多开发环境下的警告, 这些警告是很重要的, 它使用 [CSSStyleSheet.insertRule()](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet/insertRule) 将生成的样式注入到页面上, 但是开发环境下却用了 [Node.appendChild()](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)(<u>Evan Scott</u> [在这里](https://medium.com/styled-components/v3-1-0-such-perf-wow-many-streams-c45c434dbd03) 展示了 `insertRule` 到底有多快)

同时你也可以考虑使用 [babel-plugin-styled-components](https://www.styled-components.com/docs/tooling#babel-plugin) 插件, 它可以压缩并预处理样式文件.

## 总结

------

styled-components 的工作流程是很简洁的, 它会在组件渲染之前创建必要的 CSS 样式, 并且在需要解析标签字符串和预处理 CSS 的前提下也足够快.

这篇文章的讲解并没有覆盖到 styled-components 的各个方面, 但是我尽量的去专注于主要的点.

在这篇文章里面, 我使用的 styled-components 版本是 [v3.3.3](https://github.com/styled-components/styled-components/tree/v3.3.3). 在后续的版本中它的源码可能会发生变化.