---
layout: single
title: 高性能MySQL 读书笔记
---
- [架构](#架构)
  - [并发控制](#并发控制)
    - [读写锁](#读写锁)
    - [锁粒度](#锁粒度)
      - [两种重要的锁策略](#两种重要的锁策略)
  - [事务](#事务)
    - [四种隔离级别](#四种隔离级别)
    - [多版本并发控制MVCC](#多版本并发控制mvcc)
  - [引擎](#引擎)
    - [InnoDB](#innodb)
    - [MyISAM](#myisam)
- [服务器性能剖析](#服务器性能剖析)
  - [应用程序的性能剖析](#应用程序的性能剖析)
  - [关于MySQL的查询](#关于mysql的查询)
    - [整体负载](#整体负载)
    - [单条查询优化](#单条查询优化)
    - [如何诊断间歇性问题](#如何诊断间歇性问题)
- [schema与数据类型的优化](#schema与数据类型的优化)
  - [优化的数据类型](#优化的数据类型)
    - [原则](#原则)
    - [整数类型](#整数类型)
    - [实数类型](#实数类型)
    - [字符串类型](#字符串类型)
    - [日期和时间类型](#日期和时间类型)
    - [位数据类型](#位数据类型)
    - [如何选定标识符](#如何选定标识符)
  - [schema设计中的陷阱](#schema设计中的陷阱)
  - [范式和反范式](#范式和反范式)
    - [范式的优点和缺点](#范式的优点和缺点)
    - [反范式的优点和缺点](#反范式的优点和缺点)
    - [实际应用中经常是混用范式化和反范式化](#实际应用中经常是混用范式化和反范式化)
  - [缓存表和汇总表](#缓存表和汇总表)
  - [如何加快ALTER TABLE](#如何加快alter-table)
- [创建高性能的索引](#创建高性能的索引)
  - [什么是索引](#什么是索引)
    - [B-tree索引](#b-tree索引)
    - [哈希索引](#哈希索引)
    - [空间数据索引](#空间数据索引)
    - [全文索引](#全文索引)
  - [索引好在哪里](#索引好在哪里)
  - [高性能的索引策略](#高性能的索引策略)
    - [选择独立的列](#选择独立的列)
    - [前缀索引](#前缀索引)
    - [选择合适的索引策略](#选择合适的索引策略)
    - [聚簇索引](#聚簇索引)
    - [覆盖索引](#覆盖索引)
    - [使用索引扫描来做排序](#使用索引扫描来做排序)
    - [压缩索引](#压缩索引)
    - [冗余和重复索引](#冗余和重复索引)
    - [未使用索引](#未使用索引)
    - [索引和锁](#索引和锁)
  - [维护索引和表](#维护索引和表)
- [查询性能优化](#查询性能优化)
  - [查询速度慢的原因](#查询速度慢的原因)
  - [访问的数据太多：优化数据访问](#访问的数据太多优化数据访问)
    - [是不是在检索超多数据，访问了太多的行，或者是列](#是不是在检索超多数据访问了太多的行或者是列)
    - [是不是在扫描额外的记录](#是不是在扫描额外的记录)
  - [重构查询的方式](#重构查询的方式)
    - [切分查询：分而治之的思路](#切分查询分而治之的思路)
    - [分解关联查询](#分解关联查询)
  - [查询是怎么执行的？](#查询是怎么执行的)
    - [MySQL客户端/服务器端通信协议](#mysql客户端服务器端通信协议)
    - [查询缓存（名词）](#查询缓存名词)
    - [查询优化](#查询优化)
    - [查询执行引擎](#查询执行引擎)
    - [返回结果给客户端](#返回结果给客户端)
  - [查询优化器的局限性](#查询优化器的局限性)
    - [关联子查询可能有一些问题](#关联子查询可能有一些问题)
    - [UNION的限制](#union的限制)
    - [等值传递的问题](#等值传递的问题)
    - [无法并行执行查询](#无法并行执行查询)
    - [哈希关联](#哈希关联)
    - [松散索引扫描](#松散索引扫描)
    - [最大值和最小值优化](#最大值和最小值优化)
    - [同时查询和更新。](#同时查询和更新)
  - [使用优化器提示来改变执行计划](#使用优化器提示来改变执行计划)
  - [优化特定类型的查询](#优化特定类型的查询)
    - [优化count()](#优化count)
    - [优化关联查询](#优化关联查询)
    - [优化子查询](#优化子查询)
    - [优化GROUP BY 和 DISTINCT](#优化group-by-和-distinct)
    - [优化limit](#优化limit)
    - [优化SQL_CALC_FOUND_ROWS](#优化sql_calc_found_rows)
    - [优化union查询](#优化union查询)
    - [在使用自定义的变量时的局限](#在使用自定义的变量时的局限)
- [新的特性](#新的特性)
  - [分区表](#分区表)
    - [分区表原理](#分区表原理)
    - [分区表的类型](#分区表的类型)
    - [如何使用分区表](#如何使用分区表)
    - [分区表的问题](#分区表的问题)
    - [使用分区进行查询优化](#使用分区进行查询优化)
    - [合并表](#合并表)
  - [视图](#视图)
    - [可更新视图](#可更新视图)
    - [视图对性能的影响](#视图对性能的影响)
    - [视图的限制](#视图的限制)
  - [外键约束](#外键约束)
  - [在MySQL内部存储代码（性能方面）](#在mysql内部存储代码性能方面)
    - [触发器](#触发器)
    - [事件](#事件)
  - [游标](#游标)
  - [绑定变量](#绑定变量)
  - [用户自定义函数](#用户自定义函数)
  - [插件](#插件)
  - [字符集](#字符集)
    - [服务器和客户端通信中的字符集](#服务器和客户端通信中的字符集)
    - [选择字符集和校对规则](#选择字符集和校对规则)
    - [字符集等对于查询的影响](#字符集等对于查询的影响)
  - [全文索引](#全文索引-1)
    - [自然语言的全文索引](#自然语言的全文索引)
    - [布尔全文索引](#布尔全文索引)
    - [全文索引的限制](#全文索引的限制)
    - [解决问题的小技巧](#解决问题的小技巧)
  - [分布式（XA）事务](#分布式xa事务)
    - [内部XA事务](#内部xa事务)
    - [外部XA事务](#外部xa事务)
  - [查询缓存](#查询缓存)
    - [如何判断命中](#如何判断命中)
    - [如何使用内存](#如何使用内存)
    - [什么情况下查询缓存能够发挥作用](#什么情况下查询缓存能够发挥作用)
    - [InnoDB和查询缓存](#innodb和查询缓存)
    - [通用缓存优化](#通用缓存优化)
    - [查询缓存替代方案](#查询缓存替代方案)
- [优化服务器设置](#优化服务器设置)
  - [配置机制](#配置机制)
    - [设置变量的副作用](#设置变量的副作用)
    - [如何设置变量](#如何设置变量)
  - [如何配置MySQL文件](#如何配置mysql文件)
    - [如何设置InnoDB缓冲区大小](#如何设置innodb缓冲区大小)
  - [如何配置内存](#如何配置内存)
    - [确定可以使用的内存上限](#确定可以使用的内存上限)
    - [确定每个连接需要的内存](#确定每个连接需要的内存)
    - [确定操作系统要多少内存](#确定操作系统要多少内存)
    - [确定缓存需要的内存](#确定缓存需要的内存)
      - [InnoDB缓存池](#innodb缓存池)
      - [MyISAM的键缓冲](#myisam的键缓冲)
      - [线程缓存](#线程缓存)
      - [表缓存](#表缓存)
      - [InnoDB数据字典](#innodb数据字典)
  - [如何配置MySQL的I/O行为](#如何配置mysql的io行为)
    - [InnoDB事务日志](#innodb事务日志)
    - [InnoDB表空间](#innodb表空间)
    - [MyISAM配置](#myisam配置)
  - [配置MySQL并发](#配置mysql并发)
  - [基于工作负载的配置](#基于工作负载的配置)
- [操作系统和硬件优化](#操作系统和硬件优化)
  - [CPU和I/O](#cpu和io)
  - [如何选择CPU](#如何选择cpu)
  - [平衡内存和磁盘资源](#平衡内存和磁盘资源)
  - [固态存储](#固态存储)
  - [为备库选择硬件](#为备库选择硬件)
  - [有关RAID](#有关raid)
  - [SAN和NAS](#san和nas)
  - [多磁盘卷](#多磁盘卷)
  - [操作系统](#操作系统)
  - [文件系统](#文件系统)
- [复制](#复制)
  - [复制能够解决的问题](#复制能够解决的问题)
  - [复制的原理](#复制的原理)
    - [基于语句的复制](#基于语句的复制)
    - [基于行的复制](#基于行的复制)
    - [涉及的文件](#涉及的文件)
    - [备库的备库](#备库的备库)
  - [如何进行配置？](#如何进行配置)
    - [创建账号](#创建账号)
    - [配置主库和从库](#配置主库和从库)
    - [启动复制](#启动复制)
    - [关于复制的推荐设置](#关于复制的推荐设置)
  - [复制拓扑结构](#复制拓扑结构)
    - [一主多从](#一主多从)
    - [主主模式下的主主复制](#主主模式下的主主复制)
    - [主被模式下的主主复制](#主被模式下的主主复制)
    - [有备库的主主结构](#有备库的主主结构)
    - [环形复制](#环形复制)
    - [使用分发主库](#使用分发主库)
    - [树形结构](#树形结构)
    - [其他定制](#其他定制)
  - [容量规划](#容量规划)
  - [如何维护复制](#如何维护复制)
    - [监控复制](#监控复制)
    - [测量备库延迟](#测量备库延迟)
    - [确定主备一致](#确定主备一致)
    - [从主库重新同步备库](#从主库重新同步备库)
    - [改变主库](#改变主库)
    - [主主复制交换角色](#主主复制交换角色)
  - [复制的问题及其解决](#复制的问题及其解决)
  - [复制的速度](#复制的速度)
  - [其他特性](#其他特性)
- [可扩展性](#可扩展性)
  - [如何生成一个全局唯一的ID？](#如何生成一个全局唯一的id)
  - [MySQL如何通过集群扩展](#mysql如何通过集群扩展)
  - [MySQL通过归档文件来向内扩展](#mysql通过归档文件来向内扩展)
  - [负载均衡](#负载均衡)
    - [常见的读写分离的方法](#常见的读写分离的方法)
    - [引入中间件分发负载](#引入中间件分发负载)
- [高可用](#高可用)
  - [宕机的原因](#宕机的原因)
  - [如何实现高可用性](#如何实现高可用性)
    - [提升平均失效时间](#提升平均失效时间)
    - [降低平均恢复时间](#降低平均恢复时间)
  - [如何避免单点失效](#如何避免单点失效)
  - [故障转移和故障恢复](#故障转移和故障恢复)
- [备份](#备份)
  - [逻辑备份和物理备份](#逻辑备份和物理备份)
  - [备份的内容](#备份的内容)
    - [文件系统快照](#文件系统快照)
    - [从备份中恢复](#从备份中恢复)
- [工具](#工具)

## 架构
他的架构设计把**查询处理以及系统任务**和**数据的存储和提取**相分离。

![架构图](https://i.loli.net/2020/05/25/r4DdlefLk1gFtqY.png)

### 并发控制
通过创建锁，来控制并发的资源占用问题。
#### 读写锁
读锁，也叫共享锁，互相不阻塞。
写锁，也叫排他锁，会阻塞其他的读锁和写锁。
#### 锁粒度
通过区分加锁的数据量，来提升系统的并发量。但是锁操作都会增加系统的开销。所以要在安全性和锁开销之间寻求一个平衡。
##### 两种重要的锁策略
表锁：开销最小的策略。一般采用mysql自己实现的表锁而忽略存储引擎的锁机制。
行锁：最大程度的并发处理，伴随最大的开销。且只在存储引擎中实现。

### 事务
四大特性：
* 原子性：要么全部成功，要么全部失败
* 一致性：从一个一致性转换成另一种一致性
* 隔离性：所做的改变在最终提交之前，对于其他事务来说是不可见的
* 持久性：事务提交之后改动不会丢失

#### 四种隔离级别
较低级别的隔离通常可以执行更高的并发，开销也更低。
* read uncommitted（未提交读）：很少使用，问题很多
* read uncommitted（提交读/不可重复读）：大多数数据库系统的默认级别（Mysql不是），其他事务执行两次相同的读可能得到不一样的结果。
* repeatable read（可重复读）：MySQL默认隔离级别。解决脏读问题，会有幻行的问题（多了一行）。Mysql已经解决了这一问题。
* serializable（串行）：强制事务串行执行。对读取的所有行都加锁。

避免不可重复读锁行就行
避免幻读锁表就行

#### 多版本并发控制MVCC
行级锁的变种，很多情况下避免了加锁操作，开销更低。

InnoDB的MVCC通过在每一行后面保存两个隐藏的列来实现，一个是行的创建时间，一个是行的过期时间（版本号）

### 引擎
#### InnoDB
数据存储在表空间中。表是基于聚簇索引建立的。
#### MyISAM
不支持事务和行级锁，崩溃后无法恢复。

MYD和MYI文件存储，分别存储数据文件和索引文件。


## 服务器性能剖析
性能优化并不是降低cpu利用率、也不是提升每秒查询量
### 应用程序的性能剖析
对任何需要消耗时间的任务都可以进行性能剖析。性能瓶颈可能有很多的影响因素：
* 外部资源。调用了外部的服务或者搜索引擎
* 应用处理大量的数据，分析一个超大规模的XML文件
* 在循环中执行昂贵的操作，滥用正则表达式
* 使用了低效的算法

性能剖析会导致服务器变慢，但是为了定位一些性能平静下来，还是值得的，可以通过采样的方式发现严重问题。
### 关于MySQL的查询
#### 整体负载
**慢查询日志**
记录执行时间超过某一值的语句。是开销最低、精度最高的测量查询时间的工具。
分析慢查询日志时，先使用pt-query-digest生成一个剖析报告，它能够提供日志的一个整体的概览，接下来再去定为精确所在之处也不迟。
![pt-query-digest](https://i.loli.net/2020/05/28/eIOhlKNU8JgpwX5.png)

#### 单条查询优化
* `show profile`：服务器上执行的所有语句都会测量器耗费的时间
* `show status`：返回全局计数器，也有某个会话级别的计数器。显示某些活动的频繁程度。
* 慢查询日志
* `performance schema`：5.5之后会变得很强大，待考证。

#### 如何诊断间歇性问题
使用`show global status`/`show process list`/`查询日志`确定是单条查询还是服务器问题

## schema与数据类型的优化
### 优化的数据类型
#### 原则
* 更小的通常更好，但要确保没有低估值的范围
* 简单就好，整型比字符操作代价更低
* 尽量避免NULL值。但通常把可为NULL的列改为NOT NULL对性能提升不是很大，如果计划对该列上建立索引，那么最好不要设置NULL

在选择数据类型是，第一步确定合适的大类型（数字、字符串、时间等），然后选择具体的类型。

#### 整数类型
`TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT`、`BIGINT`，也可以无符号，将上限提升一倍

分别是8,16,24,32,64为存储空间 $-2^{N-1}\leq x \leq 2^{N-1}$

#### 实数类型

`DECIMAL`类型可以指定精度，对于`DECIMAL`列可以指定小数点前后所允许的最大位数。`DECIMAL`(18,9)小数点两边各存储9个数字，一共使用9个字节，一个小数点，4个整数部分、4个小数部分。

`FLOAT`使用4个字节，`DOUBLE`使用8个字节。MySQL使用`DOUBLE`作为内部浮点计算的类型。

#### 字符串类型

`VARCHAR`、`CHAR`实现形式与数据库引擎有关。

`VARCHAR`用于存储可变长字符串，比定长更加节省空间，但update时可能需要做额外开销。`VARCHAR`需要使用1-2个额外字节记录字符的长度。

合适的情况：
* 列最大长度比平均长度大很多
* 列更新很少
* 使用了复杂的字符集UTF-8

`CHAR`适合存储很短的字符串，或者所有值接近同一个长度，并且删除所有的末尾空格。例如MD5值，因为定长的MD5值不容易产生碎片。

`BLOB`和`TEXT`，BLOB二进制、TEXT字符形式，BLOB和SMALLBLOB同义词，TEXT和SMALLTEXT同义词。当他们太大时，InnoDB会使用专门的存储区域来存储，在行内需要1-4个字节存储一个指针。

需要尽量避免使用这两种类型。

**使用枚举代替字符串**
* 存储非常紧凑，会根据列表值的数量压缩到1-2个字节中。将每个值在列表中的位置保存为整数，并且在frm的文件中保存数字-字符串映射关系的查找表。
* 内部排序按照枚举的存储顺序排序的。
* 字符列表是固定的，添加、删除都是需要使用`ALTER TABLE`的

#### 日期和时间类型

`DATETIME`：1001-9999精度为秒，YYYYMMDDHHMMSS

`TIMESTAMP`：1970-2038 秒数，比datetime的效率更高。

#### 位数据类型
`BIT`：存储一个或者多个True False的值（0，1的字符串）

如果存储一个`b'00111001'`的值，数字上下文的场景中，会导致返回57，而不是ascii码为57的字符‘9’，因此要谨慎使用BIT。
![屏幕快照 2020-05-30 下午4.59.25.png](https://i.loli.net/2020/05/30/l9qKUinm5ypakY6.png)

`SET`：一种代价相对来说较高的表示位的形式，一些列打包的bit位。但是改变列的代价相对较高，且无法在set列上通过索引查找。

#### 如何选定标识符

整数通常是最好的选择 很快，并且可以使用auto_increment

enum和set糟糕的选择

尽量避免使用字符作为标识列，当使用uuid值时，最好把“-”删除，按照数字的方式存储，还是不如递增的整数好用

一些ORM系统是常见的性能噩梦，存储的类型比较任意。

### schema设计中的陷阱

* 太多的列：行缓冲中将编码过的列转换成行数据结构的操作代价是非常高的
* 太多的关联：单个查询最好在12个表以内做关联
* 全能的枚举：0，1，2，3
* 变相的枚举
* 非此发明的NULL：不要刻意不用NULL

### 范式和反范式

范式化的数据库中，每个事实数据都会出现并且仅出现一次。
反范式化的数据库中，信息是冗余的可能存储在多个地方。

#### 范式的优点和缺点
优点：

* 更新操作比反范式操作快
* 当数据较好的范式化时，就只有很少或者没有重复数据，所以执行操作会更快
* 范式化的表通常更小，可以更好的放在内存里，执行操作会更快
* 很少有多余的数据意味着检索列表时更少需要DISTINCT或者GROUP BY
  
缺点：
通常需要关联，代价昂贵，使得一些索引策略无效

#### 反范式的优点和缺点
优点：
* 避免关联
* 进行更有效的索引策略

#### 实际应用中经常是混用范式化和反范式化
最常见的反范式化数据的方法是复制或者缓存，在不同的表中存储相同的特定列，可以使用触发器更新缓存值。

### 缓存表和汇总表
缓存表表示存储哪些可以比较简答的从schema中其他表中获取数据的表（获取的速度比较慢）
汇总表表示的是使用GROUP BY语句聚合数据的表

有时候可以在缓存表上使用别的数据库引擎，例如支持更小索引占用空间、全文搜索的MyISAM。

使用缓存表和汇总表时，要决定是定时维护还是定期重建数据。

重建汇总表和缓存表时，通常需要保证数据在操作时仍然可用，通过‘影子表’实现。

更快的读，更慢的写。

### 如何加快ALTER TABLE
有一些情况不需要重建表
* 移除一个列的AUTO_INCREMENT
* 增加、删除和更改enum set常量

技术是为想要的表结构创建一个新的.frm：
1. 创建一张有相同结构的空表，进行所需要的修改
2. FLUSH TABLES WITH READ LOCK，关闭所有表。
3. 交换frm文件
4. UNLOCK TABLES


## 创建高性能的索引
### 什么是索引
#### B-tree索引
* 支持全值匹配
* 匹配最左前缀
* 匹配列前缀
* 匹配范围值
* 匹配一列到另一列
* 只访问索引
#### 哈希索引
只有memory引擎支持hash索引
只能进行全值匹配
#### 空间数据索引
#### 全文索引
### 索引好在哪里
* 减少服务器需要扫描的数据量
* 帮助服务器避免排序和临时表
* 将随机IO变为顺序IO
  
注：当表比较小时，全表查找比索引可能更快，中大型表用索引，大型表建立索引开销比较大，可以采用分区索引。

### 高性能的索引策略
#### 选择独立的列
要求查询时，索引列既不能是表达式的一部分，也不能是函数的参数
#### 前缀索引
索引的选择性：不重复的索引值和数据表记录总数的比值，$[\frac{1}{n},n]$。
能够是索引更小更快的有效办法，无法进行orderby和groupby
#### 选择合适的索引策略
如果一个索引对应的数据条数更小，那么它应该放在更前面
#### 聚簇索引
数据放在叶子结点，索引在树节点上
InnoDB通过主键聚集数据，如果没有主键，则使用非空索引，如果没有索引，隐式定义一个主键作为聚簇索引。
#### 覆盖索引
索引中已经包含要查询的字段的值
如果视同like来做匹配，那么就不能进行覆盖索引
#### 使用索引扫描来做排序
* 只有当索引的列顺序和orderby的子句顺序完全一致并且所有列的排序方向都一样时，MySQL才能够使用索引来对结果做排序
* 关联操作时，只有子句引用字段全部为第一个表时，才能够使用索引做排序

满足最左前缀的要求
#### 压缩索引
MyISAM使用前缀压缩来减少索引的大小，从而让更多索引可以放入内存中。不过某些操作会更慢
```
ex:perform,performance
output:7,ance
```

#### 冗余和重复索引
mysql的唯一约束和主键都是索引实现的。多个在同一个字段上，就是重复索引。
`(A,B)`和`(A)`，后者就是冗余索引，`(B,A)`不是冗余索引

#### 未使用索引
建议删除

#### 索引和锁
Inno只有在访问行的时候才会对其加锁，而索引能够减少InnoDB访问的行数，减少锁的数量。

### 维护索引和表
通过`CHECK TABLE`来检查是否发生了表损坏，`REPAIRE TABLE`来修复表。如果不可以，可以使用`ALTER TABLE innodb_tb1 ENGINE=INNODB;`

## 查询性能优化
查询优化、索引优化、库表结构必须要齐头并进
### 查询速度慢的原因
查询的生命周期：从客户端、到服务器，服务器解析、生成执行计划，执行、返回结果给客户端。执行是生命周期中最重要的阶段，也是最耗时的步骤。
在执行中，查询需要在不同的地方花费时间，包括网络、cpu计算、生成统计信息和执行计划、锁等待操作等。

### 访问的数据太多：优化数据访问
分析是否试一下的两个问题：

#### 是不是在检索超多数据，访问了太多的行，或者是列
* 查询了不需要的数据，比如客户端只需要前十行
* 多标关联时返回了全部列
* 总是取出全部列（一些DBA是禁止`select * `写法的）
* 重复查询相同的数据


#### 是不是在扫描额外的记录
* 估计响应时间（我不太会）
* 查看扫描的行数。能够说明该查询的效率
* 查看访问类型。`EXPLAIN`语句中的`type`能够反映访问类型，访问类型的种类为全表扫描、索引扫描、范围扫描、唯一索引扫描、常数引用等。速度由快到慢。

Explain可以获取关于查询执行计划的信息、以及如何解释输出。P687

一般MySQL能够使用如下方式应用WHERE条件（由好到坏）：
* 在索引中使用WHERE条件来过滤不匹配的记录，在存储引擎层完成。
* 使用索引覆盖扫描（Extra：Using Index）来返回记录，从索引中过滤不需要的记录并返回命中的结果，无需回表查询。
* 从数据表中返回数据，过滤不满足条件的记录。

一些技巧：
* 使用索引覆盖扫描，将需要的列都放在索引中
* 改变库表结构。例如使用单独的汇总表
* 充血复杂查询，让MySQL优化器能够以更加优化的方式执行这个查询

### 重构查询的方式
一个重要的问题：是否要将一个复杂的查询分成多个简单的查询。

#### 切分查询：分而治之的思路
隔一段时间删除10000条数据，一般来说是一个比较高效且对服务器影响也最小的办法。大大减少的删除时锁的持有时间。

#### 分解关联查询
有一些好处：
* 让缓存的效率更高，应用程序可以方便的缓存单表查询对应的结果对象
* 分解后单个查询减少锁的竞争
* 在应用层做关联
* 查询本身的效率也有所提升
* 减少冗余记录的查询
* 在应用中实现哈希关联而不是MySQL的嵌套关联

很多场景下通过重构查询将关联放到应用程序中将会更加的高效。

### 查询是怎么执行的？
![屏幕快照 2020-06-23 下午8.41.41.png](https://i.loli.net/2020/06/23/JSaHPiVThptQOzv.png)
1. 客户端发送查询
2. 检查缓存，命中立即返回
3. SQL解析、预处理，优化器生成对应的执行计划
4. 根据执行计划点用存储引擎的API进行查询
5. 返回给客户端

#### MySQL客户端/服务器端通信协议
半双工、没有办法进行流量控制。
查询状态：Sleep、Query、Locked、Analyzing and statistics、Copying to tmp table、Sorting result、Sending data

#### 查询缓存（名词）
如果查询缓存打开，MySQL优先检查这个查询是否命中。通过一个对于大小写敏感的哈希查找。命中后检查用户权限，如果权限够，不会解析查询，不生成执行计划，也不会执行。

#### 查询优化
1. 语法解析成语法树
2. 语法树被认为合法，转换成执行计划。一条查询可以有多种执行方式，都返回相同的结果，优化器作用就是找到其中最好的执行计划。
3. 通过因子的值来计算操作的代价

选择错误执行计划的原因：
* 统计信息不准确
* 估算可能不准确
* 此最优非彼最优，并不是最快的（听不懂）
* 不考虑并发执行的查询
* 不考虑存储过程和自定义函数
* 无法估算所有可能的执行计划

能够处理的优化类型（不是全部，但足以证明其复杂性和智能性）
* 重新定义关联表顺序
* 外链接转化成内链接
* 使用等价变换规则
* 优化COUNT、MIN、MAX
* 预估并转化为常数表达式
* 覆盖索引扫描
* 子查询优化
* 提前终止查询（使用了Limit、发现了不成立的条件）
* 等值传播
* 列表in的比较（MySQL使用IN时，先排序，在二分法进行判断）

MySQL是如何进行关联查询的？
* 每一次查询都是一次关联。
* MYSQL对于任何关联都执行嵌套关联操作。先从一个表里循环取出单条数据，在嵌套循环到下一个表中寻找匹配的行。
* 可以使用STRAIGHT——JOIN关键词重写查询，让优化器按照你个爹关联顺序执行，但是人的判断一般没有机器精确。

如何进行排序优化？
应该尽可能比秒排序或者尽可能避免对大量数据进行排序。
当需要排序的数据小雨排序缓冲区，使用内存进行快速排序。如果内存不够，则分块进行快排，再进行合并排序。

两种排序算法：
* 读取行指针和排序字段，排序之后更具结果读取数据行
* 读取所有列，根据列进行排序，返回排序结果

在关联查询时，如果需要进行排序，有两种情况：
* 如果order by来自第一个关联表，那么他在关联第一个表的时候就进行关联排序。`Extra：Using filesort`
* 此外所有情况，都先将关联的数据放在临时表中再进行排序。`Extra:Using temporary;Using filesort`如果还有limit，则将在排序之后执行limit

#### 查询执行引擎
MySQL只是简单地根据执行计划给出的指令逐步执行。过程中，大量的操作通过调用存储引擎借口来完成，‘Handler API’，查询中的每一个表都是由一个handler实例表示，在优化阶段就已经为每个表都创造了handler实例。

#### 返回结果给客户端
如果查询可以被缓存，那么MySQL在这个阶段也会将结果放置到查询缓存中。返回解雇哦好死一个增量、逐步返回的过程，服务器端无需存储太多的结果，也不会耗费太多内存。

### 查询优化器的局限性
#### 关联子查询可能有一些问题
关联子查询的性能比较差，但是不一定是最差的写法，有时候关联子查询是一种非常合理、自然甚至性能最好的写法。有时候EXSIT比关联更快。

#### UNION的限制
无法将限制条件从外层下推到内层，使得原本能够限制部分返回结果的条件无法应用到内层查询中，比如说对于整体查询进行limit,需要在内部也应用limit性能才更加的好。

#### 等值传递的问题
会增加一些意想不到的额外消耗。对于`IN()`列表，mysql优化器发现存在where on、 using 的子句使得这个列表的值和另一个表相关联。

#### 无法并行执行查询
如题，不支持。其他的关系型数据库支持。

#### 哈希关联
MySQL8 才支持哈希关联。

#### 松散索引扫描
MySQL不支持松散索引扫描，无法按照不连续的方式扫描一个索引。
什么是松散索引扫描？
![rqGsnvJYaAPe96E](https://i.loli.net/2020/06/24/rqGsnvJYaAPe96E.png)

#### 最大值和最小值优化
对于min()和max()，Mysql的优化做的不是很好。
```SQL
SELECT MIN(id) FROM actor WHERE first_name = "adfgg";
```
因为在`first_name`没有索引，因此会做一次全表扫描，如果它能够进行主键扫描，理论上当读到第一个满足条件的记录的时候，就是找的最小值，因为id字段是严格按照大小排序的。可以直接通过使用`LIMIT 1`来进行优化重写。

#### 同时查询和更新。
不允许对于同一张表同时进行查询和更新。


### 使用优化器提示来改变执行计划
可以查看官方有关Hint的要求。

### 优化特定类型的查询
#### 优化count()
MyISAM的count在没有where 条件的时候非常的快。
可以通过增加汇总表和外部缓存系统来优化count扫描。

#### 优化关联查询
* 确保`On`或者`Using` 子句中的列上有索引
* 确保`group by`和`order by`中的表达式只涉及一个表中的列，这样才能够使用索引来优化这个过程
* 注意版本

#### 优化子查询
尽可能用关联查询代替

#### 优化GROUP BY 和 DISTINCT
两者可以相互转化
将超级聚合`with rollup`这个做法放到应用层面

#### 优化limit
`limit 10000,20 `会将前面的都丢弃，代价非常高。
此时使用索引覆盖扫描，而不是查询所有的列，效率提升非常大。
同时可以记录上一次的条数，然后下一页查询从记录的开始，不管翻页到什么情况下，性能都很好。ß

#### 优化SQL_CALC_FOUND_ROWS
在`limit`语句中加入这个hint可以获得去掉limit以后满足条件的行数，作为分页的总数，这个情况下 进行了一次全表扫描，然后丢弃了不需要的，开销很大。

通过设计下一页按钮显示特定数量的记录。
或者缓存较多的数据。


#### 优化union查询
将`where` `limit` `order by`的条件放到内层可以使优化器对于这些条ß件进行优化。
如果不是一定要消除重复的行，那就一定要使用`UNION ALL`这样才不会对于整个临时表的数据做唯一性检查，这样的代价非常高。

#### 在使用自定义的变量时的局限
* 使用自定义变量，无法使用查询缓存
* 不能在使用常量或标示符的地方使用变量
* 只在一个连接中有效
* 在持久连接中会产生共享bug
* 5.0之前大小写敏感
* 不能显式声明自定义变量的类型，确定未定义的变量的具体类型的时机各个版本可能都不一样。是动态类型
* 优化器可能会把变量优化掉，会出现错误
* 赋值的顺序和时间并不是总是固定的
* 赋值符号`:=`优先级非常低，赋值表达式要用明确的括号
* 使用未定义的变量不会产生任何语法错误，非常容易犯错


## 新的特性
### 分区表

分区要做的事情就是以代价非常小的方式定位数据在哪一片。

分区的主要目的是将数据按照较粗的粒度放在不同的表中。相关的数据可以存放在一起，批量删除整个分区也更加的方便。
适用的场景：
* 表非常大，无法全部放在内存中；热点数据在表最后
* 更容易维护。适合批量删除和独立优化
* 分区表的数据可以分布在不同的物理设备上
* 避免特殊竞争，比如InnoDB的单个索引的互斥访问、ext3文件系统的inode锁竞争等
* 备份和恢复独立分区

一些限制：
* 一个表最多只能有1024个分区
* 分区表达式必须是整数、或者是返回整数的表达式
* 分区字段中有主键或者唯一索引的列，那么所有主键列和唯一索引都必须包含进来

#### 分区表原理
进行增删查改操作之前，分区层都会先打开并锁住所有底层表。

如果存储引擎能够自己实现行级锁，InnoDB就会在分区层释放对应表所，这个加和解锁的过程与普通InnoDB上的查询类似。

#### 分区表的类型
* 根据范围进行分区（如图）
* 根据键值分区，减少InnoDB的互斥量竞争
* 根据哈希分区
* 根据列表分区
* 根据函数分区，mod7返回周几，HASH(id DIV 1000000)

![partition by range](https://i.loli.net/2020/06/25/URsloHTqm3QOFkA.png)

#### 如何使用分区表
当数据量超大的时候，B-Tree索引就没有办法起作用了。除非是索引覆盖查询，否则数据库服务器需要根据索引扫描的结果回表，查询所有符合条件的记录，如果数据量极大，将产生大量随机I/O，数据库的响应时间将大到不可接受。此外，索引维护的代价非常的高。

保证大数据量可扩展性的两个策略：
* 全量扫描数据，不要任何索引
* 索引数据，分离热点，热点数据所在的分区可以使用缓存、使用索引

#### 分区表的问题
* NULL值会使分区过滤无效。当为NULL或者非法值的时候他们会被分到底一个分区。当第一个分区很大的时候，第一种策略代价非常大。5.5以后可以使用列来进行分区，而不是函数，改变了这个情况。
* 分区列和索引列不匹配会导致无法进行分区过滤（？P264）
* 选择分区的成本可能会很高
* 打开并锁住底层表的成本可能很高
* 维护分区成本可能很高（新增和删除分区很快，但是重组分区很慢，需要临时表参与其中）
* 分区存储引擎要一致
* 分区函数和表达式有限制
* 某些存储引擎不支持分区
* MyISAM不能使用LOAD INDEX INTO CACHE操作
  
#### 使用分区进行查询优化
在where条件中带入分区列，看似多余也要带上。而且要和分区函数中列的数据类型一样。

Explain Partition 可以观察是否执行了分区过滤。partition分区会显示他所要查询的分区。

#### 合并表
一种过时的技术。代表一个容器，里面包含了多个真实的表。

删除合并表不会对子表产生影响。删除子表的影响视操作系统而定。

### 视图
虚拟表，不存放任何的数据。视图和表在同一个命名空间。

MySQL支持嵌套定义视图。视图操作中，有合并算法和临时表算法。其中如果视图中包括GROUP BY、DISTINCT、任何聚合函数、UNION、子查询等只要无法在原表记录和视图记录中建立一一映射的场景中，都会用临时表算法生成视图。当然也可以强行使用临时表算法生成一个临时表。
![53bUmzCEMJw41tH](https://i.loli.net/2020/06/25/53bUmzCEMJw41tH.png)

#### 可更新视图
可以通过更新这个视图来更新视图涉及的相关表。如果视图定义中包含了GROUP BY、任何聚合函数、UNION或者其他的一些特殊情况，就不能被更新。任何使用临时表算法的视图都无法被更新。

#### 视图对性能的影响
* 重构schema可以使用视图确保应用代码不报错
* 实现基于列的权限控制
* 使用临时表算法实现的视图，在某些时候性能会很差
* 如果打算使用视图，需要比较详细的测试，不能盲目使用

#### 视图的限制
* 不支持物化视图（结果存放在表中，定期从原始表获取数据）
* 不支持视图中的索引
* 不会保存视图定义的原始SQL语句

### 外键约束
InnoDB是目前MySQL中唯一支持外键的内置存储引擎。

使用外键的成本在于每次修改数据时都要在另一张表中多执行一次查找操作。

如果想确保两个相关表始终有一致的数据，那么使用外键比在应用程序中检查一致性的性能要高得多。

如果只是使用外键做约束，那么通常在应用程序中实现约束会更好。

### 在MySQL内部存储代码（性能方面）
形式：触发器、存储过程、函数

优点：
* 服务器内部执行，节省带宽和网络延迟
* 代码重用
* 简化代码的维护和版本更新
* 提升安全、提供细粒度的权限控制
* 缓存存储过程的执行计划，大大降低功耗
* 存储过程维护工作很简单
* 各司其职可以交给数据库专家

缺点：
* 不太好调试
* 难以实现复杂逻辑
* 带来额外复杂性
* 安全性会被一举攻破
* 给数据库服务器增加额外的压力
* 无法控制存储过程的资源消耗
* 存储代码功能比较弱
* 和二进制日志合作的并不好

很多短小的查询使用存储过程代替可以减少网络通信开销、解析开销和优化器开销因而更加快速。

#### 触发器
MySQL中：
* 对于每一个表的每一个事件，只能定义一个触发器
* MySQL只支持**基于行的触发（最大性能限制）**，如果变更数据集很大，效率会很低。

对于触发器本身，有以下限制：
* 掩盖背后的工作
* 问题很难排查
* 可能导致死锁和锁等待

在实现约束、系统维护任务、更新反范式数据、记录数据变更日志时适合用触发器。

#### 事件
定时任务。可以在informatuob_schema.events中看到各个事件的状态。

### 游标

游标指向的对象都是存储在临时表而不是实际查询到的数据，总是只读的。

**案例没听懂P284**

### 绑定变量

![Xolyf8dDMisCptG](https://i.loli.net/2020/06/25/Xolyf8dDMisCptG.png)

这种做法高效的原因：
* 服务端只需要解析一次SQL语句
* 某些优化器只需要执行一次，因为它会缓存一部分执行计划
* 以二进制只发送参数和句柄，效率高、减少网络开销
* 仅仅参数，网络开销少
* 存储参数直接放到缓存中，不需要再内存中多次复制

优化方案：
![rxBGs3c9CtWQIli](https://i.loli.net/2020/06/26/rxBGs3c9CtWQIli.png)

限制：
* 会话级别，连接之间不能共用绑定变量句柄
* 5.1之前绑定变量SQL不能查询缓存
* 不是所有时候使用绑定变量都能收获更好的性能
* 老版本中不能再存储函数中使用绑定变量
* 如果忘记释放绑定变量资源，在服务器端很容易发生泄漏
* BEGIN无法再绑定变量中完成

三种不同的绑定变量：
* 客户端模式：接受一个带参数的sql并且将值带入其中，发送整段查询到服务器
* 服务端绑定：客户端使用特殊二进制协议将带参数的字符串发送到服务器端
* SQL接口的绑定：客户端将带参数的字符串到服务器端，然后发送设置参数的sql最后用EXECTUE来执行SQL，使用普通的文本传输协议。

### 用户自定义函数
UDF可以使用C语言点用约定的任何编程语言来实现。必须事先编译好并动态链接到服务器上，速度非常快，可以访问大量操作系统功能，使用大量库函数。

不过其中的任何一个错误都很可能使服务器直接崩溃，且UDF无法读写数据表。在使用时，要保证UDF是线程安全的。

### 插件
支持在MySQL中新增启动选项和状态值、新增INFORMATIOB_SCHEMA表或者在MySQL中执行后台任务。

### 字符集

服务有默认的字符集和校对规则，每个数据库、数据表也有自己的默认值。在整个体系结构上逐层继承，底层的决定所有。

#### 服务器和客户端通信中的字符集
![RXNZF5mLcVoy2ia](https://i.loli.net/2020/06/26/RXNZF5mLcVoy2ia.png)

#### 选择字符集和校对规则
使用`SHOW CHARACTERSET`和`SHOW COLLATION`来查看MySQL支持的字符集和校对规则。

#### 字符集等对于查询的影响
某些字符集和校对规则可能会需要更多CPU的操作，可能会消耗更多的内存和存储空间。在使用与服务器数据集不同的查询时，MySQL就需要使用文件排序。

### 全文索引
基于相似度的查询，而不是精确度的数值比较。
5.6以后InnoDB试验性的支持
MyISAM全文索引是一类特殊的B-TREE索引，共有两层，第一层是关键字，然后对于每个关键字的第二层，包含的是一组相关的文档指针。

#### 自然语言的全文索引
在整个索引中出现的越少，匹配时的相关度就越高。如果一个词语在50%以上的记录中都出现了，那么自然语言将不会搜索这类词语。所以很少的记录中建立全文索引，可能没有返回值。

全文索引语法和普通查询略有不同，可以根据`Where`子句中的`MATCH AGAINST`来区分查询是否使用全文索引。
![X72eRGzTa45BWki](https://i.loli.net/2020/06/26/X72eRGzTa45BWki.png)

与普通查询不同的是，这类查询自动按照相似度进行排序。在使用全文索引进行排序的时候，MySQL无法在使用索引排序，所以如果不想用文件排序的话，那么就不要再查询中使用`ORDER BY`子句。

#### 布尔全文索引
布尔搜索中，用户可以在查询中自定义某个被搜索词语的相关性。

![y2rqsuTxXJSYh7R](https://i.loli.net/2020/06/26/y2rqsuTxXJSYh7R.png)

如果搜索的关键词是不是太常见的词语，那么速度比like要快。

只有MyISAM才能够使用布尔全文索引，但并不是一定要有全文索引才能使用布尔全文搜索，当没有全文索引的时候，MySQL就通过扫描全表来实现。

#### 全文索引的限制
* 只通过词频来判断相关性
* 全文索引只有全部在内存中的时候性能才会非常好
* 增删改操作时，全文索引的操作代价很大
* 会影响查询优化器的工作：有全文索引就用全文索引、不能做覆盖扫描、只能做相关性排序、其他排序要用文件排序


![MNVeGidtIZzXfQw](https://i.loli.net/2020/06/26/MNVeGidtIZzXfQw.png)

#### 解决问题的小技巧
* 使用边框搜索
* 缓存全文索引返回的主键值
* 将MyISAM作为备库，并在这个上面建立相应的全文索引
* 抽样查询，分组返回前几名以解决产生大量随机I/O和大量结果的问题

### 分布式（XA）事务
将ACID扩展到数据库层面

#### 内部XA事务
存储引擎提交的同时，需要将提交的信息写入二进制日志，这就是一个分布式的事务。
会给MySQL带来巨大的性能下降，但是没办法。

#### 外部XA事务
一种在多个服务器之间同步数据的方法。在由于某些原因不能使用MySQL本身的复制或者性能不瓶颈的时候，可以尝试使用。

### 查询缓存
MySQL能够缓存完整的SELECT的查询结果。

很多时候应该还是默认关闭查询缓存

#### 如何判断命中
缓存放在引用表中，通过一个哈希值引用。哈希值包括查询、要查询的数据库、客户端协议版本等。判断命中，是通过客户端发送过来的其他原始信息。任何字符上的不同都会导致缓存不命中。

如果查询语句中包含任何的不确定函数，那么在查询缓存中是不可能找到缓存结果的。

查询缓存是加锁排他操作。

查询缓存之前，MySQL只做一件事情，就是通过大小写不敏感去检查SQL是不是以SEL开头。

查询缓存对于渡河写操作都会带来额外的消耗：
* 读查询在开始之前先检查是否命中缓存
* 如果缓存中没有，那么当完成执行后，会将这个查询结果写入查询缓存
* 当向某个表进行写入操作时，MySQL必须将对应表的所有缓存都设置失效，如果缓存大，操作就会带来很大的系统消耗

#### 如何使用内存
查询缓存完全存储在内存中。除了查询结果之外，还需要很多维护相关的数据。40k维护的数据结构大小。

MySQL自己管理一大块内存，而不是操作系统的内存管理。

1. 初始化查询缓存需要的内存
2. 有需要缓存内容时，从大空间申请一个数据块用于存储结果（>query_cache_min_res_unit）
3. 锁住空间块
4. 选择一个尽可能小的内存块，也可能是大的
5. 存入其中
6. 如果数据块全部用完，那将申请一个新的数据块
7. 释放剩余空间

![CSkNbFL1W47VeRU](https://i.loli.net/2020/06/26/CSkNbFL1W47VeRU.png)

但是实际操作情况下会产生碎片
![eq6LgJz4bjnX9Dh](https://i.loli.net/2020/06/26/eq6LgJz4bjnX9Dh.png)

#### 什么情况下查询缓存能够发挥作用
* 需要消耗大量资源的查询
* 涉及修改操作相比SELECT要非常少

指标：Qcache_hits和Qcache_inserts的比值。3:1有效、10:1优秀。如果没有达到，可以考虑禁用缓存。

#### InnoDB和查询缓存
事务是否可以访问查询缓存取决于当前的事务ID，以及对应数据表上是否有锁。每一个InnoDB表的内存数据字典都保存了一个事务ID，如果当前事务ID小于该事务ID，则无法访问查询缓存。所有加锁操作的事务都不使用任何查询缓存。

#### 通用缓存优化
* 多个小表代替一个大表对于查询缓存有好处
* 批量写入时只会引起一次缓存失效，因此比单条写入效果好
* 缓存空间大会导致过期操作时服务器僵死 可以相对减小或者禁用
* 无法在数据库和表级别控制查询缓存
* 写密集型应用应当禁用查询缓存
* 因为互斥信号量的竞争，直接关闭查询缓存对于读密集型应用也有好处

#### 查询缓存替代方案
客户端缓存

## 优化服务器设置

最好使用默认配置

### 配置机制
MySQL从命令行参数和配置文件中获取配置信息。

文件一般在：`/etc/my.cnf`、`/etc/mysql/my.cnf`

![XIyPtJdS5HFmOBC](https://i.loli.net/2020/06/26/XIyPtJdS5HFmOBC.png)

#### 设置变量的副作用
小心那些可以在线改动的设置，他们可能导致数据库做大量的工作

#### 如何设置变量
如果设置的值太高，会导致问题，可能会由于内存不足导致服务器内存交换或者超过地址空间。

### 如何配置MySQL文件
![5XvfMryejSBc3gG](https://i.loli.net/2020/06/26/5XvfMryejSBc3gG.png)

`/var/lib/mysql`存放数据，`pid`文件也要放在相同的位置（也可以放在`/var/run`下）

应当明确指明`socket`和`pid`文件的位置

#### 如何设置InnoDB缓冲区大小
![THuByGUlCdt1r95](https://i.loli.net/2020/06/26/THuByGUlCdt1r95.png)

即使是保守的缓冲池设置也是服务器内存的87.5%。

### 如何配置内存
包含**可以控制的内存**和**不可以控制的内存**

#### 确定可以使用的内存上限
32位linux内核通常限制任意进程课使用的内存在2.5G-2.7G之间

#### 确定每个连接需要的内存
MySQ保持一个连接（线程）只需要少量的内存。要了解在高峰期MySQL将消耗多少内存非常有价值。

#### 确定操作系统要多少内存
如果没有虚拟内存正在与磁盘交换，说明内存足够。预留内存的5%。不要太多，因为操作系统通常会利用所有剩下的内存做文件系统缓存。

#### 确定缓存需要的内存
最重要的缓存：
* InnoDB缓存池
* InnoDB日志文件
* MyISAM键缓存
* 查询缓存
* 无法手工配置的缓存，二进制日志等

##### InnoDB缓存池
用于缓存索引、行的数据、自适应哈希索引、插入缓冲、锁和其他内部数据结构、延迟写入和合并写入。

大缓冲池预热和关闭都会花费很多时间。重启预热缓冲池可能需要几天，可以使用别的工具让他缩短到几分钟，这个功能对于复制尤其有好处。

##### MyISAM的键缓冲
只缓存索引、不缓存数据（依赖操作系统缓存数据）

##### 线程缓存
保存了那些当前没有与连接关联但是准备为后面心的连接服务的线程。

没有必要把线程缓存设置的非常大，设置的很小也不能节省太多内存。每个缓存的线程通常使用256KB左右的内存

##### 表缓存
每个在缓存中的对象包含相关表.frm文件的解析结果，加上一些其他数据。其他数据依赖具体的存储引擎。

InnoDB不依赖表缓存。使用这个纯属历史遗留问题。

##### InnoDB数据字典

当InnoDB打开了一张表，相当于增加一个对应的对象到数据字典。大表很多很多时才有影响，可以移除其中没有使用的表。

第一次表打开时会需要很多I/O操作统计信息。

### 如何配置MySQL的I/O行为
一些操作影响MySQL怎样同步数据到磁盘中以及如何进行恢复操作。这些操作对于性能的影响非常大，因为都设计昂贵的I/O操作，也表现了性能和数据安全之间的权衡。保证数据立刻并一致地写入到磁盘是很昂贵的。

![5lo6pmP9rUFytGZ](https://i.loli.net/2020/06/26/5lo6pmP9rUFytGZ.png)

#### InnoDB事务日志
InnoDB使用日志把随机I/O变成顺序I/O，一旦日志写到磁盘，事务就持久化了。

在InnoDB变更任何数据时，会写一条变更记录到内存日志缓冲区，在缓冲满、事务提交、或者每一秒钟，InnoDB都会刷写缓冲区内容到磁盘日志文件。日志缓冲区推荐范围是1MB-8MB。

“把日志缓冲写到日志文件”和“把日志刷新到持久化存储”是有差别的，大部分操作系统中，前者只是将数据从InnoDB缓存转移到了操作系统缓存，并没有进行时持久化。

#### InnoDB表空间
InnoDB把数据保存在表空间内，本质上是一个有一个或多个磁盘文件组成的虚拟文件系统。包含存储表、索引、回滚日志、插入缓冲、双写缓冲等功能。

如果设置了自动扩展的功能，回收空间的唯一方式就是导出数据、关闭MySQL删除文件、修改配置、重启、创建新的数据文件、导入数据。

不能随意移动文件。如果表空间损坏，InnoDB会拒绝启动，对日志文件也一样的严格。

没有必要把InnoDB文件放在传统的文件系统上

**双写缓冲**：避免也页没有写完整导致的数据损坏。在一些连续的块中足够保存100个页。当InnoDB从缓冲池刷新页面到磁盘中时，首先将他们写到双写缓冲，再将他们写入到数据区域中，以保证每个页面的写入都是原子并且持久化的。

InnoDB知道什么页是损坏的，因为每个页面最后都有校验值。如果双写缓冲不对，那么就到原始位置读取这个页面。一些文件系统ZFS会做一样的事，没必要让InnoDB做一遍，可以关闭。

#### MyISAM配置

P361

### 配置MySQL并发
InnoDB有自己的线程调度器控制线程怎么进入内核访问数据，以及他们在内核中一次可以做哪些事。最基本限制并发的方式是使用innodb_thread_concurrency变量。0表示不限制。但是最好的方案是升级服务器。

理论上 并发值 = CPU数量\*磁盘数量\*2

还可以使用线程池来限制并发。

MyISAM并发配置：P365

### 基于工作负载的配置
优化BLOB和TEXT的场景 P368
当需要对BLOB和TEXT进行排序时，只会使用前缀。

## 操作系统和硬件优化
性能受制于最薄弱的环节，承载它的操作系统和硬件往往是限制因素。

### CPU和I/O
当数据放在内存中能够很快读取时，CPU会出现瓶颈。

当所需要的数据远远超过有效内存容量时，会出现I/O瓶颈。如果应用程序分布在网络上，有大量查询和低延时的要求，那么瓶颈可能转移到网络上。

### 如何选择CPU
当遇到CPU密集型的工作时，MySQL通常可以从更快的CPU中获益（相对更多的CPU来说）

调优服务器一般有两个目标：**低延时**和**高吞吐**。

当查询在等待锁，通常需要更快的CPU；如果在运行队列中等待，那么更多和更快的CPU都可能有帮助。

多CPU在联机事务处理场景中非常有用，Web应用属于这一类。

### 平衡内存和磁盘资源
配置大量内存的原因不是因为可以在内存中保存大量数据，而是避免磁盘I/O。

数据库服务器同时使用顺序和随机I/O，随机I/O从缓存中受益良多。

顺序读取不能从缓存中受益因为比随机读快：因为顺序I/O比随机I/O快（1:5000）、存储引擎执行顺序读比随机读快。

增加内存是解决随机I/O读取问题最好的办法。

缓存可以完成所有读操作，并且集中操作写。通过多次写入、一次刷新和I/O合并的方法集中操作写。

评估缓存命中率最好方法是查看CPU命中率，如果使用99%的时间工作而用了1%的时间等待I/O，那缓存命中率还是不错的。**最好的内存/磁盘比例取决于系统中最薄弱的组件**

传统磁盘读取数据的过程分为三步：
* 移动读取磁头到磁盘表面上的正常位置
* 等待磁盘旋转，所有所需的数据在读取磁头之下
* 等待磁盘旋转过去，所有所需的数据都被读取磁头读出

### 固态存储
pros：
* 相比硬盘有更好的随机读写性能
* 相比硬盘有更好的顺序读写性能
* 相比硬盘能更好的支持并发。

最重要的特征是可以迅速完成多次小单位的存取，但是写入更有挑战性。

有两种单元类型：**单层单元SLC**和**多层单元MLC**。

单层单元的每个单元存储数据的一个bit。昂贵但是非常快，寿命长（100000次擦写）

多层单元每个单元存储2个、3个bit。成本更低，速度和耐擦写性也下降（10000写循环周期）

制造一个好的MLC设备比制造一个SLC设备难得多，但也是可能的。

PCIe的互联带宽更大，能满足高性能的需求，但是太贵了。

在数个TB下MySQL很难良好的工作，MySQL需要对数据进行拆分、横向扩展和无共享架构。

**Flashcache**可以在内存和磁盘之间创造了一个中间层，创建了一个块设备，能够创建文件系统，其中闪存设备缓冲写入和缓存读取。

### 为备库选择硬件
很多人选择主备使用相同的硬件，或者为主库购买新的硬件然后让备库使用主库淘汰的老硬件。当备库的硬件很难跟上主库时，使用固态硬盘能够提升随机I/O能力。

### 有关RAID
存储引擎通常把数据和索引都保存在一个大文件中，这意味着RAID存储大量数据是最可行的方法。
* RAID 0：成本最低、性能最高、不可恢复，可以轻易克隆备库
* RAID 1：很好的读性能，适合存放日志，只有两块硬盘有需要冗余的低端服务器的选择
* RAID 5：随机写很昂贵（底层磁盘的两次读和两次写）、一个损坏可重建、随机读、顺序读都不错、10块硬盘以后就不能很好的扩展
* RAID 10：读写都不错、重建也简单，失去磁盘时性能下降明显
* RAID 50：条带化RAID 5，主要用于存放非常庞大的数据集，数据仓库和OLTP系统

![zULbdKp2XPxNy7I](https://i.loli.net/2020/06/28/zULbdKp2XPxNy7I.png)

不要低估磁盘同时发生故障的可能性（SSD盘同时损坏的可能性是很大的），RAID不能消除甚至减少备份的需求。因此对于RAID的监控非常重要。

### SAN和NAS
访问SAN设备时通过块接口，服务器可以直接看到一块硬盘。通过光纤通道协议和iSCSI连接服务器。

NAS设备通过基于文件的协议来访问，NFS和SMB。使用网络连接。

SAN不适合随机读取。把MySQL放在SAN上读写会产生很严重的性能问题。

执行大量随机I/O的单线程任务不适合，拷贝、二进制日志以及InnoDB事务日志都需要很好的小随机I/O性能。

![hdUjo65r4fXim2L](https://i.loli.net/2020/06/28/hdUjo65r4fXim2L.png)

### 多磁盘卷
* 使用多个卷可以帮助解决I/O负载高的问题
* 把事务日志和数据文件放在不同的卷上，这样日志的顺序I/O和随机I/O不会互相影响，同时能够减少事故中同时丢失数据和日志文件的可能性。但如果RAID控制器上没有支持电池的写缓存，可以让两者分开。

![wJCxF5vK3MdUIQR](https://i.loli.net/2020/06/28/wJCxF5vK3MdUIQR.png)

### 操作系统
最常用类Unix

### 文件系统
windows只有NTFS
![ivPwZ9p1NHI5mon](https://i.loli.net/2020/06/28/ivPwZ9p1NHI5mon.png)

## 复制
复制解决的基本问题是仍一台服务器的数据与其他数据服务器保持同步。

两种复制方式：**基于行的复制**和**基于语句的复制**。

这两种方式都是通过在主库上记录二进制日志、在备库重放日志的方式来实现异步的数据复制。在某一时刻可能存在主备库数据不一致的情况。

MySQL的复制是向后兼容的，新版本的服务器可以作为老版本服务器的备库，反之不行。同时通过将读操作指向备库可以获得更高的读拓展，但是对于写操作，除非设计得当，否则并不是很通过复制来扩展写操作。在一主多备的架构中，写操作会被执行数次，这时候整个系统的性能取决于写入最慢的那个部分。

### 复制能够解决的问题
* 解决数据分布的问题
* 读操作分布多个服务器实现负载均衡
* 数据备份
* 高可用和故障切换
* MySQL升级测试

### 复制的原理
1. 主库更改数据记录到二进制日志
2. 备库将日志复制到自己的中继日志上（Relay log）
3. 备库读取中继日志的事件，将其重放到备库数据之上

![k2Lxz56urcRp3MG](https://i.loli.net/2020/06/28/k2Lxz56urcRp3MG.png)

MySQL会按照提交事务的顺序来记录二进制日志而不是每条语句的执行顺序。

获取事件和重放事件两个过程异步进行，I/O线程能够独立于SQL线程之外工作。因为只有一个SQL线程来重放中继日志中的事件，所以主库上并发运行的查询在备库中只能串行化运行。

#### 基于语句的复制
好处是实现相当的简单、坏处是其中一些函数的值可能稍有不同、且必须串行，需要更多的锁。

可以通过修改备库，提升称为主库，允许更灵活的操作

如果老版本中使用触发器和存储过程，就不要使用基于语句的复制方式

#### 基于行的复制
将实际数据记录在二进制日志中。可以正确的复制每一行，且更加高效。

占用更少的cpu，能够更快的找到和解决数据不一致的情况

无法判断执行了哪些SQL。

#### 涉及的文件
* mysql-bin.index 日志索引文件。文件中的每一行包含了二进制文件的文件名
* mysql-relay-bin-index 中继日志的索引文件
* master.info 保存备库连接到主库所需要的信息
* relay-log.info 当前备库复制二进制日志和中继日志的坐标

#### 备库的备库
log-slave-updates可以让备库变成其他服务器的主库，默认情况下这个选显示打开的
![iYNBM2z4CptcJGk](https://i.loli.net/2020/06/28/iYNBM2z4CptcJGk.png)

其中给每一个服务器分配的服务器ID是解决复制过程中的无限循环问题的。
![6B2YaHhwZN8qA9S](https://i.loli.net/2020/06/28/6B2YaHhwZN8qA9S.png)

### 如何进行配置？
1. 在每台服务器上创建复制账号
2. 配置主库和从库
3. 通知备库连接到主库并从主库复制数据

#### 创建账号
在主库创建账号![HgGTCvX3uwW61YE](https://i.loli.net/2020/06/28/HgGTCvX3uwW61YE.png)

复制账号事实上只需要在主库上的Replication slave权限，并不一定要每一端都有Replication client权限？**为什么**
![qzTZO76Y41R9swU](https://i.loli.net/2020/06/28/qzTZO76Y41R9swU.png)

#### 配置主库和从库
主库的配置：
![9AHP58ZUc16vp3i](https://i.loli.net/2020/06/28/9AHP58ZUc16vp3i.png)

从库的配置：

![v5DA2PhHsYfETFl](https://i.loli.net/2020/06/28/v5DA2PhHsYfETFl.png)


#### 启动复制
告诉备库如何连接到主库并重放其二进制日志。
![X9jZIUml7iMB25p](https://i.loli.net/2020/06/28/X9jZIUml7iMB25p.png)

pos被设置为0，因为要从日志的开头开始读起。
然后可以用过`show slave status`来查看复制是否正确执行。之后运行`start slave`命令开始复制。之后I/O线程和SQL线程就会开始运行。`show processlist`可以看到两个复制线程的状态。

如果备库是刚刚增加的，需要有三个条件来让主库和备库保持同步：
* 在某个时间点主库的数据快照
* 主库当前的二进制日志和数据快照在二进制文件中的偏移量
* 从快照时间到现在的二进制日志

其他方法：
* 冷备份：关闭主库，把数据复制到备库，重启主库后，使用新的二进制日志文件，然后通过在备库执行change master to指向这个文件的起始处
* 热备份：如果只使用了MyISAM表，可以在主库运行时使用`mysqlhotcooy`或者`rsync`来复制数据
* 使用`mysqldump`来转储主库数据并将其加载到备库，然后设置相应的二进制日志坐标![mvgUrkjzDi1xMWF](https://i.loli.net/2020/06/28/mvgUrkjzDi1xMWF.png)
* 使用快照或备份
* 使用别的热备份工具，不能使用`show master status`来获得主库的二进制坐标位置，而是在获取快照的时候使用`show slave status`来获取备库在主库上的执行位置。

#### 关于复制的推荐设置
* 在主库上二进制日志最重要的选项时sync_binlog=1
* 备库上推荐开启如下配置![fupqTtmJOW4jG1o](https://i.loli.net/2020/06/28/fupqTtmJOW4jG1o.png)relay_log可以避免中继日志文件机遇机器名来命名。skip_slave_start能够组织备库在崩溃后自动启动复制，导致数据不一致。read-only可以组织大部分用户更改非临时表，除了复制线程和超级权限的用户之外，也要尽量避免给正常账号授予超级权限。

### 复制拓扑结构

基本原则：
* 一个MySQL备库实例一定只能有一个主库
* 每个备库必须有一个唯一的服务器ID
* 一个主库可以有多个备库
* 如果打开了log_slave_updates，一个备库可以将主库上的数据变化传播到其他备库

#### 一主多从
备库之间没有交互，仅仅是连接到同一个主库上。

可以把读分摊到多个备库上，直到备库给主库造成太大的负担，或者主备之间的带宽成为瓶颈

它有以下一些用途：
* 为不同的角色使用不同的备库
* 把一台备库当作待用的主库，用作灾难恢复
* 把一台备库放到远程数据中心，以备灾难恢复
* 延迟一个获多个备库，以备灾难恢复
* 使用其中一个备库作为备份、培训、开发或者测试使用服务器

#### 主主模式下的主主复制
两台服务器都被配置为对方的主库和备库

很麻烦！
![FiglvSI1G3XUdr6](https://i.loli.net/2020/06/29/FiglvSI1G3XUdr6.png)

#### 主被模式下的主主复制
其中一台服务器是只读的被动服务器。使得反复切换主动和被动服务器非常方便。
![Ww5DI1fHXAPhYFQ](https://i.loli.net/2020/06/29/Ww5DI1fHXAPhYFQ.png)

类似创建了一个热备份。并且能够用它来执行读操作、备份、离线维护和升级。但通常不会获得比单台服务器更好的写性能。

#### 有备库的主主结构
增加了冗余
![4JetSnGUkdqEaLM](https://i.loli.net/2020/06/29/4JetSnGUkdqEaLM.png)

#### 环形复制

非常脆弱，应当避免。
![XcTYIwL45aOjWnt](https://i.loli.net/2020/06/29/XcTYIwL45aOjWnt.png)

#### 使用分发主库
每个备库不会共享binlog dump资源，给主库增加负担。

将主库移除负载并使用分发主库。分发主库也是 一个备库，唯一目的就是提供主库的二进制日志。为避免在分发主库上做实际的查询，可以将它的表修改为blackhole存储引擎。

![yDe4H3VRUPnQjFa](https://i.loli.net/2020/06/29/yDe4H3VRUPnQjFa.png)

也可使用多个分发主库。分发主库也可以对二进制日志事件执行过滤和重写操作。

问题在于无法使用备库来代替主库，因为由于分发主库的存在，每个备库和原始主库的二进制日志坐标已经不相同。

#### 树形结构
减轻主库负担，但是中间层出现任何错误都会影响到多个服务器。
![2wZT3B9no4APUlh](https://i.loli.net/2020/06/29/2wZT3B9no4APUlh.png)

#### 其他定制
选择性复制：在主库上将数据划分到不同的数据库里
分离功能：将短查询事务和慢查询事务分离到不同硬件支持的库上
数据归档：在备库上保留主库上删除过的数据，不传递delete
备库全文索引
只读备库：禁止大部分写操作，除了复制进程和拥有超级权限的用户
多主库复制：？
创建日志服务器：容易重放和过滤二进制日志事件

### 容量规划
当为单位台主库增加备库时。将很快达到投入远高于回报的地步，查询和库数量增加不是线性关系。

同时，复制只能扩展读操作，根本不能扩展写操作。

普遍的问题是：备库何时跟不上主库？得看复制的速度

构建一个大型应用时，有意让服务器不被充分利用，能够更好的处理负载尖峰。

### 如何维护复制

#### 监控复制
主库上使用`show master status`来查看当前主库的二进制日志位置

#### 测量备库延迟
show slave status输出seconds_behind_master列理论上显示了备库的延时，但是并不总是准确的。可以通过hearbeat record来监控，这是一个在主库上每秒更新一次的时间戳，延时可以使用备库当前的时间戳减去心跳记录的值。

#### 确定主备一致
理论上两者应该是一样的，但是备库可能发生错误并且导致数据不一样，即使没有明显的错误，MySQL同样可能因为自身的特性导致不一致，比如MySQL中的bug、网络中断、服务器崩溃、非正常关闭或者其他一些错误。

检查主备以一致应当是日常的工作，有现成的工具，checksum。

#### 从主库重新同步备库

传统方法是关闭备库，然后从主库重新复制一份数据，然后从备份中恢复备库。

最简单的办法是使用mysqldump转储受影响的数据并重新导入。在过程中如果数据没有发生变化，方法会很好。还可以用工具。

#### 改变主库
计划内的操作，在备库中使用`change master to`改变指定的值。备库将抛弃之前的配置和中继日志，并且从新的主库开始复制。最困难的是获取新主库上合适的二进制日志位置，这样备库才能够从和老主库相同的逻辑位置开始复制。

将备库提升为主库则要更难一点。

如果是**计划内的提升**
1. 停止当前所有主库上的写操作，最好将所有客户端程序关闭
2. flush tables with read lock在主库上停止活跃的写入（可选）
3. 选择一个备库作为新的主库，确保他已经完全跟上主库
4. 确保新主库和旧主库数据一致性
5. 新主库stop slave
6. 新主库执行change master to master_host=''，然后reset slave，断开与老主库的连接，并丢弃master.info的信息
7. show master status记录新主库的二进制日志坐标
8. 确保其他备库已经追赶上
9. 关闭旧主库
10. 激活新主库事件
11. 客户端连接到新主库
12. 每台备库change master to，使用之前通过show master status获得的二进制日志坐标来指向新的主库

如果是**计划外的提升**
如果只有一台备库，直接使用它。如果有超过一台，就需要做一些额外的工作。
步骤中需要确保在计算中使用`master_log_file`和`read_master_log_pos`的值。
1. 确定哪台备库的数据为最新。使用`show slave status`选择`master_log_file`和`read_master_log_pos`最新的那个
2. 让所有备库执行完所有从旧主库那里获得的中继日志（如果未完成就修改主库，他会抛弃剩下的日志事件，而无法获知该备库在什么地方停止）
3. 前一小节5-7
4. 比较每台备库和新主库上的`master_log_file`和`read_master_log_pos`
5. 前一小节10-12

#### 主主复制交换角色
1. 停止主动服务器上的所有写入
2. 主动服务器上执行read_only=1，配置文件里面页设置read_only以防重启失效
3. 主动服务器获取二进制日志坐标
4. 使用刚刚的坐标在被动服务器上执行select master_pos_wait()将语句阻塞住，直到复制跟上主动服务器
5. 被动服务器执行set gGLOBAL read_only=0变成主动服务器
6. 写入主动服务器

### 复制的问题及其解决
**重要**P477

### 复制的速度

理论上非常快，仅受限与网络速度

### 其他特性
半同步复制，帮助你确保备库拥有主库数据的拷贝，减少了潜在数据丢失的风险。
在提交过程汇总给你增加了一个延迟：当提交事务时，在客户端接收到的查询结束反馈前必须保证二进制日志已经传输到了至少一台备库上。（只有客户端通知被延迟，事务提交不会延迟；备库接收到事务立即发送反馈而不是执行后；如果一直没有回应，超时转化为正常异步复制模式）

半同步复制相比在主库哈桑执行强持久化的性能有两倍的改善。

5.5之后复制心跳能确保备库一直和主库相联系。

## 可扩展性
可扩展性依赖多个条件。能够通过增加资源来提升容量的能力。
USL模型揭示了构建高可用扩展性系统的重要原则：**在系统内尽量避免串行化和交互**。

**向上扩展**：升级硬件

**向外扩展**：复制、拆分、数据分片

其中一个节点就是一个功能部件。

可以按照**功能**拆分数据节点。**其实不太可取。**
![Ga9hF7BybO1uVgt](https://i.loli.net/2020/06/29/Ga9hF7BybO1uVgt.png)

**数据分片**是最通用和成功的办法。
采用分片的应用常有一个数据库访问抽象层，用以降低应用和分片数据存储之间通信的复杂度，但无法完全隐藏分片。

分片不是必须的。

数据分片最大的挑战是查找和获取数据。需要为数据选择一个分区键。分区键决定了每一行分配到哪一个分片中、应该从哪一个分片中获取数据。最糟糕的情况是不知道数据在哪里，这时候就需要扫描所有的分片。

分片和节点不一定会死一对一的关系，应该尽可能让分破案的大小比节点容量小很多，这样就可以在单个节点上存储多个分片。保持分片足够小更加容易管理、转移和执行查询。

在节点上部署数据分片时，有以下一些操作：
* 每个分片应当使用单一的、名字相同的数据库。
* 多个分片到同一张表中，表名包含分片号。
* 每个分片使用一个数据库，在数据库中包含所有应用需要的表，数据库名包含分片号（广泛）
* 每个分片使用一个数据库，数据库名和表名都包含分片号
* 每个节点上运行多个MySQL实例，每个实例上又一个或者多个分片


数据分配到分片有两种方法：**固定分配**和**动态分配**。

固定分配的分区函数仅仅依赖于分区键值，哈希函数和mod运算就是很好的例子。
**缺点**是：分片很大数量不多的情况下很难平衡负载、无法自定义放置的分片、修改分片策略比较困难

所以更加倾向于动态分配的方法。建立一张包含分区键和分片ID的表。给定分区键，就可以获得分片号，如果该行不存在，就从目标分片中找到并将其加入到表中。

![WG3RYJHkF5PaTzV](https://i.loli.net/2020/06/30/WG3RYJHkF5PaTzV.png)


这种条件下，可以直接向USERS表中增加一个分片ID。P521？400:3不太懂。

还可以混合动态和固定分配。

### 如何生成一个全局唯一的ID？
* auto_increment_increment和auto_increment_offset，每个自增值不同
* 全局节点中创建表
* 使用memcached incr()自动增长一个数字并返回结果/redis
* 批量分配数字
* 使用复合值 数+分片ID
* GUID，不过基于语句的复制时不能正确复制。

如果使用全局分配器来产生唯一ID，要注意避免单点竞争成为应用的性能瓶颈。


此外，数据分片应用的抽象层应当完成以下任务：
* 连接到正确的分片并且执行查询
* 分布式一致性检验
* 跨分片结果集聚合
* 跨分片关联操作
* 锁和事务管理
* 创建新的数据分片并且重新平衡分片

### MySQL如何通过集群扩展
P526

### MySQL通过归档文件来向内扩展
P530

### 负载均衡

很多层次上的高可用和可扩展性问题都需要使用负载均衡的思路


#### 常见的读写分离的方法
* 基于查询分离，不能忍受脏数据的查备库
* 基于脏数据分离，前面的一点点小改进
* 基于会话分离，不看到其他用户的更新，只看到自己的更新
* 基于版本分离，跟踪对象版本
* 基于版本分离和基于会话分离的变种

也可以同你过修改DNS和修改应用的配置来分发负载。

**这是应用和MySQL直接项链的情况**

#### 引入中间件分发负载
有许多负载均衡的硬件和软件，但是很少有专门为MySQL服务器设计的。

负载均衡算法：
* 随机
* 轮询
* 最少连接
* IP哈希
* 权重


## 高可用
### 宕机的原因
* 磁盘空间耗尽
* 运行很糟糕的SQL
* 糟糕的Schema和索引设计
* DROP TABLE

### 如何实现高可用性
#### 提升平均失效时间
![AOQLh1ljEo9g4a8](https://i.loli.net/2020/06/30/AOQLh1ljEo9g4a8.png)

#### 降低平均恢复时间
just experience

### 如何避免单点失效
* 共享存储和磁盘，SAN、DRDB
* MySQL同步复制，只有在至少一个备库上提交后才能认为其执行完成
* 使用复制管理器

### 故障转移和故障恢复
* 提升备库、切换主被动角色
* 转移虚拟IP
* 中间件转发连接
* 让应用来处理

## 备份
建议：
* 大数据库进行物理备份，小数据库，逻辑备份可以很好地胜任
* 保留多个备份集
* 定期从逻辑备份中抽取数据进行恢复测试
* 保存二进制日志以用于基于故障时间点的恢复
* 不借助备份工具来监控备份和备份的过程
* 经常演练
* 考虑安全性

关闭MySQL做备份是最简单安全的。flush tables with read lock操作会使MySQL关闭并锁住所有的表，时间会不可预估。大系统不要用这个操作

### 逻辑备份和物理备份
**逻辑备份**：导出、复制原始文件的物理备份
pros：
* 可以编辑
* 恢复很简单
* 可以通过网络备份恢复
* 非常灵活
* 与存储引擎无关
* 有助于避免数据损坏

cons：
* 必须由数据库服务器完成生成逻辑备份的工作，占用cpu
* 逻辑备份在某些场景下比数据库文件本身更大
* 无法保证还原出来一定是同样的数据
* 从逻辑备份中还原需要MySQL加载和解释语句，**很慢**

物理备份：
pros：
* 复制文件就好
* MyISAM复制到目的地就行、InnoDB需要其他一些步骤
* 从物理备份中恢复会更快

cons：
* InnoDB的原始文件比逻辑备份要大得多
* 物理备份不总是可以跨平台


尽量不要完全依赖物理备份，至少每隔一段时间还是做一次逻辑备份。

### 备份的内容
最基本的是备份数据和表定义

其他：
* 二进制日志和InnoDB事务日志
* 触发器和存储过程
* 复制配置：中继日志、日志索引文件和info文件
* 服务器配置
* 选定的操作系统文件

增量备份的建议：
* 使用其他工具进行增量备份
* 备份二进制日志
* 不要备份没有改变的表
* 不要备份没有改变的行
* 某些数据不需要备份
* 备份数据发送到去重特性的目的地，例如ZFS文件管理程序

有两种导出方法：SQL导出和符号分隔文件。

#### 文件系统快照
文件系统快照也是一种非常好的在线备份方法，支持快照的文件系统能够瞬间创建用来备份的内容一致的镜像。

LVM使用写时复制技术来创建快照。可以对一个很大的卷做快照

![M3GtXyHxLfrUJz5](https://i.loli.net/2020/06/30/M3GtXyHxLfrUJz5.png)

#### 从备份中恢复
1. 停止MySQL服务器
2. 记录服务器的配置和文件权限
3. 将数据从备份中移到MySQL数据目录
4. 改变配置
5. 改变文件权限
6. 一限制访问模式重启服务器，等待完成启动
7. 载入逻辑备份文件
8. 检查和重放二进制日志
9. 检测已经还原的数据
10. 已完全权限重启数据库

可以从物理备份中恢复、从逻辑备份中恢复、基于二进制日志的时间点恢复等。P624

## 工具
Nagios、Zabbix、Zenoss

---END---

**datagridview** 

2020.06.30
