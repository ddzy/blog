---
title: js-xlsx 日期处理的坑点
date: 2020-12-24 15:46:24
tags: [excel, js-xlsx, xlsx, js, date]
categories: [frontend, freebie]
---

在开发后台管理系统的时候, 遇到了需要 `批量导入表格` 的需求, 最终决定由前端来读取 Excel 表格, 将数据以 JSON 的格式发送给后端. 但是有一个坑点, 表格里面的一些 Date 类型的数据, 经过 js-xlsx 处理之后会存在毫秒级别的误差, 导致最终展现的日期不精确.

<!-- more -->

## 更新

---

### [2020-12-24]

- Initial release

## 依赖版本

---

| Name | Description     | Version | Link                               |
| ---- | --------------- | ------- | ---------------------------------- |
| xlsx | 解析 Excel 表格 | 0.16.9  | https://github.com/SheetJS/sheetjs |

## 问题复现

---

js-xlsx 已经提供了 `cellDates` 可配置项, 用来处理日期:

```js
const workbook = XLSX.read(result, {
  type: "binary",
  cellDates: true, // 将 Excel 日期转化为 js 的 Date 实例
});
```

但是, 使用这种方式会出现 `43` 秒的误差, 如下图所示:

![4.png](https://oos.blog.yyge.top/2020/12/24/js-xlsx%E6%97%A5%E6%9C%9F%E5%A4%84%E7%90%86%E7%9A%84%E5%9D%91%E7%82%B9/images/4.png)

由于 js-xlsx 内部会对 Excel 的日期数值进行 `四舍五入` 并且 `保留三位小数` 的操作, 所以会出现误差.

## 解决方案

---

由于 js-xlsx 读取到的 Excel 中的日期格式可能有两种:

- string: 也就是说 Excel 会把时间戳以文本形式原封不动的传递(`2021-12-20 16:00:00`)
- number: Excel 内部会将诸如 `2021-12-20 16:00:00` 的日期转化为诸如 `44228.3756944444` 的数值, 这个数值表示**从 `1900` 年至今的总天数**, 如下图所示:

![5.png](https://oos.blog.yyge.top/2020/12/24/js-xlsx%E6%97%A5%E6%9C%9F%E5%A4%84%E7%90%86%E7%9A%84%E5%9D%91%E7%82%B9/images/5.png)

所以就需要分别对以上两种格式做处理, 首先要做的是关闭 `cellDates` 配置项:

```diff
const workbook = XLSX.read(result, {
  type: "binary",
-  cellDates: true, // 将 Excel 日期转化为 js 的 Date 实例
+  cellDates: false,
});
```

接着格式化 "开服时间" 条目:

```js
typeof item["开服时间"] === "string"
  ? ~~(new Date(item["开服时间"]).getTime() / 1000)
  : ~~(this._format(item["开服时间"]).getTime() / 1000);

// 自定义格式化日期
_format(ExcelDate: number) {
  // 25569 => 自 1900 年到 1970 年的天数
  let utc_days = Math.floor(excelDate - (25567 + 2))
  // 86400 => 24 * 60 * 60 => 一天的总秒数
  let utc_value = utc_days * 86400
  // 一天的总毫秒数
  let date_info = new Date(utc_value * 1000)

  // 误差处理
  let fractional_day = excelDate - Math.floor(excelDate) + 0.0000001
  // 自 1970 年至今的总秒数
  let total_seconds = Math.floor(86400 * fractional_day)

  let seconds = total_seconds % 60

  total_seconds -= seconds

  let hours = Math.floor(total_seconds / (60 * 60))
  let minutes = Math.floor(total_seconds / 60) % 60

  return new Date(
    date_info.getFullYear(),
    date_info.getMonth(),
    date_info.getDate(),
    hours,
    minutes,
    seconds
  )
}
```

## 参考

---

- [Excel 自动转换日期的坑](https://www.zhihu.com/question/31583664)
- [Converting Excel Date Serial Number to Date using Javascript](https://stackoverflow.com/questions/16229494/converting-excel-date-serial-number-to-date-using-javascript)
- [UTC 时间和 GMT 时间的区别](https://www.zhihu.com/question/27052407)
