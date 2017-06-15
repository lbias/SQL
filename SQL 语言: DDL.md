# SQL 语言: DDL
关系型数据库使用 SQL(Structured Query Language) 语言，每个 SQL 句子叫做 SQL Query 或 SQL Statement。我们可以用 CLI 指令或 GUI 软件，用 SQL 语言对数据库进行查询和操作。

SQL 分成 DDL 和 DML 两种，都是用分号 `;` 结尾。

## Data Definition Language, DDL

如何告诉数据库去定义 Schema 纲要? 也是使用 SQL 语法，这类型的 SQL 就做 DDL(Data Definition Language)

* 建立、删除和更名数据库

每家方法不太一样，建议可以用 GUI 进行即可。PostgreSQL 和 MySQL 都是数据库服务器，可以管理很多不同数据库，例如你可以架很多 Rails 网站，但是只需要一个数据库服务器，里面建立不同数据库即可。

建立数据库时，请注意选择编码(Encoding)。PostgreSQL 可用 utf-8、MySQL 可用 utf8mb4 编码。

在 SQLite3 的话，直接在 Terminal 用 CLI 指令 `sqlite3 your_db_name.db` 就会打开(或产生)
一个数据库档案。直接砍掉档案就是删除数据库。

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fgloz43jm6j30m108a0t8.jpg)

> MySQL 的话，指令式 `mysql -u root -p`。PostgreSQL 的指令是 `psql <database_name>`。
以下用 SQLite3 示范。

* 建立 Table

以下 SQL 会建立 `events` 表，并新增三个字段 `name`, `capacity` 和 `date`。默认是字段允许 `NULL`，除非加上 `NOT NULL`。

 `CREATE TABLE events (name VARCHAR(50) NOT NULL, capacity INTEGER, date DATE);`

![](https://ws1.sinaimg.cn/large/006tNbRwly1fglp0mxijaj30m107pmxt.jpg)

你可以用 SQLite3 的 [GUI DB Browser for SQLite](http://sqlitebrowser.org) 打开 `demo.db` 这个档案：

![](https://ws3.sinaimg.cn/large/006tNbRwly1fglp1f5zm5j30u00ic0uw.jpg)

用 GUI 进行 Schame 操作会比较简单，在 Rails 的话，则是统一都用 Migrations 机制来变更 Schema。

* 修改 Table

改名 Table，例如 `ALTER TABLE persons RENAME TO people;`
新增字串，例如 `ALTER TABLE people ADD COLUMN status VARCHAR(50);`
修改和移除字段：SQLite3 没支援，需要开一个新 table 然后把资料复制过去

* 删除 Table

语法是 `DROP TABLE IF EXISTS people;`

通常不会让终端使用者可以动态新建 table 和修改 schema。数据库的 Schema 比较像是你程式的一部分，我们的代码会依赖于 Schema 设计。另外也有效能的考量，变更 Schema 是很耗时的操作，特别是数据量已经很多的情况下，修改 Schema 会锁住整个 Table 影响网站运作。

* Migration 机制

数据库 Schema 不是一成不变的，会随着软件变更升级也会有修改的需要。因此，在一些软件中会实作一种叫做 Migration 的功能，透过 Schema Migration 纪录目前的 schema 版本。开机的时候检查目前程式的版本和数据库里面的版本是否相同，不同的话，执行 Migration 更新 schema。这些 Migration 代码也会放进版本控制系统 Git 里面，这样整个团队的开发者和不同服务器上，都可以利用 Migration 来一致管理 Schame。

这个功能就是大家熟习的 Rails Migration。
