---
title: Python | time模块
tags: [Python, 模块]
categories: Python
abbrlink: PY_Time
date: 2019-07-13 15:01:30
---

和时间有关系时，我们就要用到时间模块。

## 表现形式

在 Python 中，通常有这三种方式来表示时间：时间戳、元组（struct_time）、格式化的时间字符串：

<!-- more -->

1. 时间戳（timestamp）：通常来说，时间戳表示的是从 1970 年 1 月 1 日 00:00:00 开始按秒计算的偏移量。我们运行`type(time.time())`，返回的是 float 类型。

2. 格式化的时间字符串(Format String)： ‘2019-07-13’

```
%y 两位数的年份表示（00-99）
%Y 四位数的年份表示（000-9999）
%m 月份（01-12）
%d 月内中的一天（0-31）
%H 24小时制小时数（0-23）
%I 12小时制小时数（01-12）
%M 分钟数（00=59）
%S 秒（00-59）
%a 本地简化星期名称
%A 本地完整星期名称
%b 本地简化的月份名称
%B 本地完整的月份名称
%c 本地相应的日期表示和时间表示
%j 年内的一天（001-366）
%p 本地A.M.或P.M.的等价符
%U 一年中的星期数（00-53）星期天为星期的开始
%w 星期（0-6），星期天为星期的开始
%W 一年中的星期数（00-53）星期一为星期的开始
%x 本地相应的日期表示
%X 本地相应的时间表示
%Z 当前时区的名称
%% %号本身
```

3. 元组（struct_time）：struct_time 元组共有 9 个元素共九个元素:(年，月，日，时，分，秒，一年中第几周，一年中第几天等）

   | 索引（Index） | 属性（Attribute）         | 值（Values）        |
   | ------------- | ------------------------- | ------------------- |
   | 0             | tm_year（年）             | 比如 2011           |
   | 1             | tm_mon（月）              | 1 - 12              |
   | 2             | tm_mday（日）             | 1 - 31              |
   | 3             | tm_hour（时）             | 0 - 23              |
   | 4             | tm_min（分）              | 0 - 59              |
   | 5             | tm_sec（秒）              | 0 - 60              |
   | 6             | tm_wday（weekday）        | 0 - 6（0 表示周一） |
   | 7             | tm_yday（一年中的第几天） | 1 - 366             |
   | 8             | tm_isdst（是否是夏令时）  | 默认为 0            |

示例：

```python
import time
# 时间戳
print(time.time())
## 输出结果
## 1562998231.9308622

# 时间字符串
print(time.strftime("%Y-%m-%d %X"))
print(time.strftime("%d/%m/%y %H-%M-%S"))
## 输出结果
## 2019-07-13 14:10:31
## 13/07/19 14-10-31

# 时间元组：localtime将一个时间戳转换为当前时区的struct_time
print(time.localtime(3000000000))
print(time.gmtime(3000000000))
## 输出结果
## time.struct_time(tm_year=2065, tm_mon=1, tm_mday=24, tm_hour=13, tm_min=20, tm_sec=0, tm_wday=5, tm_yday=24, tm_isdst=0)
## time.struct_time(tm_year=2065, tm_mon=1, tm_mday=24, tm_hour=5, tm_min=20, tm_sec=0, tm_wday=5, tm_yday=24, tm_isdst=0)

```

### 小结

时间戳是给计算机看的时间；时间字符串是人能够看懂的时间；元组则是用来操作时间的

## 几种格式之间的转换

![时间格式互换](https://files.zuiyu1818.cn/Python/time_transform.png)

```
#时间戳-->结构化时间
#time.gmtime(时间戳)    #UTC时间，与英国伦敦当地时间一致
#time.localtime(时间戳) #当地时间。例如我们现在在北京执行这个方法：与UTC时间相差8小时，UTC时间+8小时 = 北京时间
>>>time.gmtime(1500000000)
time.struct_time(tm_year=2017, tm_mon=7, tm_mday=14, tm_hour=2, tm_min=40, tm_sec=0,tm_wday=4, tm_yday=195, tm_isdst=0)
>>>time.localtime(1500000000)
time.struct_time(tm_year=2017, tm_mon=7, tm_mday=14, tm_hour=10, tm_min=40, tm_sec=0, tm_wday=4, tm_yday=195, tm_isdst=0)

#结构化时间-->时间戳　
#time.mktime(结构化时间)
>>>time_tuple = time.localtime(1500000000)
>>>time.mktime(time_tuple)
1500000000.0
```

![格式化时间](https://files.zuiyu1818.cn/Python/time_format.png)

```
#结构化时间 --> %a %b %d %H:%M:%S %Y串
#time.asctime(结构化时间) 如果不传参数，直接返回当前时间的格式化串
>>>time.asctime(time.localtime(1500000000))
'Fri Jul 14 10:40:00 2017'
>>>time.asctime()
'Mon Jul 24 15:18:33 2017'

#时间戳 --> %a %b %d %H:%M:%S %Y串
#time.ctime(时间戳)  如果不传参数，直接返回当前时间的格式化串
>>>time.ctime()
'Mon Jul 24 15:19:07 2017'
>>>time.ctime(1500000000)
'Fri Jul 14 10:40:00 2017'
```

## 计算毕业时间

```python
import time
time_graduate = time.mktime(time.strptime("2019/06/28 12:00:00", "%Y/%m/%d %H:%M:%S"))
print(time_graduate)
time_pass = time.time() - time_graduate
print(time_pass)
print("已经过去了%d天" % (time_pass//86400))
```

输出结果

```
1561694400.0
1306448.1965110302
已经过去了15天
```
