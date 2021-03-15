# tongweixu.github.io

Interview

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
redis 雪崩：redis 宕机，大量key失效。限流，服务熔断降级，缓存高可用（hystrix熔断降级）。
redis 击穿：大量用户访问同一个key。热key永不过期，互斥锁查数据库。
redis 缓存一直性：1、更新db,更新缓存  2、更db，删缓存（缓存刚好失效，读比写慢,大部分实现） 3、延时双删（也会有问题）4、数据库binlog同步 
redis 热key问题：1、二级缓存，jvm hashmap（ehcache） 2、备份热key + 随机数，落到不同节点。
redis redlock 算法，单点故障。一半实例获取锁，ttl获取所有锁的时间，获取锁有时间限制，获取锁-开始时间要小于获取所有锁的时间。

Innodb: 分聚集索引，非聚集索引，非聚集索引子节点存放聚集随意主键；支持事务，支持外键，支持行锁，不支持全文索引。
Myisam:非聚集索引子节点指向数据文件记录。

tcp：
报文头：源端口，目的端口，序号，确认序号，校验和（奇偶校验），控制位（syn请求建立连接，fin关闭连接，ack确认控制），滑动窗口（流量控制），
       数据偏移：数据区在报文段中的起始偏移值
滑动窗口：1、数据可靠性：发送消息确认；2、流量控制：窗口大小随网络链路变化。
TCP中窗口大小是指tcp协议一次传输多少个数据。因为TCP是一个面向连接的可靠的传输协议，既然是可靠的就需要传输的数据进行确认。TCP窗口机制有两种，一种是固定窗口大小，另一种是滑动窗口。数据在传输时，TCP会对所有数据进行编号，发送方在发送过程中始终保持着一个窗口，只有落在发送窗口内的数据帧才允许被发送；同时接收方也始终保持着一个接收窗口，只有落在窗口内的数据才会被接收。这样通过改变发送窗口和接收窗口的大小就可以实现流量控制。

TCP的窗口滑动技术通过动态改变窗口的大小来调节两台主机之间数据传输。每个TCP/IP主机支持全双工数据传输，因此TCP有两个滑动窗口，一个用于接收数据，一个用于发送数据。接收方设备要求窗口大小为0时，表明接收方已经接收了全部数据，或者接收方应用程序没有时间读取数据，要求暂停发送。
TCP在传送数据时，第一次接受方窗口大小是由链路带宽决定的，但是接收方在接收到的数据后，返回ack确认报文，同时也告诉了发送方自己的窗口大小，此时发送方第二次发送数据时，会改变自己的窗口大小和接收方一致。
当窗口过大时，会导致不必要的数据来拥塞我们的链路，但是窗口太小时，会造成很大的延时，比如为1时，发送方每发送一个数据，接收方就会返回一个ack报文，在发送方未接收到接收方的确认报文ack之前不会进行下一次发送。（当链路变好了或者变差了这个窗口还会发生变化，并不是第一次协商好了以后就永远不变了。）
窗口滑动协议是TCP使用的一种流量控制方法。该协议允许发送方在停止并等待接收确认报文前可以连续发送多个分组。由于发送方不必每发一个分组就停下来等待确认，因此该协议可以加速数据的传输。只有在接收窗口向前滑动时（与此同时也发送了确认），发送窗口才有可能向前滑动。收发两端的窗口按照以上规律不断地向前滑动，因此这种协议又称为滑动窗口协议。

tcp保证可靠性：
1、可靠性连接；2、校验和 3、确认应答与序列号 4、超时重传 5、流量控制 6、拥塞控制。

tcp拥塞控制：
慢开始（类似指数增长），拥塞避免（类似线性增长），快重传（个别报文丢失，收到3个重复确认），快恢复（慢开始门限值做拥塞避免）

输入域名请求流程

拥塞窗口 cwnd是发送方维护的一个 的状态变量，它会根据网络的拥塞程度动态变化的。
我们在前面提到过发送窗口 swnd 和接收窗口 rwnd 是约等于的关系，那么由于入了拥塞窗口的概念后，此时发送窗口的值是swnd = min(cwnd, rwnd)，也就是拥塞窗口和接收窗口中的最小值。

tcp 三次握手（为什么三次握手，失效的报文重复建立连接），四次挥手（为什么四次，请求结束连接时部分数据还没发送完；2msl:2个最长报文存活的时间段，主动请求方1msl收到被动请求方重发报文）
存活在网络里的老的TCP报文可能与新TCP连接报文冲突。

