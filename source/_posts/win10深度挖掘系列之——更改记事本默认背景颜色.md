---
title: win10深度挖掘系列之——更改记事本默认背景颜色
date: 2019-08-02 09:16:37
tags: [windows]
categories: [freebie, tools]
---

`win10`记事本的背景默认为白色, 看久了有些刺眼, 所以萌生出将其更改为护眼的绿色, 将修改的步骤记录于此.


<!-- more -->


## 更新

-----

### [2019-8-2]

- Initial release

## 步骤

------

- <kbd>Win + R</kbd>打开运行窗口, 输入`regedit`

![regedit](https://oos.blog.yyge.top/2019/8/2/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%9B%B4%E6%94%B9%E8%AE%B0%E4%BA%8B%E6%9C%AC%E9%BB%98%E8%AE%A4%E8%83%8C%E6%99%AF%E9%A2%9C%E8%89%B2/images/regedit.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 打开`HKEY_CURRENT_USER/Control Panel/Colors`

![regedit_window_color_path](https://oos.blog.yyge.top/2019/8/2/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%9B%B4%E6%94%B9%E8%AE%B0%E4%BA%8B%E6%9C%AC%E9%BB%98%E8%AE%A4%E8%83%8C%E6%99%AF%E9%A2%9C%E8%89%B2/images/regedit_window_color_path.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 将`Window`的值改为任何想要的颜色

![regedit_window_color_set](https://oos.blog.yyge.top/2019/8/2/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%9B%B4%E6%94%B9%E8%AE%B0%E4%BA%8B%E6%9C%AC%E9%BB%98%E8%AE%A4%E8%83%8C%E6%99%AF%E9%A2%9C%E8%89%B2/images/regedit_window_color_set.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- 重启电脑

## 参考

- [https://zhidao.baidu.com/question/394342823536380125](https://zhidao.baidu.com/question/394342823536380125)