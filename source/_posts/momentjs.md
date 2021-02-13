---
title: momentjs 使用笔记
date: 2020.02.29
categories:
  - 开发
tags:
  - momentjs
  - 时区
  - dayjs
---

# momentjs 使用笔记

## 解析

解析字符串`moment(string)`，默认使用[ISO 8601](https://zh.wikipedia.org/wiki/ISO_8601)格式解析

如果不能识别，降级为`new Date(string)`识别，

<!-- more -->

### 注意时区

**注意**： js new Date()里里默认使用`GMT`作为标准时间，如果传入其它标准时间关键字，会识别不准确

例如`CST`，在中国就会**多出14个小时**，

```js
new Date('Thu Oct 24 10:45:31 CST 2019') // Fri Oct 25 2019 00:45:31 GMT+0800 (中国标准时间)
```

因为带有CST关键字后，会默认北美时区(-6)，显示再转换为本地的时区(中国+8)，就会多出14个小时

>CST
>
> 美国中部时间：Central Standard Time (USA) UT-6:00
>
> 澳大利亚中部时间：Central Standard Time (Australia) UT+9:30
>
> 中国标准时间：China Standard Time UT+8:00
>
> 古巴标准时间：Cuba Standard Time UT-4:00

解决办法，可以加14个小时，或者删除关键字

```js
new Date('Thu Oct 24 10:45:31 CST 2019'.replace('CST', '')) // Thu Oct 24 2019 10:45:31 GMT+0800 (中国标准时间)
```

更具浏览器兼容性不同，选择合适的方法

### 当前时间

如果moment没有参数时`moment()`，会返回当前时间的实例

基本等同于`moment(new Date())`和`moment(undefined)`，但`moment(null)`无法解析

### 已知时间格式

如果已知了new Date()无法识别的时间格式，可以传入moment第二个参数，来正确的解析时间参数

```js
moment('2012-12-13 14:15:16', 'YYYY-MM-DD HH:mm:ss')
```

这里关键字含义如下，区分大小写

输入 | 示例 | 	描述 
---- | ----  | ---- 
YYYY | 2014 |	4 或 2 位数字的年份
YY | 14 |	2 位数字的年份
M | 1 - 12 |	月份
MM | 01 - 12 |	月份,两位数
D | 1-31 |	一个月中的某天
DD | 01-31 | 一个的某天，两位数
H | 0-23 | 小时
HH | 00-23 | 小时，两位数
h | 1-12 | 小时，12小时制
hh | 01-12 | 小时，12小时制，2位数字
m | 0-59 | 分钟
mm | 00-59 | 分钟，两位数
s | 0-59 | 秒
ss | 00-59 | 秒，两位数
S | 0-9 | 百毫秒，1位数
SS | 00-99 | 十毫秒，两位数
SSS | 000-999 | 毫秒，三位数
Z | -05：00 | UTC 偏移量
ZZ | -0500 | UTC 偏移量，两位
A | 上午下午 | 后或事前子句，大写
a | 上午下午 | 后或事前子句，小写

## 显示

### format

根据参数显示格式化后的**字符串**，参数产考上面的表格

不传参时，显示为`ISO_8601`格式，无毫秒

```js
moment('2012-12-13').format('YYYY-MM-DD HH:mm:ss') // 2012-12-13 00:00:00
moment('2012-12-13').format() // 2012-12-13T00:00:00+08:00"
```

### diff

对比差值

```js
moment('2012-12-13').diff(moment('2012-12-12')) // -86400000
moment('2012-12-11').diff(moment('2012-12-12')) // 86400000
moment('2012-11-11').diff(moment('2010-12-12'), 'Y') // 1
moment('2012-11-11').diff(moment('2010-12-12'), 'Y', true) // 1.913978494623656
```

第二个参数如下

键 | 简写
---- | ----
years | y
months | M
weeks | w
days | d
hours | h
minutes | m
seconds | s
milliseconds | ms

### valueOf

显示毫秒**数值**

```js
moment('2012-12-13').valueOf() //1355328000000
```

### unix

显示秒**数值**

```js
moment('2012-12-13').unix() // 1355328000
```

### daysInMonth

当月多少天

```js
moment('2012-02').daysInMonth() // 29
moment('2011-02').daysInMonth() // 28
```

## 设置

```js
moment('2012-12-12').format('YYYY-MM-DD HH:mm:ss') // "2012-12-12 00:00:00"
moment('2012-12-12').second(2).format('YYYY-MM-DD HH:mm:ss') // 秒 "2012-12-12 00:00:02"
moment('2012-12-12').minute(2).format('YYYY-MM-DD HH:mm:ss') // 分钟 "2012-12-12 00:02:00"
moment('2012-12-12').hour(2).format('YYYY-MM-DD HH:mm:ss') // 小时 "2012-12-12 02:00:00"
moment('2012-12-12').date(2).format('YYYY-MM-DD HH:mm:ss') // 当前月中的天 "2012-12-02 00:00:00"
moment('2012-12-12').day(2).format('YYYY-MM-DD HH:mm:ss') // 当前星期中的天 "2012-12-11 00:00:00"
moment('2012-12-12').weekday(2).format('YYYY-MM-DD HH:mm:ss') // 当前星期的星期几 "2012-12-11 00:00:00"
moment('2012-12-12').dayOfYear(2).format('YYYY-MM-DD HH:mm:ss') // 当前年的第几天 "2012-01-02 00:00:00"
moment('2012-12-12').week(2).format('YYYY-MM-DD HH:mm:ss') // 当前年的第几个星期 "2012-01-11 00:00:00"
moment('2012-12-12').month(2).format('YYYY-MM-DD HH:mm:ss') // 当前年的第几月，0是第一个月，"2012-03-12 00:00:00"
moment('2012-12-12').quarter(2).format('YYYY-MM-DD HH:mm:ss') // 当前年的第几季度 "2012-06-12 00:00:00"
moment('2012-12-12').year(2).format('YYYY-MM-DD HH:mm:ss') // 年 "0002-12-12 00:00:00"
```

还可以
```js
moment('2012-12-12').set('date', 2)
moment('2012-12-12').set('hour', 2).format('YYYY-MM-DD HH:mm:ss')
```

## 获取

```js
moment('2012-12-12').get('year');
moment('2012-12-12').get('month');  // 0 至 11
moment('2012-12-12').get('date');
moment('2012-12-12').get('hour');
moment('2012-12-12').get('minute');
moment('2012-12-12').get('second');
moment('2012-12-12').get('millisecond');
```

## 操作

```js
moment('2012-12-12').add(7, 'days').subtract(1, 'months').format('YYYY-MM-DD HH:mm:ss') // "2012-11-19 00:00:00"
```

moment对象是可变的，操作后原本对象内的值就会变化，例如我们要显示`前后3天的值`

```js
const cur = moment('2012-12-12')

// 错误
cur.subtract(3, 'days').format('YYYY-MM-DD HH:mm:ss') // "2012-12-09 00:00:00"
cur.add(3, 'days').format('YYYY-MM-DD HH:mm:ss') // "2012-12-12 00:00:00"

// 正确，需要克隆出来再使用
moment(cur).subtract(3, 'days').format('YYYY-MM-DD HH:mm:ss') // "2012-12-09 00:00:00"
moment(cur).add(3, 'days').format('YYYY-MM-DD HH:mm:ss') // "2012-12-15 00:00:00"
```

### 开始结束时间

```js
// 开始
moment().startOf('year');    // 设置为今年一月1日上午 12:00
moment().startOf('month');   // 设置为本月1日上午 12:00
moment().startOf('quarter');  // 设置为当前季度的开始，即每月的第一天上午 12:00
moment().startOf('week');    // 设置为本周的第一天上午 12:00
moment().startOf('isoWeek'); // 根据 ISO 8601 设置为本周的第一天上午 12:00
moment().startOf('day');     // 设置为今天上午 12:00
moment().startOf('date');     // 设置为今天上午 12:00
moment().startOf('hour');    // 设置为当前时间，但是 0 分钟、0 秒钟、0 毫秒
moment().startOf('minute');  // 设置为当前时间，但是 0 秒钟、0 毫秒
moment().startOf('second');  // 与 moment().milliseconds(0); 相同

// 结束
moment().endOf("year"); // 将 moment 设置为今年的 12 月 31 日 23:59:59.999
```

**注意**：开始时间和结束，小时以上，均为`12:00`，结束时间均为`23:59:59:999`

## 查询

```js
// 小于
moment('2010-10-20').isBefore('2010-10-21'); // true

// 相等
moment('2010-10-20').isSame('2010-10-20'); // true

// 大于
moment('2010-10-20').isAfter('2010-10-19'); // true

// 范围内
moment('2010-10-20').isBetween('2010-10-19', '2010-10-25'); // true

// 是否是momentjs对象
moment.isMoment() // false
moment.isMoment(new Date()) // false
moment.isMoment(moment()) // true

// 是否是new Date() 原生对象
moment.isDate(); // false
moment.isDate(new Date()); // true
moment.isDate(moment()); // false
```

可用[dayjs](https://github.com/iamkun/dayjs)替代

- 🕒 和 Moment.js 相同的 API 和用法
- 💪 不可变数据 (Immutable)
- 🔥 支持链式操作 (Chainable)
- 🌐 国际化 I18n
- 📦 仅 2kb 大小的微型库
- 👫 全浏览器兼容