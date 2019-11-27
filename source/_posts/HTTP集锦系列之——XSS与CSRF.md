---
title: HTTP集锦系列之——XSS与CSRF
date: 2019-03-09 17:18:58
tags: [http, xss, csrf]
categories: [frontend, backend]
---

前端可能遇到的两个攻击, 很久之前有和别人讨论过这个问题, 但是时间太久, 又没有适当的记录, 还是淡化了.


<!-- more -->


## 更新

------

### [2019-3-12]

#### Added

- 完善`CSRF`特性内容

## 记录

------

### CSRF

> 跨站请求伪造

#### 特性

- 登录网站A并生成`cookie`
- 在不登出该网站的情况下, 访问危险网站B
- 危险站点B携带用户cookie发送请求至网站A, 执行恶意操作

#### 预防

- Token验证

### XSS

> 跨站脚本

#### 特性

不对`server`端造成任何伤害. 通过站内交互途径, 比如发布评论, 提交含有js内容的文本. 如果`server`端没有过滤掉这些文本, 作为内容发布到了页面上, 其他用户访问这个页面的时候就会运行这些脚本

#### 预防

- 输入框转义过滤

## 参考

------

- [总结XSS和CSRF两种跨站攻击](https://www.cnblogs.com/wangyuyu/p/3388180.html)
- [CSRF和XSS区别](https://baijiahao.baidu.com/s?id=1615650809297607360&wfr=spider&for=pc)