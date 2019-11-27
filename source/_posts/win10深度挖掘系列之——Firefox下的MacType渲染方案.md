---
title: win10深度挖掘系列之——Firefox下的MacType渲染方案
date: 2019-08-03 13:59:57
tags: [windows]
categories: [freebie, tools]
---

不定期会使用`Firefox`作测试, 由于使用`Win`电脑, 火狐的字体渲染简直没法看, 甚至还不如`Chrome`. 虽然现在已经彻底抛弃`Chrome`, 使用`CentBrowser`替代.但是还是写篇博客, 记录下用`MacType`优化火狐的过程.


<!-- more -->


## 更新

------

### [2019-8-3]

- Initial release

## 步骤

------

- 地址栏输入`about:config`进入火狐设置页

![firefox_config_main_interface](https://oos.blog.yyge.top/2019/8/3/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94Firefox%E4%B8%8B%E7%9A%84MacType%E6%B8%B2%E6%9F%93%E6%96%B9%E6%A1%88/images/firefox_config_main_interface.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 搜索`gfx.direct2d.disabled`, 设置为`true`

![firefox_config_gfx_direct2d_disabled](https://oos.blog.yyge.top/2019/8/3/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94Firefox%E4%B8%8B%E7%9A%84MacType%E6%B8%B2%E6%9F%93%E6%96%B9%E6%A1%88/images/firefox_config_gfx_direct2d_disabled.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 搜索`gfx.content.azure.backends`, 修改其值, 参照如下图:

![firefox_config_gfx_content_azure_backends](https://oos.blog.yyge.top/2019/8/3/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94Firefox%E4%B8%8B%E7%9A%84MacType%E6%B8%B2%E6%9F%93%E6%96%B9%E6%A1%88/images/firefox_config_gfx_content_azure_backends.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 保存重启浏览器
- 最终的`MacType`渲染结果, 虽然整体效果不如`CentBrowser`, 但是海星

![firefox_use_mactype](https://oos.blog.yyge.top/2019/8/3/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94Firefox%E4%B8%8B%E7%9A%84MacType%E6%B8%B2%E6%9F%93%E6%96%B9%E6%A1%88/images/firefox_use_mactype.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)