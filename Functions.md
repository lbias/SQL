# Functions

数据库也有提供一些 Function 可以用在 SQL 里面：

## 计算 Aggregations

数量

`SELECT COUNT(*) AS event_count FROM events;`

>加上 AS 别名才比较好识别处理

>对应的 Rails 语法是 `Event.count`
最小和最大值

`SELECT MIN(capacity) as min_capacity FROM events;`
`SELECT MAX(capacity) as max_capacity FROM events;`

>对应的 Rails 语法是 `Event.minimum(:capacity)` 和 `Event.maximum(:capacity)`

总和

`SELECT SUM(capacity) as sum_capacity FROM events;`

>对应的 Rails 语法是 `Event.sum(:capacity)`

平均

`SELECT SUM(capacity) / COUNT(capacity) as avg_capacity FROM events;`
或
`SELECT AVG(capacity) as avg_capacity FROM events;`

对应的 Rails 语法是 `Event.average(:capacity)`

### 分类 GROUP BY

GROUP BY 分类功能主要是用来搭配上述 aggregating function 来使用的，例如请回答这个问题：计算每个 user 有多少 events?

让我们试试看：

* `SELECT user_id, COUNT(*) FROM events;` 这样不行，user_id 只会回传第一笔的 user_id
* `SELECT user_id, COUNT(*) FROM events GROUP BY user_id;` 还是不行，没有 user 的名字
* `SELECT users.name, COUNT(events.id) FROM events JOIN users ON users.id = events.user_id GROUP BY user_id;` 不对，结果少了 Roy
* `SELECT users.name, COUNT(events.id) FROM users LEFT JOIN events ON users.id = events.user_id GROUP BY user_id;` 正确答案
* `SELECT users.name, COUNT(events.id) AS c FROM users LEFT JOIN events ON users.id = events.user_id GROUP BY user_id HAVING c > 1 ORDER BY c DESC;` 可再加条件和排序

![](https://ws1.sinaimg.cn/large/006tNc79gy1fgpcbbvtuaj30u00kiq5g.jpg)

>其中 `WHERE` 是给 source tables 的条件，`HAVING` 才是 aggregation 后的条件

### DISTINCT

可以去除重复的数据

`SELECT DISTINCT(user_id) FROM events;`

### 其他函式

数据库还有提供其他函数，例如 字串 SQLite - [Useful Functions](http://www.tutorialspoint.com/sqlite/sqlite_useful_functions.htm)、时间 SQLite - [Date And Time Functions](https://www.sqlite.org/lang_datefunc.html) 等等。

不过通常比较少用到，因为我们偏好捞出来数据后，交由 Ruby 处理即可。

## 为何要 Joining

回头想想看为什么需要 Joining 语法。SQL 的 Joining 语法是对新手比较困难的部分，没办法完全掌握是正常的。很多时候其实我们在 Rails 先将需要的数据通通捞出来，然后用 Ruby 进行过滤跟组合似乎也可以达成目标，为什么需要用到这些看起来很复杂的 SQL Joining 语法呢?

主要的原因还是查询速度和需要的内存空间，数据库是一套针对 SQL 优化非常快速的软件，因此可以用远比 Ruby 高效的方式来取出数据。更何况如果全部的数据都拿出来用 Ruby 处理，很可能内存也不够。例如以下的问题：请回答去年第三季所有商品的销售额，并根据分类计算总额。去年一整年的销售可能多达上百万笔，如果要逐笔捞出用 Ruby 处理，效能会非常低下。这时候就必须用 SQL 精准地捞出想要的数据才是可行的方式。
