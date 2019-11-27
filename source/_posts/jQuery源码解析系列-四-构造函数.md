---
title: jQuery源码解析系列(四) -构造函数
date: 2019-03-31 10:40:26
tags: [jQuery, ts]
categories: [frontend]
---

接着上一篇说到的`jQuery工厂函数`内部机制, 本篇将会阅读jq真正的构造函数, 也就是`jQuery.fn.init`部分.


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 文章格式优化

## 二、前置知识点

------

#### 2.1 XSS

关于`XSS`的描述, 网上也有很多, 就不在这里总结. 但是有一点还是要着重记录以下, 那就是:

- **PS**: 由`location.hash`引起的`XSS`攻击问题

为什么要说这个? jQuery源码中有这么一段代码和注释:

```js
		// A simple way to check for HTML strings
		// Prioritize #id over <tag> to avoid XSS via location.hash (#9521)
		// Strict HTML recognition (#11290: must start with <)
		// Shortcut simple #id case for speed
		rquickExpr = /^(?:\s*(<[\w\W]+>)[^>]*|#([\w-]+))$/;
```

注意第二句, 假设`$.append()`方法接收一个`用户输入值`, 该用户正好使用location.href作为输入, 而`location.hash`存在恶意代码, 那么就会造成`XSS`. 而`rquickExpr`则是作为严格匹配:

- 诸如`<p>xxx</p>`的html片段
- 形如`#xxx-xxx`之类的IP选择器

结论很简单, 在`CentBrowser`中测试一下即可:

