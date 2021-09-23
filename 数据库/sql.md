#### select



#### 执行顺序

(1) FROM 子句 组装来自不同数据源的数据

(2) WHERE 子句 基于指定的条件对记录进行筛选

(3) GROUP BY 子句 将数据划分为多个分组

(4) 使用聚合函数进行计算（MAX是对整列，不分组）

(5) 使用HAVING子句筛选分组

(6) 计算所有的表达式

(7) 使用ORDER BY对结果集进行排序







* #### where 条件查询

| operator（操作符） | Condition（解释）                                            | Example（例子）                                              |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| =                  | Case sensitive exact string comparison (*notice the single equals*)完全等于 | col_name = "abc"                                             |
| != or <>           | Case sensitive exact string inequality comparison 不等于     | col_name != "abcd"                                           |
| LIKE               | Case insensitive exact string comparison 没有用通配符等价于 = | col_name LIKE "ABC"                                          |
| NOT LIKE           | Case insensitive exact string inequality comparison 没有用通配符等价于 != | col_name NOT LIKE "ABCD"                                     |
| %                  | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) 通配符，代表匹配0个以上的字符 | col_name LIKE "%AT%" (matches "AT", "ATTIC", "CAT" or even "BATS") "%AT%" 代表AT 前后可以有任意字符 |
| _                  | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE) 和% 相似，代表1个字符 | col_name LIKE "AN_" (matches "AND", but not "AN")            |
| IN (…)             | String exists in a list 在列表                               | col_name IN ("A", "B", "C")                                  |
| NOT IN (…)         | String does not exist in a list 不在列表                     | col_name NOT IN ("D", "E", "F")                              |



* #### sort 查询

  ```sql
  SELECT column, another_column, …
  FROM mytable
  WHERE condition(s)
  ORDER BY column ASC/DESC;
  LIMIT num_limit OFFSET num_offset;
  ```

  

* ASC 升序

* DESC 降序

* `LIMIT` 和 `OFFSET` 子句通常和`ORDER BY` 语句一起使用，当我们对整个结果集排序之后，我们可以 `LIMIT`来指定只返回多少行结果 ,用 `OFFSET`来跳过某一行。



#### 连接查询

* ```sql
  JOIN //只保存两个表的交集
  LEFT JOIN //左边的表，未匹配的也保留
  rigth join 
  ```

* is not null 
* is null  查询为空或者不为空



#### 表达式查询

* 可以用+-/*% 对属性进行加减操作

* 使用mysql自带的一些函数等等，ABS()绝对值

* ```sql
  SELECT Id,Title,(Domestic_sales+International_sales)/1000000 as s FROM movies
  JOIN Boxoffice 
  where ABS(rating>0)
  ```

  

聚合查询

| Function                                  | Description |
| ----------------------------------------- | ----------- |
| **COUNT(*****)**, **COUNT(***column***)** |             |
| **MIN(***column***)**                     |             |
| **MAX(***column***)**                     |             |
| **AVG(***column*)                         |             |
| **SUM(***column***)**                     |             |



HAVING\

* having字句可以让我们筛选分组之后的各种数据，where字句在聚合前先筛选记录，也就是说作用在group by和having字句前。

* ==列函数对于group by子句定义的每个组各返回一个结果==

  ```sql
  
  显示每个地区的总人口数和总面积．仅显示那些面积超过1000000的地区。
  SELECT region, SUM(population), SUM(area)
  FROM bbc
  GROUP BY region
  HAVING SUM(area)>1000000
  在这里，我们不能用where来筛选超过1000000的地区，因为表中不存在这样一条记录。
  相反，having子句可以让我们筛选分组后的各组数据
  ```

  

  

* 而having子句在聚合后对组记录进行筛选。我的理解就是真实表中没有此数据，这些数据是通过一些函数产生的。