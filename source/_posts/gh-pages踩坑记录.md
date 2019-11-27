---
title: gh-pages踩坑记录
date: 2019-03-01 15:25:51
tags: github
categories: freebie
---

对配置`gh-pages`的配置做个记录, 便作后续参考


<!-- more -->


## 前言

------

去公司面试, 比较突然. 想搞个在线简历和项目在线演示的平台. 由于之前写`blog`直接使用`github-page`作了部署. 理所当然地想到了用`gh-pages`分支来展示简历和项目之类的.

## 配置

------

### 第一步: 创建repo

github新建一个repository,

> 该repo模拟一个在线简历or项目

这边已经建好了, 如下图:

![新建repo](https://oos.blog.yyge.top/2019/3/1/gh-pages%E8%B8%A9%E5%9D%91%E8%AE%B0%E5%BD%95/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 第二步: 命令搭建

1. clone项目

```bash
git clone https://github.com/ddzy/resume.git
```

2. 创建`gh-pages`分支

```bash
git checkout --orphan gh-pages
```

3. 添加至暂存区

```bash
git add .

git commit -m ":fire: add repo to site"
```

4. 推送至`gh-pages`分支

```bash
git push origin gh-pages
```

### 第三步: 测试

待上述`2`步骤推送成功之后, 进入repo的`settings`页:

![settings测试](https://oos.blog.yyge.top/2019/3/1/gh-pages%E8%B8%A9%E5%9D%91%E8%AE%B0%E5%BD%95/images/2.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

选中`Source`选项的`gh-pages branch`条目. 可以看到, 上方已经可以正常显示之前配置好的域名信息

![测试成功](https://oos.blog.yyge.top/2019/3/1/gh-pages%E8%B8%A9%E5%9D%91%E8%AE%B0%E5%BD%95/images/3.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 总结

------

由于之前没有考虑很多, 只是单纯地将`blog`部署至`github-page`, 今天配置起来还是比较麻烦.