![测试rquickExpr正则](https://oos.blog.yyge.top/2019/3/31/jQuery%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%E7%B3%BB%E5%88%97%28%E5%9B%9B%29%20-%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

#### 2.2 exec和match

源码中用到了`exec()`方法, 由于它和`match`方法比较相似, 好吧, 总是搞混这两个, 这里就说一下它们的最主要的区别:

- `exec`只返回第一个匹配的元素
- `match`返回匹配样本的数量, 与`g`有关

## 三、jQuery构造函数

------

#### 3.1 **思路规划**

有了上述的前置知识加持, 再来阅读源码.

首先, `copy`一下源码中的`构造函数`相关内容:

```js
		init = jQuery.fn.init = function (selector, context, root) {
			var match, elem;

			// HANDLE: $(""), $(null), $(undefined), $(false)
			if (!selector) {
				return this;
			}

			// Method init() accepts an alternate rootjQuery
			// so migrate can support jQuery.sub (gh-2101)
			root = root || rootjQuery;

			// Handle HTML strings
			if (typeof selector === "string") {
				if (selector[0] === "<" &&
					selector[selector.length - 1] === ">" &&
					selector.length >= 3) {

					// Assume that strings that start and end with <> are HTML and skip the regex check
					match = [null, selector, null];

				} else {
					match = rquickExpr.exec(selector);
				}

				// Match html or make sure no context is specified for #id
				if (match && (match[1] || !context)) {

					// HANDLE: $(html) -> $(array)
					if (match[1]) {
						context = context instanceof jQuery ? context[0] : context;

						// Option to run scripts is true for back-compat
						// Intentionally let the error be thrown if parseHTML is not present
						jQuery.merge(this, jQuery.parseHTML(
							match[1],
							context && context.nodeType ? context.ownerDocument || context : document,
							true
						));

						// HANDLE: $(html, props)
						if (rsingleTag.test(match[1]) && jQuery.isPlainObject(context)) {
							for (match in context) {

								// Properties of context are called as methods if possible
								if (jQuery.isFunction(this[match])) {
									this[match](context[match]);

									// ...and otherwise set as attributes
								} else {
									this.attr(match, context[match]);
								}
							}
						}

						return this;

						// HANDLE: $(#id)
					} else {
						elem = document.getElementById(match[2]);

						if (elem) {

							// Inject the element directly into the jQuery object
							this[0] = elem;
							this.length = 1;
						}
						return this;
					}

					// HANDLE: $(expr, $(...))
				} else if (!context || context.jquery) {
					return (context || root).find(selector);

					// HANDLE: $(expr, context)
					// (which is just equivalent to: $(context).find(expr)
				} else {
					return this.constructor(context).find(selector);
				}

				// HANDLE: $(DOMElement)
			} else if (selector.nodeType) {
				this[0] = selector;
				this.length = 1;
				return this;

				// HANDLE: $(function)
				// Shortcut for document ready
			} else if (jQuery.isFunction(selector)) {
				return root.ready !== undefined ?
					root.ready(selector) :

					// Execute immediately if ready is not present
					selector(jQuery);
			}

			return jQuery.makeArray(selector, this);
		};
```

可以看到, 虽然简简单单的百十来行代码, 其内部逻辑还是挺复杂的. 但是并不慌, 我们先从`参数`分析:

- selector
  - 具体的选择器, 可以是`DOM`、`CSS选择器`、`jQuery对象`等, 作为重点, 后续会具体分析
- context
  - `DOM元素`或者`jQuery对象`
  - 好像很少用? 查了下资料, 它的作用是: **限制(减少)搜索的范围**. 假设要获取一个`.text`元素, 此时按照`context`的提供与否, 会出现两种不同的内部机制:
    - (*已提供*): 会在`context`下查找`.text`元素
    - (*未提供*): 默认会在`document`下查找
  - 这对于性能来说是至关重要的——由于CSS解析`从右至左`进行, 浏览器看到`.text`会依次去判断其父级是不是`context`, 所以添加`context`可减少判断层级.
- root
  - jQueyr根节点, 默认为`document`

经过对`参数`的简单分析, 接着来到构造函数内部, 大致浏览一下, 会发现大部分是根据`selector`参数做不同的逻辑处理, 这也是`jQuery`的核心部分.

好吧, 一眼看上去的确很乱, 但是其内部逻辑的目的是差不多的:

- 根据`selector`的不同, 作不同的处理

所以, 我将其分为以下几个区块, 来逐个分析:

- 无效参数值
- 字符串
- DOM节点
- 函数
- 其他
  - 类数组
  - 对象
  - 数组

#### 3.2 **逐个击破**

##### 3.2.1 **无效参数值**

先来看对`无效参数值`的处理逻辑:

```js
	if (!selector) {
		return this;
	}
```

如果传入了一个无效的、没有意义(null, undefined, false, 0, '')的值, 直接返回空的`jQuery.fn.init`实例, 不作任何操作.

##### 3.2.2 **字符串**

1. 处理步骤
   - 计算`match`
     - `match`是之前提到的`rquickExpr`正则匹配到的结果, 这里要注意, 该正则执行后的[分组匹配](https://segmentfault.com/a/1190000004429477)结果分别作为数组的第二和第三项
       - `match[1]` -> 标签字串(如: `<kbd>xxx</kbd>`)
       - `match[2]` -> id选择器(如: `#xxx`)
   - 根据`match`的结果来决定后续操作:
     - 如果`match`的值存在且为(`html字符串`或`id`选择器), 会出现以下几种情况:
     	- match\[1] -> $(array)
         - 如果只匹配到单个`html`字符串, 调用`**jQuery.merge**`和`**jQuery.parseHTML**`方法, 将该字符串转化为jq对象
       - match\[1] && context -> $(array)
         - 如果同时匹配到`html`字符串, 且用户提供了`context`上下文, 会转化为jquery对象, 并作为`fragment`挂载到`context`下
       - match\[2] -> $(array)
         - 如果匹配到形如`#xxx`之类的选择器, 调用`getElementById`, 并转化为jquery对象
       - match\[2] && context -> $(array) -->
   			- 如果匹配到诸如`#xxx`, 并且用户提供了`context`, 则继续判断`context`是否为原生DOM节点或者jquery对象, 进一步执行选取操作
     - 反之则表明是一个正常的`CSS选择器`, 继续判断`context`是否存在
       - 存在则通过调用`this.constructor(context)`, 将其转化为jquery对象, 使用原型上的`find`方法来执行查询
       - 不存在则根据`rootJquery`来找寻

2. jQuery.parseHTML&jQuery.merge

------

分割线 - \[2019-4-10]更新

------

上述简单的列出了一部分逻辑, 并没有陈述过于详细, 毕竟作者的思路太严谨了.

但是你以为就这么简单?(好吧, 其实是我自己认为.), 将目光转向`jQuery.merge`和`jQuery.parseHTML`这两个方法:

> **PS**:  偷了几天懒, 翘课回家好好睡了几天觉, 周一继续更新...

这是两个挂载到jQuery上的`静态方法`, 何所谓`静态方法`, 它和`实例方法`的区别是什么? 对于此问题, 我觉得`简书`上的一篇文章写的通俗易懂:

> [简书——实例方法和静态方法有什么不一样?](https://www.jianshu.com/p/ea7c44f31cdf)

简单来说:

- 静态方法(`static-method`挂载于类本身), 常用于
  - 工具函数的定义
  - 复用性强的函数
- 实例方法(`ins-method`)则反之

3. jQuery.parseHTML

------

分割线 - \[2019-4-11]更新

------

吃个饭回来继续更新...

> **PS**:  源码太长, 不全部`Ctrl+CV`了, 大概在`9771` - `9815`行

```js
		// Argument "data" should be string of html
		// context (optional): If specified, the fragment will be created in this context,
		// defaults to document
		// keepScripts (optional): If true, will include scripts passed in the html string
		jQuery.parseHTML = function(data, context, keepScripts) {
			// 很多处理
		}
```

简单看一下函数上面的注释,

> **PS**:  源码上方的每一行注释都很重要, 通过它, 可以了解该方法的具体意图

其接收三个参数:

- data
  - (必选)-必须为`html`字符串
- context
  - (可选)-以`Fragment`的形式, 挂载解析后的结果
- keepScripts
  - (可选)-是否过滤`js`

不难看出, `parseHTML`这个方法, 顾名思义, 它的作用就是:

- 解析`html`字符串
- 挂载到对应`context`

俗话说——`擒贼先擒王`, 代码也是如此, 知道了函数的作用, 那么其内部运作机理也就理所当然的显现了, 顶多就是做一些`逻辑判断`, 那么接下来就对其内部逻辑做个简单梳理.

对于`parseHTML`内部, 大致进行了以下处理流程:

- 非法参数过滤
  - 对于无意义的参数直接返回`空数组`
- 理解三个变量
  - base
    - 给所有解析出来的元素指定`默认链接`, 诸如`a`、`img`此类
  - parsed
    - `数组`
    - 保存解析到的符合`H5标准令牌规范`的`标签字符串(尖括号字符串)`
  - scripts
    - 数组
    - 根据参数`keepScripts`, 来决定是否解析`script`标签
- 计算`context`
  - 如果没有提供`context`, 则会创建新的`document`来作为`context`
  - 看到源码中有这么一段注释
  ```js
		// Support: Safari 8 only
		// In Safari 8 documents created via document.implementation.createHTMLDocument
		// collapse sibling forms: the second one becomes a child of the first one.
		// Because of that, this security measure has to be disabled in Safari 8.
	```
	- 在`Safari8`中, 使用`document.implementation.createHTMLDocument`创建`document`会出现`折叠孩子节点`的情况, 也就是说, 我们创建了两个相同的兄弟节点, 第一个节点会被忽略, 这是开发中不能接受的
	- 所以jQuery源码中定义了`support`对象, 来作为`检测兼容性的工具`, 在这里使用其中的`createHTMLDocument`方法来做简单判断, 如果是非`Safari8`浏览器, 创建全新的`document`, 反之直接赋值给`当前document`.
  ```js
		if (support.createHTMLDocument) {
			context = document.implementation.createHTMLDocument("");

			// Set the base href for the created document
			// so any parsed elements with URLs
			// are based on the document's URL (gh-2965)
			base = context.createElement("base");
			base.href = document.location.href;
			context.head.appendChild(base);
		} else {
			context = document;
		}
	```
- 计算`parsed`
  - 这里用到了`rSingleTag`这个正则
  ```js
		var rsingleTag = (/^<([a-z][^\/\0>:\x20\t\r\n\f]*)[\x20\t\r\n\f]*\/?>(?:<\/\1>|)$/i);
	```
  - 它是为了匹配一个相对`纯净`的标签(`$('<span />')`、`$('<span></span>')`), 何为`相对纯净`?
    - 子节点为空
    - 不具有任何属性
  - 如果`rSingleTag`捕获到标签
    - 直接在当前`context`作用域下创建新的`HTMLElement`
  - 反之, 表明有多层嵌套的标签, 通过调用`buildFragment`方法来统一处理
    - 那么问题来了, `buildFragment`又是什么鬼? 不急, VS Code按住<kbd>Ctrl + MouseLeft</kbd>, 去它内部看一看:
      - 我将其内部源码结构简化了一下, 大概是这样的:
			```js
			function buildFragment(elems, context, scripts, ...args[]) {
				// 保存源html字符串
				// 也就是遍历elems得到的每一项
				var elem = '';
				// 源码中创建了div, 将其innerHTML的值设为elem
				// tmp保存该div的每个后代元素
				var tmp = '';
				// 保存解析elem得到的所有标签
				// 为了得到`script`标签
				var nodes = [];


				// 迭代操作, 取代传统递归方式
				// 遍历所有节点, 存储到`nodes`数组
				for(var i = 0; i < elems.length; i++) {
					...
					var elem = elems[i];

					tmp = context.createElement('div');
					tmp.innerHTML = elem;

					while(j--) {
						tmp = tmp.lastChild;
					}

					tmp = fragment.firstChild;
				}


				// 计算`scripts`
				// 将取得的`script`标签存放入`scripts`数组
				while((elem = nodes[i++])) {
					tmp = getAll(fragment.appendChild(elem), 'scripts');

					if(scripts) {
						while((elem = tmp[j++])) {
							scripts.push(elem);
						}
					}
				}
			}
			```
- 计算`scripts`
  - 实际的计算操作位于`buildFragment`内部
- 返回`jQuery.merge()`
  - `merge`方法会在下面说到

以上就是`jQuery.parseHTML`内部的复杂逻辑, 总结来说, 就是以下两方面:

- 对于`纯净的`html标签字符串, 直接返回该标签数组
- 对于`多层嵌套的`html标签字符串, 迭代其后代节点, 并保存`script`标签.

4. jQuery.merge

------

分割线 - \[2019-4-12]更新

------

> 之前的解析过程有些太啰嗦了, 对一个函数的内部剖析, 觉得没有必要, 看源码只是了解它的大致结构即可, 所以后面开始会对函数内部的处理逻辑加以简化, 对不常用的`API`不作解析, 避免浪费不必要的时间.


好了, 接着上一小节的`jQuery.parseHTML`的函数内部剖析, 了解到了:

- 其内部通过对html令牌类型的`标签字符串`做处理, 返回`jQuery.merge`处理过的`节点数组`

这里问题就来了:

> jQuery.merge是什么鬼?

好了, 定位到`merge`方法, 发现它是挂载于`jQuery类`上的方法, `jQuery.parseHTML`、`buildFragment`等多处都调用了它, 这表明它是一个单纯的`工具方法`或者`多复用函数`

> **PS**: 诸如类似的方法还有很多, 比如`jQuery.ajax`等等, 在后面的实例`get`、`post`方法都会使用到.

🆗, 看到其内部源码:

```js
	// Support: Android <=4.0 only, PhantomJS 1 only
	// push.apply(_, arraylike) throws on ancient WebKit
	merge: function (first, second) {
		var len = +second.length,
		j = 0,
		i = first.length;

		for (; j < len; j++) {
			first[i++] = second[j];
		}

		first.length = i;

		return first;
	},
```

函数内部的逻辑也很简单:

- 将第二个数组的值传递到第一个数组

这里就不多说了, 那么, 可以总结出这么一个结论, 该函数的作用就是:

- 合并第二个`类数组的内容`到第一项

`第一项`可以是:

- 一个数组
- jQuery对象
  - `jQuery.merge`内部会新增一个`length`属性,



##### 3.2.3 **DOM节点处理**

```js
...
else if(selector.nodeType) {
	this[0] = selector;
	this.length = 1;
	return this;
}
```

对于传递的`DOM节点`选择器, 直接包装成`jQuery对象`并返回.

##### 3.2.4 **函数处理**

在分析jQuery内部对于选择器为`函数`时的处理逻辑之前, 先来回顾一下它的用法:

```js
$(document).ready(function() {
	...
	...
})
```

或者直接使用`简写`:

```js
$(function() {
	...
	...
})
```

那么问题来了:

> **Q**: 上述的两种方法有啥区别呢?

带着问题, 来看源码:

```js
else if(jQuery.isFunction(selector)) {
	return root.ready !=== undefined
		? root.ready(selector)
		: selector(jQuery);
}
```

可以看到, jQuery对传入的`function`调用了`rootjQuery`原型上的`ready()`方法, 该`rootjQuery`也就是`$(document)`, 看到这里, 相信已经豁然开朗, 其实就是简化了代码量, 但是其本质都是一样的.



##### 3.2.5 其它类型

```js
...
...
return jQuery.makeArray(selector, this);
```

对于:

- 不为`0`的数字
- 数组
- 类数组
  - HTMLCollection
  - NodeList

等等这些类型的数据, 会调用其静态方法——`jQuery.makeArray`来包装为`jQuery`对象.

既然用到了`makeArray`这个方法, 那么我们来简单分析一下它:

```js
		// results is for internal usage only
		makeArray: function (arr, results) {
			var ret = results || [];

			if (arr != null) {
				if (isArrayLike(Object(arr))) {
					jQuery.merge(ret,
						typeof arr === "string" ?
							[arr] : arr
					);
				} else {
					push.call(ret, arr);
				}
			}

			return ret;
		},
```

它根据参数`arr`的类型来做了不同的处理:

- `arr`为数组or类数组
  - 调用`jQuery.merge`合并二者
- `arr`为其它
  - 直接合并

最终返回处理过的`ret`, 也就是`result`结果, 此时, `arr`的每一项, 已经被转化为`results`的各项, 当然, 由于`该函数`外部已经注明了:

> **注意**: results is for internal usage only(该函数的结果仅供内部使用)

那么, 此时的`results`理所当然就是包装过的`jQuery`的实例(`jQuery.fn.init`)了.

## 四、总结

------

写到这里, jQuery构造函数的内部处理逻辑大概就分析完了, 由于太多太杂, 所以在最后, 画了一张脑图来串联一下这些知识点:

![总结](https://oos.blog.yyge.top/2019/3/31/jQuery%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%E7%B3%BB%E5%88%97%28%E5%9B%9B%29%20-%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0/images/2.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 五、示例代码

------

代码已用`ts`重构, 放置于[Gayhub](https://github.com/ddzy/my-simple-jquery/tree/branch/dev)