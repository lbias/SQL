# 数据库规范化 Normalization
一个数据库会包含很多张表，那么这些表要如何因应需求来做设计呢？该设计哪些表？该设计哪些字段？这两节会告诉大家。

[数据库规范化(Normalization](https://zh.wikipedia.org/zh-cn/数据库规范化)是数据库设计的一个非常重要的基本概念，目的是要去除重复的数据，增加数据的一致性。实际的作法是会将重复的字段，抽出来变成另一个新的表。

规范化还分成一阶规范化、二阶规范化、三阶规范化、DK/NF规范化等等不同级别，一般来说我们的应用软件会做到二阶或三阶规范化。

让我们用实际的例子来看：假设我们要设计一个场景是纪录「使用者 User」参与多个「活动 Event」

## 未规范化

user name	| user city	| zipcode	| event 1	| event 1 date	| event 2	| event 2 date	| event 3	| event 3 date
---------	| ---------	| ---------	| ---------	| ---------	| ---------	| ---------	| ---------	| ---------
ihower	| hsinchu	| 300	| RubyConf	| 2015/9/11	| JSConf	| 2015/5/1	| CSSConf	| 2016/1/1
john	| taipei	| 100	| RubyConf	| 2015/9/11				

这种 Table 设计缺点很多：

1. ihower 如果需要参加第四个活动，就必须变更 Table 的 Schema 增加更多字段。但是变更 Scmema 是一件成本很高的事情 :(
1. john 没有参加这么多活动，多馀的字段都是 NULL，对数据库来说是浪费空间
1. ihower 跟 john 都有参加 RubyConf，但是 2015/9/11 这个活动日期重复存了。而且如果活动改日期，表示这些值都要改

## 一阶: 移除重复语意的 columns

刚刚的 event 1, event 1 date, event 2, event 2 date, event 3, event 3 date 俩俩都是重复的，让我们消灭它们：

user name	| user city	| zipcode	| event	| event date
--------	| --------	| --------	| --------	| --------
ihower	| hsinchu	| 300	| RubyConf	| 2015/9/11
ihower	| hsinchu	| 300	| JSConf	| 2015/5/1
ihower	| hsinchu	| 300	| CSSConf	| 2016/1/1
john	| taipei	| 300	| RubyConf	| 2015/9/11
缺点:

* 相比于没有规范化的，现在新报名不需要修改 Schame 了
* 但是重复的资料更多，包括用户数据和活动数据

## 二阶: 移除重复语意的 row

接下来需要拆表了，一口气我们拆成三张表：users 表、events 表、registration 表，并且 users 和 events 要加上可识别的 id 字段。

>相信对 Rails 已经很熟悉的同学，这就是 User model 和 Event model 透过 Registration model 来达成多对多关系。

users table

user id	| user name	| user city	| zipcode
--------	| --------	| --------	| --------
1	| ihower	| hsinchu	| 300
2	| john	| taipei	| 100
events table

event id	| event	| event date
--------	| --------	| --------
1	| RubyConf	| 2015/9/11
2	| JSConf	| 2015/5/1
3	| CSSConf	| 2016/1/1
registrations table

user id	| event id	| register_at
--------	| --------	| --------
1	| 1	| 2016-03-16	12:00:00
1	| 2	| 2016-03-16	12:30:00
1	| 3	| 2016-03-17	12:00:00
2	| 1	| 2016-03-18	12:00:00
这好多了，看起来没有重复的资料了，如果要改 user 或 event 的数据，只需要改一个地方。

但是还有一个小地方可以改进，让我们继续看下去：

## 三阶: 移除不依赖主 ID 的资料

在 users table 中，city 名字其实只跟 zipcode 相关，跟 user id 没关系，因此这个 city 可以拆出来。在 users table 中只需要留着 zipcode 就好了。

users table

user id	| user name	| zipcode
--------	| --------	| --------
1	| ihower	| 300
2	| john	| 300

zipcodes table

zipcode	| user city
--------	| --------
300	| hsinchu
100	| taipei

## 小结

规范化让数据不会重复和高度一致性，节省空间、增加修改数据时的效率、避免数据不一致的错误。
