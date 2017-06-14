# 关系型数据库的特性三: ACID
## 需求
关系型数据库的另一个重要的特性是 [ACID](https://en.wikipedia.org/wiki/ACID)，也就是
Atomicity, Consistency, Isolation, Durability。在解释个别的意义前，我们先介绍一个关系型数据库的功能，叫做 Transaction 事务。

请想像这样的场景：当你再做银行转帐时，A 的馀额会减少、B 的馀额会增加，如果这是两个 SQL 操作的话，我们如何能保证这两个 SQL 操作必须是一起成功的？不能发生 A 钱变少了，但是 B 没有收到钱的情况。或是考虑一个更极端的场景，如果你和别人同时同一秒钟互相转帐，数据库会不会算错馀额？

要达成这种跨 Tables 多个 SQL 操作必须同时完成(或失败)的需求，就必须用上 Transaction 事务。语法是用 `BEGIN;` 和 `COMMIT;` 把 SQL 句包裹在一起，例如：

 BEGIN;
  INSERT INTO histories (user_id, amount) VALUES (1, -100);
  INSERT INTO histories (user_id, amount) VALUES (2, 100);
  UPDATE accounts SET balance=200 WHERE id=1;
  UPDATE accounts SET balance=300 WHERE id=2;
COMMIT;
每个 SQL 句必须用分号 `;` 代表结束
这样 `BEGIN;` 和 `COMMIT;` 中间的所有 SQL 句，就会一起递交给数据库，要麻一起成功、要麻一起失败。

非常多场景会用到这个 Transaction 功能，来保证数据的正确性。在 Rails 中，每个 model 的 `save` 其实都会用 Transaction 包起来，包括 model 里面的所有 callback 都会在同一个 Transaction 事务里面。

如果是跨 model 的话，你也可以用 `ActiveRecord::Base.transaction` 来包裹 `Transaction` 事务，例如：
```
 ActiveRecord::Base.transaction do
  A.save
  B.save
  C.save
end
```
## ACID

ACID 其实就是在说明 Transactin 的能耐，以下取自 [wikipedia](https://zh.wikipedia.org/zh-cn/ACID):

* Atomicity 原子性：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
* Consistency 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
* Isolation 隔离性：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
* Durability 持久性：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。
透过 Transactions 事务功能，关系型数据库可以在多人连线同时执行多个 SQL 句的情况下，也可以保证数据最后的正确性。
