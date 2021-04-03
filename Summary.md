# Java 后端 Web开发

## 一、Java

### Java容器

### Java虚拟机

### 数据结构

### 锁和多线程

#### 线程池

### Spring

#### Spring IoC

来自<[BeanDefinition的资源定位过程.md](https://github.com/doocs/source-code-hunter/blob/master/docs/Spring/IoC/1、BeanDefinition的资源定位过程.md)>

**BeanDefinition资源定位（ refresh() 方法）**

1. 首先初始化 BeanFactory ，调用 obtainFreshBeanFactory() ，其中需要子类实现自定义的 refreshBeanFactory() 和 getBeanFactory() 方法，返回子类定义好的 BeanFactory 。此外，获取到 BeanFactory 后，需要对 BeanFactory 执行各种初始化操作，比如注册 BeanPost 事件处理器、是否启用 lazy_init 等。
2. refreshBeanFactory() 方法中，要对 BeanFactory 进行定制化，比如是否开启注解装配等。然后，调用 loadBeanDefinitions() 载入 BeanDefinition 。
3. 在 loadBeanDefinitions() 中，子类 AbstractXmlApplicationContext 生成了 BeanDefinitionReader 并用该 Reader 执行真正的 loadBeanDefinitions() 。
4. loadBeanDefinitions() 中会调用 getResouce() 获取用户传入的配置文件路径，得到 FileSystemResource 对象，后续将对该对象进行相关 I/O 操作，进行 BeanDefinition 的读取和载入。

**将Bean解析成BeanDefinition**

1. BeanDefinitionReader 会对配置文件中元素进行解析，类似于解析 Jsoup HTML 文档。
2. 将 <Bean> 元素交给 parseBeanDefinitionElement() 方法处理，并将该 Bean 解析封装为 BeanDefinitionHolder 对象。

**将BeanDefinitionHolder注册进IoC容器**

其实就是存入一个 ConcurrentHashMap<String, BeanDefinition> 中。

#### Dependency Injection（依赖注入）

**Spring 中触发 IoC容器“依赖注入” 的方式有两种：**

1. 应用程序通过 getBean()方法 向容器索要 bean 实例时触发依赖注入。
2. 提前给 bean 配置了 lazy-init 属性为 false，Spring 在 IoC 容器初始化会自动调用此 bean 的 getBean() 方法，提前完成依赖注入。

**BeanFactory 的 getBean()和 doGetBean()**

依赖注入从 getBean() 开始，该方法由 BeanFactory 接口提供。 getBean() 方法提供多个重载，最终还是调用 doGetBean() 。

- 在 doGetBean() 中：
  1. 根据 beanName 去容器 Map 中查找，如果找到并且该 bean 是单例模式，直接返回。
  2. 如果没有找到该 bean 或者该 bean 不是单例模式，而且当前 BeanFactory 的父容器不为 null ，则需要委派父容器去创建 bean 。
  3. 父容器为 null 的话，就开始获取该 bean 的依赖，然后递归地对该 bean 的依赖 bean 进行 getBean() 。
  4. 根据当前 bean 的生存周期执行不同的前置方法，最终还是调用 createBean() 方法创建 bean 。
- createBean() 和 doCreateBean()
  - doCreateBean() 中主要有 createBeanInstance() 和 populateBean() 两个方法，分别用于生成 bean 所包含的 Java 对象，以及处理 bean 对象之间的依赖关系。
  - 在 doCreateBean() 中，有 initiate() 方法真正实例化 bean 对象，采用了两种策略来实例化 bean 对象： Java 反射机制和 CGLIB 。
  - 当 bean 对象在 xml 配置文件中没有覆盖 look-up method 或者 replace method 的时候，使用 Java 反射机制获取 bean 的构造函数进行实例化。
  - 反之，则使用 CGLIB 来实例化对象。
- lazy-init
  - 通过牺牲 IoC 容器初始化的性能，来有效提升应用第一次获取该 bean 的效率。
  - 在 refresh() 函数中实现。

**FactoryBean 接口和 BeanFactory 接口的区别**

- FactoryBean
  
    FactoryBean 是一个特殊的 Bean 。可以通过实现 FactoryBean 接口实现向 IoC 容器注册两个 Bean，一个是 Bean 本身，一个是 FactoryBean.getObject() 返回值代表的 Bean 。
- BeanFactory
  
    BeanFactory 是 Spring  IOC 容器的核心接口，它定义了容器的主要功能，如创建 bean，获取 bean 等，是用来管理 bean 的。

#### Spring AOP

面向切面编程，指在程序运行中，将代码动态地切入到类的指定方法、位置上，不影响主业务代码。 Spring AOP 是基于动态代理的，即运行时织入。

**Advice 增强**

有 beforeAdvice 和 afterAdvice ，分别在 Pointcut 的前、后执行。

**PointCut 切面**

通过正则表达式或者匹配方法名匹配相应需要 Advice 增强的方法。

**AOP 原理**

在 AopProxyFactory 中会为目标代理对象 target 调用 createAopProxy() 创建 AopProxy 代理对象，主要依据相应 Bean 是否实现了 InvocationHandler 接口，如果实现则使用 JDK 代理的方式代理对象，否则使用 CGLIB 。

#### Spring 事务

<来自[spring-transaction](https://github.com/Snailclimb/JavaGuide/blob/master/docs/system-design/framework/spring/spring-transaction.md)>

理解ACID， MySQL 使用 Undolog 保证原子性。

##### 相关接口:

**PlatformTransactionManager**
（平台）事务管理器，Spring 事务策略的核心。

```java
public interface PlatformTransactionManager {
    //获得事务
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;
    //提交事务
    void commit(TransactionStatus var1) throws TransactionException;
    //回滚事务
    void rollback(TransactionStatus var1) throws TransactionException;
}
```

**TransactionDefinition**

事务定义信息(事务隔离级别、传播行为、超时、只读、回滚规则)。
定义了事务的传播行为、隔离级别、超时时间和是否为只读事务。

**TransactionStatus**

记录事务的运行状态。定义了一组方法,用来获取或判断事务的相应状态信息。

##### 传播行为:

**TransactionDefinition.PROPAGATION_REQUIRED**

默认的事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

```java
Class A {
    @Transactional(propagation=propagation.PROPAGATION_REQUIRED)
    public void aMethod {
        //do something
        B b = new B();
        b.bMethod();
    }
}
Class B {
    @Transactional(propagation=propagation.PROPAGATION_REQUIRED)
    public void bMethod {
       //do something
    }
}
```

只要其中一个方法回滚，整个事务均回滚。

**TransactionDefinition.PROPAGATION_REQUIRES_NEW**

- 不管外部方法是否开启事务，Propagation.REQUIRES_NEW 修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。
- 如果 aMethod() 发生异常回滚， bMethod() 不会跟着回滚，因为  bMethod() 开启了独立的事务。但是，如果  bMethod() 抛出了未被捕获的异常并且这个异常满足事务回滚规则的话， aMethod() 同样也会回滚，因为这个异常被  aMethod() 的事务管理机制检测到了。

**TransactionDefinition.PROPAGATION_NESTED**
如果 aMethod() 回滚的话，bMethod()和bMethod2()都要回滚，而bMethod()回滚的话，并不会造成 aMethod() 和bMethod()回滚。

##### 事务只读属性

- 如果给方法加上了Transactional注解的话，这个方法执行的所有sql会被放在一个事务中。
- 如果不加Transactional，每条sql会开启一个单独的事务，中间被其它事务改了数据，都会实时读取到最新值。
  1. 如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持 SQL 执行期间的读一致性；
  2. 如果你一次执行多条查询语句，例如统计查询，报表查询，在这种场景下，多条查询 SQL 必须保证整体的读一致性，否则，在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持

**Spring AOP 自调用问题**

若同一类中的其他没有 @Transactional 注解的方法内部调用有 @Transactional 注解的方法，有@Transactional 注解的方法的事务会失效。
这是由于Spring AOP代理的原因造成的，因为只有当 @Transactional 注解的方法在类以外被调用的时候，Spring 事务管理才生效。

```java
@Service
public class MyService {
    private void method1() {
        method2();
     //......
    }
    @Transactional
    public void method2() {
        //......
    }
}
```

MyService 类中的method1()调用method2()就会导致method2()的事务失效。

#### Spring MVC

DispatcherServlet

- DispatcherServlet 通过多层继承 HttpServlet ，使用 Servlet API 来对 HTTP 请求进行响应。
- 它会对 MVC 的其他模块进行初始化，比如 HandlerMapper 和 ViewResolver 等。具有自己的 IoC 容器。
- 将 HTTP 请求转发给 HandlerAdapter 返回 handler 对象，也即 Controller ，执行相应的业务代码后，返回 ModelAndView 对象。将 ModelAndView 对象转发给 ViewResolver 渲染 HTML 页面。

## 二、计算机操作系统

### 三、计算机网络

![计算机网络体系结构](images/network/计网体系结构.png)

### <font color="#69d">应用层</font>
应用层的作用是通过进程间交互来完成特定的网络应用
#### <font color="#99CC33">HTTP</font>

超文本传输协议（HTTP，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。所有的WWW文件都必须遵守这个标准。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。

##### 1、HTTP 1.0/1.1/2.0 的区别

    HTTP 1.0: 每个TCP连接只能发送一个请求，TCP连接

###### HTTP 1.0 -> 1.1

- 新增了24个错误1状态响应码

- Host头处理：IP+hostname，否则400 Bad request

- 带宽优化：支持断点续传；支持只发送header信息，如果服务器认为客户端有权限请求服务器，则返回100，否则返回401

- 长连接：默认开启Connection:keep-alive参数

- 缓存策略的控制

###### HTTP 1.1 -> 2.0

- 采用二进制格式而非文本格式

- 完全的多路复用

- 使用了报头(Header)压缩，降低了开销

- 让服务器可以将响应主动的推送到客户端缓存中(server push)

##### 2、https的请求过程

 ![img](../images/network/https.png)

 此处涉及一个问题是客户端如何验证证书是否有效，当客户端获取到签名证书之后，浏览器从签名证书中获取颁发者的信息，从颁发者的证书中找到CA证书（CA证书是所有电脑在操作系统安装时就已经装好），使用CA中心提供的公钥检验CA证书的有效性，然后再用CA证书中的公钥去验证的客户端获取到的签名证书的有效性。 

##### <font color="#99CC33"> 文件传输协议（FTP）：</font>

- FTP 是File TransferProtocol（文件传输协议）的英文简称，而中文简称为“文传协议”。用于Internet上的控制文件的双向传输。同时，它也是一个应用程序（Application）。
- 所有FTP应用程序都遵守同一种协议以传输文件。在FTP的使用当中，用户经常遇到两个概念："下载"（Download）和"上传"（Upload），用户可通过客户机程序向（从）远程主机上传（下载）文件。
- FTP的默认端口：控制端口一般为21，而数据端口不一定是20，这和FTP的应用模式有关，如果是主动模式，应该为20，如果为被动模式，由服务器端和客户端协商而定。

##### <font color="#99CC33"> 简单文件传输协议（TFTP）：</font>
    TFTP（Trivial File Transfer Protocol,简单文件传输协议）是TCP/IP协议族中的一个用来在客户机与服务器之间进行简单文件传输的协议，提供不复杂、开销不大的文件传输服务。端口号为69。

##### <font color="#99CC33"> 远程终端协议（TELNET）：</font>
    Telnet协议是TCP/IP协议族中的一员，是Internet远程登陆服务的标准协议和主要方式。它为用户提供了在本地计算机上完成远程主机工作的能力。
    在终端使用者的电脑上使用telnet程序，用它连接到服务器。终端使用者可以在telnet程序中输入命令，这些命令会在服务器上运行，就像直接在服务器的控制台上输入一样。
    可以在本地就能控制服务器。要开始一个telnet会话，必须输入用户名和密码来登录服务器。Telnet是常用的远程控制Web服务器的方法。

### <font color="#69d">传输层</font>

#### <font color="#99CC33">TCP</font>

#### <font color="#99CC33">UDP</font>

TCP与UDP区别总结：
1. TCP面向连接（如打电话要先拨号建立连接）;UDP是无连接的，即发送数据之前不需要建立连接
2. TCP提供可靠的服务，无差错，不丢失，不重复，且按序到达;UDP尽最大努力交付，即不保证可靠交付，没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低
3. 流模式（TCP）与数据报模式(UDP)
4. 每一条TCP连接只能是点到点的;UDP支持一对一，一对多，多对一和多对多的交互通信
5. TCP首部开销20字节;UDP的首部开销小，只有8个字节
6. TCP的逻辑通信信道是全双工的可靠信道，UDP则是不可靠信道

### 网络层

#### ARP协议

- ARP协议是地址解析协议（Address Resolution Protocol）是通过解析IP地址得到MAC地址。

- ARP缓存表，这个表记录着IP地址与MAC地址的映射关系

- ARP协议的主要工作就是建立、查询、更新、删除ARP表项。

### 链路层

### 物理层

## 四、数据库

### 关系型数据库（MySQL）

#### 一、三大范式

##### 第一范式： 列不可分

1NF（第一范式）是对属性具有**原子性**的要求，不可再分，例如：
如果认为最后一列还可以再分成出生年，出生月，出生日，则它就不满足第一范式的要求。
![avatar](images/database/1NF.png)

##### 第二范式： 消除非主属性对码的部分函数依赖

2NF（第二范式）是对记录有**唯一性**的要求，即实体的唯一性，不存在部分依赖，每一列与主键都相关，例如：
该表明显说明了两个事物：学生信息和课程信息；正常的依赖应该是：学分依赖课程号，姓名依赖学号，但这里存在非主键字段对码的部分依赖，即与主键不相关，不满足第二范式的要求。
![avatar](images/database/2NF.png)

##### 第三范式： 消除非主属性对码的传递函数依赖

3NF（第三范式）对字段有**冗余性**的要求，任何字段不能由其他字段派生出来，它要求字段没有冗余，即不存在依赖传递，例如：
![avatar](images/database/3NF.png)
很明显，学院电话是一个冗余字段，因为存在依赖传递：（学号）→（学生）→（学院）→（学院电话）

#### 二、SQL语句执行原理：

- 第一步：将SQL语句发送到服务端执行
- 第二步：语句解析
  1. 查询高速缓存(library cache)
  2. 语句合法性检查(data dict cache)
  3. 语言含义检查(data dict cache)
  4. 获得对象解析锁(control structer)
  5. 数据访问权限的核对
  6. 确定最佳执行计划 
- 第三步：语句执行
- 第四步：提取数据

##### SQL语句中的函数、关键字、排序等执行的顺序：

1. FROM 子句返回初始结果集。
2. WHERE 子句排除不满足搜索条件的行。
3. GROUP BY 子句将选定的行收集到 GROUP BY 子句中各个唯一值的组中。
4. 选择列表中指定的聚合函数可以计算各组的汇总值。
5. 此外,HAVING 子句排除不满足搜索条件的行。
6. 计算所有的表达式;
7. 使用 order by 对结果集进行排序。
8. 查找你要搜索的字段。

##### SQL语句执行完整过程：

1. 客户端提交SQL语句，发送到服务端。
2. 服务端接收消息，在PGA中分配此次进程所需内存，并存储会话相关信息。
3. 服务器将SQL语句转化为ASCII码，并进行hash函数后再查询高速缓存。
4. 如果不在高速缓存中，则进行SQL语义解析，包括对语法的解析和对表、索引、视图等对象的解析，再根据语句生成一个执行计划，并核对用户的执行权限，最后生成编译代码。
5. 将本条SQL语句的信息存储到高速缓存中。
6. 为已编译的执行代码准备预处理数据。
7. 判断所需数据是否存在DB Buffer中，若不是则执行编译代码。
8. 记录日志。
9. 为事务建立回滚段。
10. 改写DB Buffer块的数据内容。

#### 三、事务隔离

##### 1、事务的特性（ACID）

- 原子性（Atomicity）。事务中所涉及的程序对数据库的修改操作要么全部成功，要么全部失败。
- 一致性（Consistency）。事务执行前和执行后来源和去向保持平衡。
- 隔离性（Isolation）。并发时每个事务是隔离的，相互不影响。
- 持久性（Durubility）。一旦事务成功提交，应该保证数据的完整存在。

##### 2、事务并发

1. **脏读（Dirty Read）**
   
    一个事务读到了另一个未提交事务修改过的数据

2. **丢失修改（Lost to modify）**
   
    指在一个事务读取一个数据时，另外一个事务也访问了该数据，那么在第一个事 务中修改了这个数据后，第二个事务也修改了这个数据。这样第一个事务内的修改结果就被丢失

3. **不可重复读（Non-Repeatable Read）**

    在一个事务中多次读取同一个数据时，由于别的事务修改了这个数据，导致结果出现不一致。

4. **幻读（Phantom）**

    在一个事务中使用相同的查询条件两次读取，第二次读取到了其他事务新插入的行。

##### 3、事务的隔离级别

MySQL默认为Repeatable-read（可重复读），Oracle默认为Read-commited（读已提交）,Oracle只支持Read-committed和Serializable。

![4种隔离级别比较](images/database/事务隔离.png)

1. read uncommitted 读未提交

所有事务都可以看到没有提交事务的数据。

2. read committed 读已提交

事务成功提交后才可以被查询到。

3. repeatable 重复读

同一个事务多个实例读取数据时，可能将未提交的记录查询出来，而出现幻读。MySQL默认级别

4. Serializable可串行化

强制的进行排序，在每个读读数据行上添加共享锁。会导致大量超时现象和锁竞争。

#### 四、MySQL索引

**索引**是存储引擎快速找到记录的一种数据结构。

##### 索引类型

1. 主键索引 Primary Key
2. 唯一索引 Unique
3. 普通索引/组合索引 Index
4. 全文索引 Fulltext

##### 创建索引的技巧

1. 维度高的列创建索引。数据列中不重复值出现的个数即为维度。
2. 对 where,on,group by,order by 中出现的列使用索引。
3. 对较小的数据列使用索引，这样会使索引文件更小，同时内存中也可以装载更多的索引键。
4. 为较长的字符串使用前缀索引。
5. 不要过多创建索引，除了增加额外的磁盘空间外，对于DML操作的速度影响很大，因为其每增删改一次就得从新建立索引。
6. 使用组合索引，可以减少文件索引大小，在使用时速度要优于多个单列索引。

##### 索引优化原则

原则如下：

![索引优化原则](images/database/索引优化原则.png)

### 非关系型数据库（Redis）
