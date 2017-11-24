### 使用explain语句去查看分析结果

`explain sql` 之后会出现：
`id`  `select_type`  `table`  `type` `possible_keys`  `key` `key_len`  `ref` `rows`  `extra`各列

`id` 表示MySQL Query Optimizer 选定的执行计划中查询的序列号。表示查询中执行 select 子句或操作表的顺序,id 值越大优先级越高,越先被执行。id 相同,执行顺序由上至下。

`table` 输出行所引用的表


`select_type`

select_type查询类型 |	说明
-| -
SIMPLE	|简单的 select 查询,不使用 union 及子查询
PRIMARY |	最外层的 select 查询
UNION |	UNION 中的第二个或随后的 select 查询,不 依赖于外部查询的结果集
DEPENDENT UNION	| UNION 中的第二个或随后的 select 查询,依 赖于外部查询的结果集
SUBQUERY	| 子查询中的第一个 select 查询,不依赖于外 部查询的结果集
DEPENDENT SUBQUERY | 子查询中的第一个 select 查询,依赖于外部 查询的结果集
DERIVED	| 用于 from 子句里有子查询的情况。 MySQL 会 递归执行这些子查询, 把结果放在临时表里。
UNCACHEABLE SUBQUERY	|结果集不能被缓存的子查询,必须重新为外 层查询的每一行进行评估。
UNCACHEABLE UNION |	UNION 中的第二个或随后的 select 查询,属 于不可缓存的子查询

`type` 重要的项,显示连接使用的类型,按最 优到最差的类型排序

type值 |	说明
-|-
system |	表仅有一行(=系统表)。这是 const 连接类型的一个特例。
const |	const 用于用常数值比较 PRIMARY KEY 时。当 查询的表仅有一行时,使用 System。
eq_ref |	const 用于用常数值比较 PRIMARY KEY 时。当 查询的表仅有一行时,使用 System。
ref |	连接不能基于关键字选择单个行,可能查找 到多个符合条件的行。 叫做 ref 是因为索引要 跟某个参考值相比较。这个参考值或者是一 个常数,或者是来自一个表里的多表查询的 结果值。
ref_or_null |	如同 ref, 但是 MySQL 必须在初次查找的结果 里找出 null 条目,然后进行二次查找。
index_merge	| 说明索引合并优化被使用了。
unique_subquery |	在某些 IN 查询中使用此种类型,而不是常规的 ref:value IN (SELECT primary_key FROM single_table WHERE some_expr)
index_subquery |	在 某 些 IN 查 询 中 使 用 此 种 类 型 , 与 unique_subquery 类似,但是查询的是非唯一 性索引: value IN (SELECT key_column FROM single_table WHERE some_expr)
range |	只检索给定范围的行,使用一个索引来选择 行。key 列显示使用了哪个索引。当使用=、 <>、>、>=、<、<=、IS NULL、<=>、BETWEEN 或者 IN 操作符,用常量比较关键字列时,可 以使用 range。
index |	全表扫描,只是扫描表的时候按照索引次序 进行而不是行。主要优点就是避免了排序, 但是开销仍然非常大。
all	| 最坏的情况,从头到尾全表扫描。

`possible_keys`	指出 MySQL 能在该表中使用哪些索引有助于 查询。如果为空,说明没有可用的索引。

`key`	MySQL 实际从 possible_key 选择使用的索引。 如果为 NULL,则没有使用索引。很少的情况 下,MYSQL 会选择优化不足的索引。这种情 况下,可以在 SELECT 语句中使用 USE INDEX (indexname)来强制使用一个索引或者用 IGNORE INDEX(indexname)来强制 MYSQL 忽略索引

`key_len`	使用的索引的长度。在不损失精确性的情况 下,长度越短越好

`ref`	显示索引的哪一列被使用了

`rows`	MYSQL 认为必须检查的用来返回请求数据的行数

`extra` 中出现以下 2 项意味着 MYSQL 根本不能使用索引,效率会受到重大影响。应尽可能对此进行优化。

extra |	说明
-|-
Using filesort | 	表示 MySQL 会对结果使用一个外部索引排序,而不是从表里按索引次序读到相关内容。可能在内存或者磁盘上进行排序。MySQL 中无法利用索引完成的排序操作称为“文件排序”
Using temporary	 |  表示 MySQL 在对查询结果排序时使用临时表。常见于排序 order by 和分组查询 group by。




