---
layout: post
title:  "MySQL优化"
date:   2017-12-03
excerpt: "查看并优化MySQL的一些方法"
tags: [MySQL]
feature: https://labs.mysql.com/common/logos/mysql-logo.svg?v2
comments: true
---
1. 用`EXPLAIN`查看SQL执行计划
- `type`列，连接类型。好的SQL语句至少要达到`range`级别。杜绝出现`all`级别。
- `key`列，使用到的索引名。如果没有选择索引，值是`NULL`。可以采取强制索引方式。
- `key_len`列，索引长度。
- `rows`列，扫描行数。是个预估值。
- `extra`列，详细说明。注意常见的不太友好的值有：`Using filesort`, `Using temporary`。

2. 当只需要一条数据的时候，使用`LIMIT 1`。

3. SQL语句中`IN`包含的值不应过多，能用`BETWEEN`就不要用`IN`，或者使用连接来替换。

4. `SELECT *`会增加不必要的系统消耗：CPU、IO、内存、网络带宽。会增加使用覆盖索引的可能性。当表结构发生改变时，前端也需要更新。所以，要求直接在`SELECT`后面接上字段名。

5. 如果排序字段没有用到索引，就尽量少排序。

6. 如果限制条件中其他字段没有索引，尽量少用`OR`，使用`UNION ALL`或者是`UNION`(必要的时候)的方式来代替`OR`会得到更好的效果。

7. `UNION`和`UNION ALL`的差异主要是前者需要将结果集合并后再进行唯一性过滤操作，这就会涉及到排序，增加大量的CPU运算，加大资源消耗及延迟。当然，`UNION ALL`的前提条件是两个结果集没有重复数据。

8. 不使用`ORDER BY RAND()`
- `SELECT id FROM table ORDER BY RAND() LIMIT 1000;`
- 优化后 
- `SELECT id FROM table t1 JOIN (SELECT RAND() * (SELECT MAX(id) FROM table) AS nid) t2 ON t1.id > t2.nid LIMIT 1000;`

9. 区分`IN`和`EXISTS`主要是造成了驱动顺序的改变（这是性能变化的关键）
- 如果是`EXISTS`，那么以外层表为驱动表，先被访问。
- 如果是`IN`，那么先执行子查询。
    所以`IN`适合于外表大而内表小的情况，`EXISTS`适合于外表小而内表大的情况。
    
10. 关于`NOT IN`和`NOT EXISTS`，推荐使用`NOT EXISTS`，不仅仅是效率问题，`NOT IN`可能存在逻辑问题。
- `SELECT id FROM table_a WHERE table_a.id NOT IN (SELECT table_b.id FROM table_b);`
- 优化后 
- `SELECT id FROM table_a LEFT JOIN table_b ON WHERE table_a.id = table_b.id WHERE table_b.id IS NULL;`

11. SQL语句做分页时，随着表数据量的增加，直接使用`LIMIT`分页查询会越来越慢，可以取前一页的最大行数的id，然后根据这个最大的id来限制下一页的起点。
- `SELECT id,name FROM table LIMIT 10, 20;`
- 优化后 
- `SELECT id,name FROM table WHERE id > 10 LIMIT 20;`

12. 扫描的行数成百万级以上的时候就可以通过程序使用分段查询，循环遍历，将结果合并处理进行展示。

13. 避免在`WHERE`子句中对字段进行`NULL`值判断，因为对于`NULL`的判断会导致引擎放弃使用索引而进行全表扫描。

14. 不建议使用`%`前缀模糊查询(但是可以使用`LIKE 'name%'`)，例如`LIKE '%name'`或者`LIKE '%name%'`，这种查询会导致索引失效而进行全表扫描。

普通索引是无法满足查询需求的。在MySQL中，有全文索引来帮助我们。

- `ALTER TABLE table ADD FULLTEXT INDEX 'idx_user_name'('user_name');`

15. 使用全文索引的SQL语句是，需要注意的是查询语句的写法与普通索引的区别：
- `SELECT id,fnum,fdst FROM dynamic_201606 WHERE MATCH(user_name) AGAINST('zhangsan' IN BOOLEAN MODE);`

16. 避免在`WHERE`子句中对字段进行表达式操作，对字段进行运算，会造成引擎放弃使用索引
- `SELECT user_id,user_project FROM user_base WHERE age*2=36;`
- 优化后
- `SELECT user_id,user_project FROM user_base WHERE age=36/2;`

17. `WHERE`子句中出现字段的类型和传入的参数类型不一致的时候会发生的类型转换，建议先确定`WHERE`中的参数类型，避免隐式类型转换。

18. 对于联合索引来说，要遵守最左前缀法则，常用的查询字段放在最前面。

19. 必要时可以使用`FORCE INDEX`来强制强制MySQL优化器使用我们制定的某个索引。

20. 对于联合索引来说，如果存在范围查询，比如between,>,<等条件时，会造成后面的索引字段失效。

21. 关于JOIN优化
    `LEFT JOIN` 左表为驱动表
    `INNER JOIN` 数据少的表作用驱动表
    `RIGHT JOIN` 右表为驱动表

22. MySQL中没有`FULL JOIN`，可以用以下方式来解决。
	`SELECT * FROM table_a a LEFT JOIN table_b b ON b.name = a.name WHERE b.name IS NULL UNION ALL SELECT * FROM b`;

23. 尽量使用inner join，避免left join，因为如果连接方式是inner join，在没有其他过滤条件的情况下MySQL会自动选择小表作为驱动表，但是left join在驱动表的选择上遵循的是左边驱动右边的原则，即left join左边的表名为驱动表。

24. 合理利用索引，使用被驱动表的索引字段作为on的限制字段。

25. 利用小表去驱动大表，减少嵌套循环中的循环次数，以减少 IO总量及CPU运算的次数。

26. inner join是由mysql选择驱动表，但是有些特殊情况需要选择另个表作为驱动表，比如有group by、order by等「Using filesort」、「Using temporary」时。STRAIGHT_JOIN来强制连接顺序，在STRAIGHT_JOIN左边的表名就是驱动表，右边则是被驱动表。在使用STRAIGHT_JOIN有个前提条件是该查询是内连接，也就是inner join。其他链接不推荐使用STRAIGHT_JOIN，否则可能造成查询结果不准确。
