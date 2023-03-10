[TOC]

# 事务的基本特性和隔离级别有哪些？

事务：表示多个数据操作组成一个完整的事务单元，这个事务内的所有数据操作要么同时成功，要么同时失败。

事务的特性：ACID

1. 原子性：事务是不可分割的，要么完全成功，要么完全失败。
2. 一致性：事务无论是完成还是失败，都必须保持事务内操作的一致性。当失败时，都要对前面的操作进行回滚，不管中途是否成功。
3. 隔离性：当多个事务操作一个数据的时候，为防止数据损坏，需要将每个事务进行隔离，互相不干扰。
4. 持久性：事务开始就不会终止。他的结果不受其他外在因素的影响。

事务的隔离级别 SHOW VARIABLES like 'transaction%'

设置隔离级别：set transaction level xxx 设置下次事务的隔离级别

set session transaction level xxx 设置当前会话的事务隔离级别

set global transaction level xxx 设置全局事务隔离级别

MySQL当中有五种隔离级别

NONE：不使用事务

READ UNCOMMITED：允许脏读

READ COMMITED：防止脏读，最常用的隔离级别

REPEATABLE READ：防止脏读和不可重复读。MYSQL默认

SERIALIZABLE：事务串行，可以防止脏读、幻读，不可重复读。

物种隔离级别，级别越高，事务的安全性更高，但是，事务的并发性能也就会越低。



# MySQL的锁有哪些？什么是间隙锁？

从锁的粒度来区分

1. 行锁
2. 表锁
3. 全局锁













