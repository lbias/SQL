# 关系型数据库的特性一: Schema
在关系型数据库中，有一些共通的特性，第一个就是 Schema 纲要：

## Schema 纲要

所谓的 Schema 纲要就是使用数据库存数据前，需要先定义 Tables(表) 和 Columns(字段)：

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fgkui1lwi9j30c3037aa1.jpg)

例如在这张 Table，有 id、name、capacity、user_id 等字段，其中每一行(row)就一笔数据(data)。

相信大家用过试算表(Microsoft Excel 或 Apple Numbers)，这跟试算表看起来蛮像的：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fgkuie11syj30e40c5ab9.jpg)

在试算表中，每一格都可以随便你填什么，但是 Schema 纲要不一样。我们需要定义数据型别(Data Type)。每个字段(Column)都要指定格式，只有符合格式才能存进数据库。不同数据库的数据类型大同小异，大体上都有：

* 字符串：varchar 或 text。varchar 默认是 255 字符、text 默认是 65535 字符。如果你要开一个字段来存长篇文章，text 可能会不够存，需要在额外指定长度。
* 数字: Integer, Decimal, Float
* Blob 二进制: 可以存放档案。但是通常不建议把档案直接塞数据库，一来数据库塞太大不容易备份和管理、二来没有什么好处，因为你也没办法针对二进制档案进行条件搜寻和过滤。人们对于读档案也有心理准备会比较慢。所以通常只会在数据库里面纪录档案的 metadata 例如档名、大小、MimeType 等等，而实际的档案则放在档案系统上，或是上传到七牛或AWS S3等空间。
* Boolean 布林
* Date 日期
* Time 时间
* Datetime 日期时间
在 Rails Migration 之中，就是在定义这些表的字段是什么格式，例如：
```
 create_table :events do |t|
  t.string :name
  t.text   :description
  t.integer :capacity
  t.integer :user_id, :null => false

  t.timestamps
end```

在Rails 实战圣经有一份[对照表](https://ihower.tw/rails/migrations-cn.html#sec2)。Rails 会根据数据库的不同，自动对应使用不同的数据型态。

除了定义 Data Type，你还可以在 Schema 中定义限制 (constraint)，最基本就限制是 `NOT NULL` 必填不能为空。

数据库还可以设定更多数据验证，不过你会发现这跟我们在 Rails model 中的 validations 的作用是一样的。所以通常我们偏好在 Rails 里面做验证，而不是在数据库这层做。优缺点是：

* 在 Rails 做比较有弹性，可以用 Ruby 来写验证逻辑
* 在 DB 层做是硬条件，无法跳过这个验证
早期的数据库非常重视 Data/Entity Integrity 资料验证，这是因为早期的数据库用户，就是直接操作数据库。但是对近年来的网络公司来说，用户不会直接操作数据库，而是都会经过网站应用服务器(例如 Rails)，用我们写好的功能间接地使用到数据库，因此我们可以在应用层验证好数据即可。
