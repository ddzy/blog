---
title: js-xlsx 日期处理的坑点
date: 2020-12-24 15:46:24
tags: [excel, js-xlsx, xlsx, js, date]
categories: [frontend, freebie]
---

在开发后台管理系统的时候, 遇到了需要 `批量导入表格` 的需求, 最终决定由前端来读取 excel 表格, 将数据以 JSON 的格式发送给后端. 但是有一个坑点, 表格里面的一些 Date 类型的数据, 经过 js-xlsx 处理之后会存在毫秒级别的误差, 导致最终展现的日期不精确.