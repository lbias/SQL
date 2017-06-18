# NoSQL 简介

世界上除了「关系型」数据库，还有其他另类不使用 SQL 的数据库系统，泛称 [NoSQL](https://zh.wikipedia.org/wiki/NoSQL)。

这些 NoSQL 又可以分成几个类型：

## Key-value

[Redis](https://redis.io) 可说是一种小型数据结构瑞士刀，作为搭配用的数据库来使用。我们用 sidekiq 实作异步时看过它。

它的用途包括：

* 各式计数器：流量+1
* 简易标记( 例如同一 IP 十分钟内只算一次 pageview)
* 排行榜 zadd
* 自制 Inverted-Index Text Search
* Pub/Sub 聊天室
* Job Queue (例如 sidekiq)
主要是局部的、需要密集性写入的功能，改用 Redis 来操作，来降低主数据库的负担。

## Document-Oriented

[MongoDB](https://www.mongodb.com) 是一种泛用型的数据库，和 MySQL/PostgreSQL 打对台，特色是 Scheme-free 不需要定义 Schema。曾经有一阵子 Rails 社区非常流行，因为不需要用 Migration 去定义 Schema 就可以使用 Model，一开始用起来非常方便。不过后来大家发现营运久了以后，没有 Schema 的数据会变脏，造成后续维运的成本增加。所以后来就没这么流行了。

## Column-Oriented

关系型数据库的 Transaction 事务的缺点是效能。数据库在做 Transaction 事务时，不可避免地必须锁住一些数据，避免其他人同时修改。因此如果是一个写入流量非常大的网站，就说是一个售票网站好了，非常多人在开票时准时抢票，这时候数据库的效能就会非常差。

特别是当数据非常巨量的时候，需要多台数据库服务器时，Transaction 事务的写入速度，就会成为扩充的瓶颈。

根据 [CAP 定理](https://zh.wikipedia.org/zh-cn/CAP定理)告诉我们: RDBMS 在多服务器(P)架构下为了维持 C 特性，只能牺牲 A、NoSQL 让你有 tradeoff 的空间，牺牲 C 特性以换得 A、但这不是 C 和 A 二元的选择，而是 C 和 delay 延迟时间的取舍，你愿意容忍多少延迟时间才算作不 Available。

因此这类型的 NoSQL 不讲 ACID，而是讲 BASE 特性 (Basically Available, Soft state, Eventual consistency)，重点是 Eventual consistency。想像一个场景: Facebook 和 Twitter 贴文，当你贴文成功的时候，并不是当下马上其他人就可以看到你的贴文，这中间其实是有延迟时间的。这个延迟对于关系型数据库来说是不可以接受的，但是对于这种社交应用来说，却没有关系。牺牲 Consistence 一致性，就可以换到更多的写入反应效能，这就是这类型的 NoSQL 的设计目的。

* [Apache HBase](https://hbase.apache.org) 分布式、强调超大 Table： billions of rows X millions of columns (PB以上等级) (Google Big Table 的开源版本，由 Yahoo 推出)
* [Apache Cassandra](http://cassandra.apache.org) 分布式、最终一致性，高写入场景 (Amazon Dynamo 的开源版本，由 Facebook 推出)
* [Amazon DynamoDB](https://aws.amazon.com/cn/dynamodb/)
* [Google Cloud Datastore](https://cloud.google.com/datastore/)
如果你的数据量不到 1PB (=1000TB)，你就不需要考虑这类型的数据库了，用 MySQL 或 PostgreSQL 足矣。

## Graph: neo4j 图形数据库

[neo4j](https://neo4j.com) 用节点和边来储存数据