https：超文本传输安全协议
TCP(控制传输协议)->SSL(加解密/身份验证,安全套接层)->http
浏览器如何保证证书的合法性：证书是否是信任的有效证书。浏览器从服务器拿到证书。证书上有服务器的公钥和CA机构打上的数字签名。拿到证书后验证其数字签名。具体就是，根据证书上写的CA签发机构，在浏览器内置的根证书里找到对应的公钥，用此公钥解开数字签名，得到摘要（digest,证书内容的hash值），据此验证证书的合法性。

输入域名请求流程：dns 解析，建立tcp,发送http请求，服务器响应http请求，断开tcp,前端渲染页面
dns 解析：浏览器缓存->操作系统hosts文件->本地域名服务器->根域名服务器->顶级域名服务器->二级域名....(迭代，也可转发递归，一层一层往上找）l

数据的传输流程就是封装和分用的过程；
（1）不同的协议层对数据包有不同的称谓,在传输层叫做段(segment)，在网络层叫做数据报 (datagram)，在链路层叫做帧(frame)；
（2）应用层数据通过协议栈发到网络上时,每层协议都要加上一个数据首部(header),称为封装 (Encapsulation)；
（3）首部信息中包含了一些类似于首部有多长, 载荷(payload)有多长, 上层协议是什么等信息；
（4）数据封装成帧后发到传输介质上,到达目的主机后每层协议再剥掉相应的首部, 根据首部中的 “上层协议 字段” 将数据交给对应的上层协议处理；

一个TCP报文最多传输多少字节(即MSS)？
答案：1440 字节
窗口大小 16位 65536字节

spring aop：面向切面的编程范式，切面切入，切面通知逻辑处理，jdk动态代理（代理接口），cglib(类与接口)
spring ioc：控制反转，将需要在代码中实现的对象的创建，对象的依赖关系反转给ioc容器实现，ioc容器通过xml,注解，配置类等方式完成对对象的创建和依赖关系的注入。
spring bean生命周期:实例化（依赖无参构造函数） -> 属性赋值 -> 初始化 -> 销毁
spring bean循环依赖：1、构造器循环依赖,srping 无法解决，抛出BeanCurrentlyInCreationException
                    2、【setter循环依赖】field属性的循环依赖【setter方式 单例，默认方式-->通过递归方法找出当前Bean所依赖的Bean，然后提前缓存【会放入Cach中】起来。通过提前暴露 -->暴露一个                          exposedObject用于返回提前暴露的Bean。】
                    3、autowired 反射注入。
spring bean 作用域：singleton 单例、prototype 每次从容器中获取创建新的、request 每次http请求创建新的bean,session,@scope
适配器模式：目标接口，被适配类，适配类
spring 中涉及的设计模式：单例（spring bean 作用域），工厂模式（ioc）, 代理（aop）,适配器模式（springmvc）
concurrenthashmap 1.7 segment 分段锁；1.8 没有hash冲突扩容cas + 冲突 synchronized + 线程去帮助扩容，红黑树。
hashmap 1.7 头插法 成环，hashmap 1.8 尾插法
hashcode hashcode ^ hashcode >>> 16  (n-1) & hashcode  

k8s: yaml(kind(depolyment)，镜像image, replicas副本数量，动态扩缩容（scale,replicas)
docker:from(基础镜像)，copy,add，maintainer（所有者），run, cmd

1. JVM栈 (Java Virtual Machine Stacks) 

 每当启动一个新线程的时候，java虚拟机都会为它分配一个java栈。java以栈帧为单位保存线程的运行状态。虚拟机只会对java栈执行两种操作：以栈帧为单位的压栈或者出栈。

每个线程包含一个栈区，栈中只保存基础数据类型的对象和自定义对象的引用(不是对象)，对象都存放在堆区中 。

每个栈中的数据(原始类型和对象引用)都是私有的，其他栈不能访问。 

栈分为3个部分：基本类型变量区、执行环境上下文、操作指令区(存放操作指令)。

2. 堆内存 (Heap Memory) 

存储的全部是对象，每个对象包含一个与之对应的class信息–class的目的是得到操作指令。

jvm只有一个堆区（heap）被所有线程共享，堆区中不存放基本类型和对象引用，只存放对象本身。  

堆的优势是可以动态地分配内存大小，生存期也不必事先告诉编译器，因为它是在运行时动态分配内存的，Java的垃圾收集器会自动收走这些不再使用的数据。 
