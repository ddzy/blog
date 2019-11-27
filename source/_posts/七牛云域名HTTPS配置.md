---
title: 七牛云域名HTTPS配置
date: 2019-01-30 10:11:27
tags: 七牛云
categories: freebie
---

继上一次配置完七牛云的`自定义域名`服务后, 本来平安无事的, 谁知今天突然抽风, `CentBrowser`提示`Mixed Content: This content should also be served over HTTPS`...


<!-- more -->


## **引申**

------

### 问题来源

> `Chrome`默认不允许带有`HTTPS`协议的域名访问`HTTP`的域名下的内容

### 解决办法

> 需要在七牛云配置域名的`HTTPS`服务

## **配置**

------

老样子, 进入自己的`bucket`控制台:

![进入bucket控制台](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

右下角`融合CDN加速域名`区域中点击自己为此`bucket`绑定的域名, 也就是要配置`HTTPS`的域名:

![融合CDN加速域名区域](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

进入到`域名管理`页, 可以看到, 里面有我们的域名信息:

![域名管理页](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

下拉, 找到`HTTPS配置`区域框, 点击`修改配置`按钮:

![HTTPS配置区域](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/4.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

在右边弹出的`drawer`表单中填写相应的信息:

![填写相应信息](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/5.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

点击确认之后, 可以看到顶部`Tip`信息:

![tip成功提示](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/6.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

最后找到`域名管理`页, 在里面可以看到自己的域名信息:

![域名管理页](https://oos.blog.yyge.top/2019/1/30/%E4%B8%83%E7%89%9B%E4%BA%91%E5%9F%9F%E5%90%8DHTTPS%E9%85%8D%E7%BD%AE/images/7.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

此时的状态为处理中, 大概要等一两天左右, 耐心等待即可...

## **总结**

------

浏览器的安全策略, 总而言之有利也有弊, 像`Chrome`这种, 直接把你的`HTTP`请求`block`掉, 对于企业来说, 是个提醒, 提醒要更换相对安全的`HTTPS`服务, 但是对于个人来说, 则显得有些多余了...