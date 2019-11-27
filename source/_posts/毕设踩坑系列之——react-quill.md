---
title: 毕设踩坑系列之——react-quill
date: 2019-07-21 11:27:52
tags: [毕设]
categories: [frontend]
---

记录有关`react-quill`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

## 记录

------

### 1. delta转html

1. quill-delta-to-html
2. Quill构造器

<details>
<summary>展开代码</summary>

```ts
const tempCont = document.createElement('div');
(new Quill(tempCont)).setContents(JSON.parse(delta));

return tempCont
  .querySelector('.ql-editor')
  .innerHTML;
```
</details>

### 2. 自定义图片上传处理函数, 解决上传至七牛云的问题

<details>
<summary>展开代码</summary>

```ts
// 初始化react-quill模块
const initModules = () => ({
  ...quillModuleConfig,
  toolbar: {
    ...quillModuleConfig.toolbar,
    handlers: {
      image: handleEditorImageUpload,
    },
  },
});

const handleEditorImageUpload = () => {
  // to do something
};
```
</details>

### 3. 每次上传图片都会显示两张图片

<details>
<summary>展开代码</summary>

```ts
// 获取光标选区
const editorSelRange = editor.getSelection();

editor.deleteText(
  editorSelRange.index + 1,
  1,
);

// 重新调整光标位置
editor.setSelection(
  editorSelRange.index + 1,
  editor.getLenght() - 1,
  'user',
);
```
</details>

### 4. 拓展内置的img组件

内置的`img`组件只支持`src`属性, 自定义拓展`Embed`来增加配置项

<details>
<summary>展开代码</summary>

```ts
import Quill from 'quill';

const InlineEmbed = Quill.import('blots/embed');


export interface IQuillImageBlotProps {
  alt: string;
  src: string;
  'data-src': string;
  class?: string,
};

export default class BaseQuillImageBlot extends InlineEmbed {

  public static blotName: string = 'image';
  public static tagName: string = 'img';
  public static className: string = 'inline-img';

  public static create(value: IQuillImageBlotProps) {
    const node = super.create();

    node.setAttribute('alt', value.alt);
    node.setAttribute('src', value.src);
    node.setAttribute('data-src', value["data-src"]);
    node.setAttribute('class', value.class ? value.class : BaseQuillImageBlot.className);

    return node;
  }

  public static value(node: any) {
    return {
      alt: node.getAttribute('alt'),
      src: node.getAttribute('src'),
      'data-src': node.getAttribute('data-src'),
      class: node.getAttribute('class'),
    };
  }
}
```
</details>

### 5. 七牛云的图片无法正常显示

**原因**: 没有注册自定义的`embed`组件

**解决**:

<details>
<summary>展开代码</summary>

```ts
Quill.register(baseQuillImageBlot);
```
</details>

**注意**: 在`接收到richContent`之后和`编辑页`都要注册