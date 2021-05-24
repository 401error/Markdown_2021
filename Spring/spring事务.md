事务创建方式

一.编程式事务创建，使用TransactionTemplate或者TransactionManager这两个类进行实现事务的执行和回滚。

```
@Autowired
private TransactionTemplate transactionTemplate;
public void testTransaction() {

        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {

                try {

                    // ....  业务代码
                } catch (Exception e){
                    //回滚
                    transactionStatus.setRollbackOnly();
                }

            }
        });
}
```



二.声明书事务

@Transaction

1. `@Transactional` 注解只有作用到 public 方法上事务才生效，不推荐在接口上使用；
2. 避免同一个类中调用 `@Transactional` 注解的方法，这样会导致事务失效；
3. 正确的设置 `@Transactional` 的 rollbackFor 和 propagation 属性，否则事务可能会回滚失败





三.事务的属性

 隔离级别

* **`TransactionDefinition.ISOLATION_DEFAULT`** :使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 Oracle 默认采用的 `READ_COMMITTED` 隔离级别.
* **`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`** :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**
* **`TransactionDefinition.ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**
* **`TransactionDefinition.ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**
* **`TransactionDefinition.ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

传播方式

* ```
      int PROPAGATION_REQUIRED = 0;//内部方法和外部方法均属于同一事务
      int PROPAGATION_REQUIRES_NEW = 3; //内部方法和外部方法相互独立
      int PROPAGATION_SUPPORTS = 1; //以非事务方式运行，如果当前存在事务，加入事务
      int PROPAGATION_MANDATORY = 2; //当前存在事务，加入，不存在则抛异常
      int PROPAGATION_NOT_SUPPORTED = 4;//以非事务方式运行，如果当前存在事务，则把当前事务挂起
      int PROPAGATION_NEVER = 5;//以非事务方式运行，如果当前存在事务，则抛出异常。
      int PROPAGATION_NESTED = 6; //外部为父事务，内部创建嵌套子事务，父事务回滚，子事务回滚。子事务回滚，父事务不管
  ```

超时时间

* 所谓事务超时，就是指一个事务所允许执行的最长时间，如果超过该时间限制但事务还没有完成，则自动回滚事务

回滚规则

* 当遇到何种异常，会回滚

