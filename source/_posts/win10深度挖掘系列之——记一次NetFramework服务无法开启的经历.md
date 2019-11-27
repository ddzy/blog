---
title: win10深度挖掘系列之——记一次NetFramework服务无法开启的经历
date: 2019-04-12 16:45:41
tags: [windows]
categories: [freebie, tools]
---

`win10-18362`的某次更新, 导致`.Net Framework`服务全面崩溃, 相关的依赖于该框架的软件都无法正常运行, 最最重要的是`小飞机`也没法用了...


<!-- more -->


## 版本

------

win10版本信息(截图):

![win10版本信息](https://oos.blog.yyge.top/2019/4/12/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E8%AE%B0%E4%B8%80%E6%AC%A1NetFramework%E6%9C%8D%E5%8A%A1%E6%97%A0%E6%B3%95%E5%BC%80%E5%90%AF%E7%9A%84%E7%BB%8F%E5%8E%86/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 问题

------

三月份的某次更新, 导致`.Net`服务无法启动, 去[论坛反馈](https://answers.microsoft.com/zh-hans/windows/forum/windows_10-other_settings/win10-1903/c41ed31d-1bbe-49e4-b8d5-9e5806c8055a?tm=1552620084965)也毫无见效, 最终结合了网上大佬的博客, 找到了解决办法.

## 解决

------

#### 第一步

以`**管理员身份**`运行`cmd`, 输入以下命令:

```bash
dism /online /Get-Features >D:\1.txt
```

会在`D盘`生成一个文件, 点开之后看到下面几个条目, 这就是需要开启的:

![服务列表](https://oos.blog.yyge.top/2019/4/12/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E8%AE%B0%E4%B8%80%E6%AC%A1NetFramework%E6%9C%8D%E5%8A%A1%E6%97%A0%E6%B3%95%E5%BC%80%E5%90%AF%E7%9A%84%E7%BB%8F%E5%8E%86/images/2.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

#### 第二步

在`任意盘符`新建一个`.bat`文件, 我这里的是`work.bat`, 并写入如下内容:

```
start /w pkgmgr /iu:Microsoft-Windows-NetFx3-OC-Package
start /w pkgmgr /iu:Microsoft-Windows-NetFx4-US-OC-Package
start /w pkgmgr /iu:Microsoft-Windows-NetFx3-WCF-OC-Package
start /w pkgmgr /iu:Microsoft-Windows-NetFx4-WCF-US-OC-Package
```

如果不能直接编辑`.bat`文件的话, 可以先编辑`.txt`, 再重命名即可:

![bat脚本](https://oos.blog.yyge.top/2019/4/12/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E8%AE%B0%E4%B8%80%E6%AC%A1NetFramework%E6%9C%8D%E5%8A%A1%E6%97%A0%E6%B3%95%E5%BC%80%E5%90%AF%E7%9A%84%E7%BB%8F%E5%8E%86/images/3.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

#### 第三步

找到刚才的`.bat`文件, 右键以`管理员身份`运行, 然后`等两分钟`即可.

![运行bat脚本](https://oos.blog.yyge.top/2019/4/12/win10%E6%B7%B1%E5%BA%A6%E6%8C%96%E6%8E%98%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E8%AE%B0%E4%B8%80%E6%AC%A1NetFramework%E6%9C%8D%E5%8A%A1%E6%97%A0%E6%B3%95%E5%BC%80%E5%90%AF%E7%9A%84%E7%BB%8F%E5%8E%86/images/4.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

#### 注意

如果上述步骤有任何问题的话, 可以加我QQ(`1766083035`)深入讨论!

## 参考

------

参阅了这位大佬的[文章](https://www.cnblogs.com/buergege520/p/8532723.html)