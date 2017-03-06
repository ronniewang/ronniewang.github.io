---
layout: default
title: Mysql交换两列的值
description: 不小心写了个bug, 两个字段值入库的时候写反了, 得纠正过来
category: tech
---

创建一个测试的表

```
create table test_swap(x char(10), y char(10));
```

插入几条数据

```
insert into test_swap values('x1', 'y1'), ('x2', 'y2'), ('x3', null), (null, 'y4');
```

看一下现在表的样子

```
select * from test_swap;
```

输出

```
+------+------+
| x    | y    |
+------+------+
| x1   | y1   |
| x2   | y2   |
| x3   | NULL |
| NULL | y4   |
+------+------+
4 rows in set (0.00 sec)
```

执行交换语句

```
update test_swap set x=(@t:=x), x=y, y=@t;
```

再看一下交换后表的样子

```
select * from test_swap;
```

输出

```
+------+------+
| x    | y    |
+------+------+
| y1   | x1   |
| y2   | x2   |
| NULL | x3   |
| y4   | NULL |
+------+------+
4 rows in set (0.00 sec)
```

交换成功

参考文档

* <http://stackoverflow.com/questions/37649/swapping-column-values-in-mysql>
