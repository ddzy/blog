---
title: 书写JavaScript代码的15个整洁之道
date: 2020-12-29 09:58:22
tags: [js, JavaScript]
categories: [frontend, translation]
---

原文链接:

https://medium.com/javascript-in-plain-english/15-rules-for-writing-clean-javascript-8e2b2b426515

<!-- more -->

## 更新

------

### [2020-12-29]

- Initial release

## 概述

------

![1.jpeg](https://oos.blog.yyge.top/2020/12/29/%E4%B9%A6%E5%86%99JavaScript%E4%BB%A3%E7%A0%81%E7%9A%8415%E4%B8%AA%E6%95%B4%E6%B4%81%E4%B9%8B%E9%81%93/images/1.jpeg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

原文链接:

 https://medium.com/javascript-in-plain-english/15-rules-for-writing-clean-javascript-8e2b2b426515

无论你是 React 开发者还是 Node.js 开发者. 都能写出可以运行的代码. 但是你写的代码美观并且别人能看懂吗?

下面的规则可以让你的 JavaScript 代码更加整洁和清晰.

## 正文

------

### 规则1. 不要使用随机的字符作为变量

不要使用随机字符去表示一个变量.

```js
// BAD
const x = 4;
```

应该采用语义化的方式去命名变量.

```javascript
// GOOD
const numberOfChildren = 4;
```

### 规则2. 使用驼峰式命名

不要使用蛇形命名(`snake_case`)、帕斯卡命名(`PascalCase`)或者变量名以动词开头.

```js
// Bad: 以大写字母开头
var UserName = "Faisal";

// Bad: 以动词开头
var getUserName = "Faisal";

// Bad: 使用下划线
var user_name = "faisal";
```

使用驼峰命名法代替, 并且使用名词.

```js
// Good
const userName = "Faisal";
```

### 规则3. 使用良好的驼峰来命名函数

函数名不要用名词, 避免与变量名混淆.

```js
// Bad: 以大写字母开头
function DoSomething() {
    // code
}

// Bad: 以名词开头
function car() {
    // code
}

// Bad: 使用下划线
function do_something() {
    // code
}
```

而应该使用驼峰式命名, 并且以动词开头.

```js
//GOOD
function doSomething() {
    // code
}
```

### 规则4. 使用帕斯卡来命名构造函数

```js
// Bad: 以小写字母开头
function myObject() {
    // code
}

// Bad: 使用下划线
function My_Object() {
    // code
}

// Bad: 以动词开头
function getMyObject() {
    // code
}
```

同样的, 构造函数名不应该以动词开头, 并且通过 `new` 来创建对象实例是一个动作.

```js
// GOOD
function MyObject() {
    // code
}
```

### 规则5. 全局常量

全局常量的值不能被改变, 不应该以命名正常变量的方式来命名它.

```js
// BAD
const numberOfChildren = 4;
// BAD
const number_of_children = 4;
```

所有的单词应该大写, 并且以下划线分隔.

```js
// GOOD
const NUMBER_OF_CHILDREN = 4;
```

### 规则6. 分配变量

不要直接将一个 `比较值` 分配给变量.

```js
// BAD
const flag = i < count;
```

应该使用小括号包裹:

```js
// GOOD
const flag = (i < count);
```

### 规则7. 相等运算符的用法

不要使用 "==" 或 "!=" 来对比值. 因为它们不会检查两个值的类型是否相同.

```js
//BAD
if (a == b){
    //code
}
```

而应该使用 "===" 或 "!==", 避免类型错误.

```js
//GOOD
if (a === b){
    //code
}
```

### 规则8. 三元运算符的用法

不要使用三元运算符代替 `if` 条件语句:

```js
//BAD
condition ? doSomething() : doSomethingElse();
```

仅仅在某些条件下使用它来分配值:

```js
// GOOD
const value = condition ? value1 : value2;
```

### 规则9. 简单的声明

不要在一行写多个声明语句, 尽管 JavaScript 是支持这么写的.

```js
// BAD
a =b; count ++;
```

多个声明语句应该分成多行. 并且总是应该在语句的末尾添加分号.

```js
// GOOD
a = b;
count++;
```

### 规则10. if 语句的使用

不要省略 if 语句的大括号, 并且首尾大括号不要写在同一行.

```js
// BAD: 不正确的空格
if(condition){
    doSomething();
}
----
// BAD: 缺失大括号
if (condition)
    doSomething();
----
// BAD: 所有的代码都在一行
if (condition) { doSomething(); }
----
// BAD: 代码挤在一行, 并且没有大括号
if (condition) doSomething();
```

应该总是使用大括号并且进行适当的缩进:

```js
// GOOD
if (condition) {
    doSomething();
}
```

### 规则11. 循环语句的用法

不要在 `for` 循环内部声明变量.

```js
// BAD: 在循环体内声明变量
for (let i=0, len=10; i < len; i++) {
    // code
    let i = 0;
}
```

而应该在 `for` 循环之前声明.

```js
// GOOD
let i = 0;

for (i=0, len=10; i < len; i++) {
    // code
}
```

### 规则12. 缩进长度保持一致

始终使用 2 个或 4 个缩进.

```js
// GOOD
if (condition) {
    doSomething();
}
```

### 规则13. 单行长度

任意行的代码都不要超过 80 个字符. 如果超出的话, 超出的部分应该另起一行.

```js
// BAD: 下一行只缩进 4 个空格
doSomething(argument1, argument2, argument3, argument4,
    argument5);

// BAD: 在运算符前换行
doSomething(argument1, argument2, argument3, argument4
        ,argument5);
```

第二行应该是 **8 个缩进**而不是 4 个, 并且不应该以分隔符开头.

```js
// GOOD
doSomething(argument1, argument2, argument3, argument4,
        argument5);
```

### 规则14. 原始值

字符串不应该使用单引号包裹.

```js
// BAD
const description = 'this is a description';
```

而应该使用双引号.

```js
// GOOD
const description = "this is a description";
```

### 规则15. 使用 "undefined"

不要使用全等运算符判断变量是否为 undefined.

```js
// BAD
if (variable === "undefined") {
    // do something
}
```

使用 typeof 操作符来查看一个变量是否已经定义.

```js
// GOOD
if (typeof variable === "undefined") {
    // do something
}
```

因此, 遵循上述的这些规则, 可以让你的 JavaScript 项目更加整洁.

上述所有的规则都可以在 "**编写可维护的JavaScript**" 这本书中找到. 所以即使你不同意其中的某些规则也没关系. 因为编码风格是多样化的. 但是这些规则可以作为一个良好的起点.

编码愉快! :D
