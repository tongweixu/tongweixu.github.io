# tongweixu.github.io

interview

synchorized：
可重入性：死锁 state +1 -1。可见性：volatile 加速前后与主内存交互。抛异常：退出同步方法是否monitor。对象头：偏向锁，轻量级锁（自旋），重量级锁（挂起线程）
synchorized 静态方法：类对象锁；普通方法：对象锁。 与 reetrantlock 对比：条件，公平，可中断（个线程t1通过lockInterruptibly()方法获取到一个可重入锁，并执行一个长时间的任务，另一个线程通过interrupt()方法就可以立刻打断t1线程的执行），需手动释放。

volatile: 内存屏障（load读 store写，刷主内存，防重排序）可见性，i++ 读，写非原子性。

乐观锁cas,compare and swap 比对交换 旧值，新值，当前内存值，场景读多于写。
悲观锁，写多于读。

线程死锁排除：jconsole,jstack， jmap（内存中对象信息）
mysql 死锁排查：show engine innodb status

一致性hash: 2^32 环，服务器hash取模落入节点，key hash取模顺时针落入节点，数据倾斜（虚拟节点），新增删除节点，一个节点受影响。
redis clusetr：16384个槽，每个节点负责一部分槽范围， crc16（key） 取模，节点扩容或缩小容时，对数据槽进行重新分配迁移即可，数据不会丢失。
redis 哨兵 +  主从： 哨兵高可用，监听主节点存活情况（ping），切换主从。

redis 缓存穿透：不存在的key,布隆过滤器(bitmap，key多个hash函数，对应bitmap数组置为1，验证过程只要一个位置不为1，则肯定不存在，可以判断一定不存在，无法判断一定存在，redission实现)，直接存放null。
