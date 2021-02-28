# JAVA基础
## 1、Java创建对象的4种方式
### 使用new关键字
### 使用clone方法
### 使用反序列化

    序列化对象必须实现Serializable这个接口
    把对象转为字节序列的过程称为对象的序列化
    把字节序列恢复为对象的过程称为对象的反序列化

### 使用反射机制
- 使用Class类的newInstance方法，这个newInstance方法调用无参的构造函数创建对象。
```java
//创建方法1
User user = (User)Class.forName("根路径.User").newInstance();　
//创建方法2（用这个最好）
User user = User.class.newInstance();
```
- 使用Constructor类的newInstance方法，可以通过这个newInstance方法调用有参数的和私有的构造函数。
```java
Constructor<User> constructor = User.class.getConstructor();
User user = constructor.newInstance();
```

------

## 2、何时使用抽象类，何时使用接口

- 抽象类的特性

  1. 使用abstract修饰，<font color = "red">`抽象方法只能是public，protected，不能是private（继承后无法被实现）`</font>
  2. <font color = "red">抽象类无法直接创建对象，</font>必须子类继承实现抽象方法之后才能创建对象
  3. 抽象类可以继承抽象类，子类必须复制继承父类抽象方法
  4. 就算是只含有一个抽象方法的类也必须定义为抽象类

- 接口类的特性

  1. 接口使用interface关键字修饰，<font color = "red">接口可以包含变量，成员变量会被隐式标记成public static final</font>
  2. 接口中的方法只能标记成public abstract，
  3. 一个类可以继承多个接口，但是必须实现所有接口方法

- 抽象类和接口类的区别

  1. 抽象类可以实现成员方法的实现细节，而接口中只能存在public stract
  2. 抽象类的成员变量可以是各种类型，但是接口类只能是public static final
  3. 接口方法不能含有静态代码块和静态方法

- <font color = "blue" size = "5">结论</font>

  例子，抽象类可以定义一个人的固有属性，口眼耳鼻等，接口类只要是定义那些不是人人都有的东西—>打球，游泳等

  1. 抽象类主要是定义某个领域的固定属性，也就是公共的部分，而接口定义的是这个领域的扩展
  2. 当需要为一些类提供公共模块的时候，优先考虑抽象类，这样被子类继承即可使用，代码简洁
  3. 当注重代码的扩展性和可维护性的时候应该优先考虑接口类型，接口是程序之间的协议，比抽象类的使用更加的安全 

------

## 3、为什么需要内部类

- 内部类的基本使用方法；内部类在使用情况上和基本的方法的使用情况差不多，

  静态内部不可以使用非静态内部类，非静态内部类可以使用任何类但是不能定义静态变量

  构造内部类：

  ```java
  OutterObject outterObject = new OutterObject(Constructor Parameters);
  InnerObject innerObject = outterObject.new InnerObject(Constructor Parameters);
  ```

  

- 内部类的优点：

  1. 当要实现的一个接口和类里面的一个方法名完全一致就可以使用内部类来进行解决
  2. 可以通过内部类和接口来解决Java无法进行多继承的问题
  3. 提高封装性
  4. 一个内部类可以访问创建它的外部类的变量，甚至是私有变量，这就为我们设计程序提供了更多的思路
  5. <font color ="red">未完待续.........</font>

------

**常见运算符（&、~、<<、>>、|、^）及经典例子**

- &（与运算）针对二进制，只要有一个为0就为0；

  例：4 & 5 = 100 & 101 = 100 = 4；

- ~（取反运算）为单目运算符，对参与运算的数按位进行取反，

  例：~1 = ~000000001 = 11111110 = 0；

- << （向左位移）转换成二进制之后向左平移n个位置，后面用0补齐；

  例：2<<3 = 10 << 3 = 10<font color = green>000</font> = 16;

- (>>) (向右平移）转换成二进制之后向右平移n个位置，被移除的全部丢弃

  例：15 >> 2 = 1111 >> 2 = 11 =  3;

- | （或运算）只要在按位运算的过程中出现有一个1就运算出结果是1

  例：9 | 7 = 1001 | 111 = 1111 = 15；

- ^  (异或运算) 只有按位运算的过程中两个数是相异的时候为1，否为为0；

  例：9 ^ 7 = 1001 ^ 111 = 1110 = 14；

1. 判断一个数是不是2的次方：

   ```java
   
   		if(((n-1)&n)==0){
   			System.out.println("是2的整数次方");
   		}else{
   			System.out.println("不是2的整数次方");
   		}
   ```

   

2. <font color = "red">待更新</font>

------

## 4、JAVA中普通io和nio的区别

**a. 普通io**

​	Java中的通信是通过socket来完成的，其中Socket分为ServerSocket和普通的Socket。

​	服务端的构建过程：

​	服务端根据场景的要求通过5种创建ServerSocket中的一种方法ServerSocket方法，调用ServerSocket中的accept方法启动监听，但是accept()方法是阻塞的（当程序调用accept之后，整个程序只有在接受有请求接入的时候程序才会往下走），当accept接受到请求之后会返回一个socket，具体流程如下程序注释

```java
public class MyServerSocket {
    public static void main(String[] args) {
        try {
            //创建ServerSocket实例
            ServerSocket serverSocket = new ServerSocket(8080);
            //调用accept方法等待请求
            Socket socket = serverSocket.accept();
            //接受到请求之后，通过BufferedReader获取socket里面的数据
            BufferedReader is = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String line = is.readLine();
            System.out.println("客户端发送过来的数据："+line);
            //创建printWriter对象用于往socket里面写入数据
            PrintWriter printWriter = new PrintWriter(socket.getOutputStream());
            printWriter.print("已接受客户端传来的数据："+line);

            printWriter.flush();
            //关闭资源
            printWriter.close();
            is.close();
            socket.close();
            serverSocket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

​	客户端构建过程

​	客户端通过主机地址和端口新建一个socket对象，通过printWriter和BufferedReader对socket进行写入和读取的操作，具体过程如下：

```java
public class MyClientSocket {
    public static void main(String[] args) {
        String msg = "客户端数据";
        try {
            //新建Socket对象，使用主机地址和端口连接到服务器
            Socket socket = new Socket("127.0.0.1",8080);

            //先向服务器发送请求，创建PrintWriter用于写入数据
            PrintWriter printWriter = new PrintWriter(socket.getOutputStream());

            //读取服务端传回的数据
            BufferedReader is = new BufferedReader(new InputStreamReader(socket.getInputStream()));

            //写入数据
            printWriter.println(msg);
            printWriter.flush();

            //接受服务端的数据
            String line = is.readLine();
            System.out.println("服务器传回数据："+line);

            //关闭连接资源
            is.close();
            printWriter.close();
            socket.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**b.nio**

​	nio内部由Buffer、channel、selector构成，其中buffer就是信息传输中的信息载体，相当于货物；channel是服务器与客户端建立的一条信息传输的管道，相当于送货车；selector则是将信息进行分拣，相当于中转场。



## 5、函数式接口   {  @FunctionalInterface

**简介：**

所谓的函数式接口首先是一个接口，并且在这个接口里面只能只有一个抽象方法。这种类型的接口也称为SAM接口—single Abstract method interfaces

**特点：**

- 接口有且只有一个抽象方法
- 允许定义静态方法
- 允许定义默认方法
- java.lang.object里面的public方法
- 注解并不是必须的，只要定义的接口符合函数式接口的规范即可，但是只有增加函数式接口就必须符合函数接口的规范

**示例**

```java
// 正确的函数式接口
@FunctionalInterface
public interface TestInterface {
 
    // 抽象方法
    public void sub();
 
    // java.lang.Object中的public方法
    public boolean equals(Object var1);
 
    // 默认方法
    public default void defaultMethod(){
    
    }
 
    // 静态方法
    public static void staticMethod(){
 
    }
}

// 错误的函数式接口(有多个抽象方法)
@FunctionalInterface
public interface TestInterface2 {

    void add();
    
    void sub();
}
```

在构建方法的时候需要将函数式接口作为方法参数，然后就可以在使用该方法的时候使用lamda表达式

## 6、Java双冒号（::）的使用

| 语法种类                         | 示例                              |
| -------------------------------- | --------------------------------- |
| 引用静态方法                     | ContainingClass::staticMethobName |
| 引用特定对象的示例方法           | ContainingOject::instanceMethob   |
| 引用特定类型的任意对象的实例方法 | ContainingType::methodName        |
| 引用构造函数                     | ClassName::new                    |

- 引用静态方法

```java
public class test{
    public void demo(){
        List<String> list = Arrays.asList("aaa","bbb","ccc");
        list.forEach(Test::print)
    }
    
    public static void print(String text){
        System.out.println(text);
    }
}
```

- 引用特定对象的实例方法

```java
public class Test{
    public void test(){
        List<String> list = Arrays.asList("aaa","bbb","ccc");
        
        list.forEach(new Test()::print)
    }
    
    public void print(String text){
        System.out.println(text);
    }
}
```

- 引用特定类型的任意对象的实例方法

```java
public class Test extends BaseTest{
    public void test(){     
        List<String> list = Arrays.asList("aaa","bbb","ccc");
        
        list.forEach(super::print);
    }
 
}
class BaseTest{
    public void print(String text){
        System.out.println(text);
    }
}
```

- 引用构造函数

```java
public class Test{
    public void test(){
        InterfaceExample com = Example::new;
        Example bean = com.create();
        System.out.println(bean);
    }
    
}
interface InterfaceExample{
    Example create();
}
```

## 7、Java 8中的Optional类

Java 8引入最新的特性Optional类，Optional类主要解决的问题是空指针异常的问题(NullPointerException)

本质上，Optional是一个包含可选值的包装类，这意味着Optional类既可以含有对象也可以为空。

以前存在的问题：

场景：获取某一属性值

```java
String isocaode = user.getAddress().getCountry().getIsocode().toUpperCase();
```

为了让上述代码不出现空指针异常的情况，需要代码进行相应的优化

```java
if(user != null){
	Address address = new Address();
	if(address != null){
		Country country = new Country();
		if(country.getIsocode != null){
			String isocode = country.getIscode();
			if(iscode != null){
				isocode = isocode.toUpperCase();
			}
		}
	}
}
```

**创建Optional实例**

```java
// 使用of()方法创建的时候传进null值得话就会出现NullPointerException,如下所示
Optional<User> opt = Optional.of(user);
// 使用ofNullable()创建的时候，传进去的值既可以是null值也可以的是非null值
Optional<User> opt = Optional.ofNullable(user);
```

**访问Optional对象的值**

从Optional实例中取回实际值对象的方法之一就是使用get()方法，但是这个方法会在值为null的时候抛出异常，所以在获取值时候需要进行相应的验证。

```java
String name = "test";
Optional<String> opt = Optional.ofNullable(name);
assertTrue(opt.isPresent());
```

**返回默认值**

Optional类提供了API用以返回对象值，或者在对象为空的时候返回默认值。

- 第一个方法orElse()

有值就返回值，没有值就返回参数

```java
User user1 = new User();
User user2 = new User("null","18");
User user = Optional.ofNullable(user1).orElse(user2);
```

- 第二个方法orElseGet()

有值就返回值，没有值就执行作为Supplier(供应者)函数式接口，并执行返回执行结果

```java
User user = Optional.ofNullable(user).orElseGet(() -> user2)
```

差别在于在使用orElse()方法的时候，无论Optional里面有没有装配值都会对备选对象进行查询检查，而在使用orElseGet()方法的时候只有当Optional装配的是null值的时候才会执行lamda表达式。

**返回异常**

这个方法可以让我们自定义抛出异常，提供有用的异常信息

```java
User user = Optional.ofNUllable(user).orElseThrow(() -> new IllegalArgmentException());
```

**转换值**

map()方法：

map()对值应用作为参数的函数，然后将返回值包装在Option中，这就使对返回值进行链式调用的操作成为可能—下一个环节就是orElse()。

```java
User user = new User("转换值","18")
String name = Optional.ofNullable(user)
.map(u -> u.getName()).orElse("默认值")
```

flatMap()方法：

flatMap()也是需要函数作为参数，需要对Optional对象进行操作，对Optional对象进行操作返回的值是接触包装后的String值；

**过滤值**

Optional类也提供了按照条件进行“过滤”值的方法。filter()接受一个Predicate参数，返回测试结果为true的值，如果测试结果为false，会返回一个空的Optional

```java
User user = new User("过滤值","1234");
Optional<User> opt = Optional.ofNullable(user)
.filter(u -> u.getName() != null && u.getName().length <= 3);
```



## 8、Java自带的四大函数式接口

- **supplier 提供者接口**
- **consumer 消费者接口**
- **function 函数式接口**
- **predicate 断定式接口**
- ***其他接口***

> supplier 提供者

​	该接口不接受参数，但是提供一个返回值，无私的奉献者，使用get()方法获得这个返回值

```java
Supplier<String> getInstance = () -> "HelloWorld!"
```

> consumer 消费者

​	该接口接受一个参数，没有返回值，通俗理解就是把参数消费掉了，一般来说使用Consumer接口往往伴随着一些期望状态的改变或者事件的发生。

```java
Consumer<String> printer = s -> System.out.println(s);
printer.accept("helloWorld!")
```

> function 函数式接口

​	该接口接受一个T类型的参数，返回R类型的结果的方法的抽象，通过调用apply方法执行内容。

```java
Function<String,String> func = s -> s.trim();
func.apply(" helloWorld!  ");
```

> predicate 断言式接口

​	该接口接受一个参数，制定相应的规则进行判断，通过test()方法返回一个Boolean值

```java
Predicate<String> pre = s -> s.length() >= 0;
pre.test("hello world!");
```

> 其他接口

1. 类型限制接口： IntPredicate，LongPredicate，DoublePredicate，这几个接口，都是基于Predicate接口，不同的就是他们的泛型变成Integer，Long，Double，而IntConsumer，LongConsumer，DoubleConsumer比如这几个，对应的就是Consumer<Integer>，Consumer<Long>，Consumer<Double>，其余的都是一样的道理。
2. 数量限制接口：有些接口需要接受两个参数，此类接口的所有名字前面都是附加上Bi，是Binary的缩写

## 9、**transient**关键字的意思

将不需要序列化的属性前添加关键字**transient**,序列化对象的时候，这个属性就不会被序列化。（静态变量无法被序列化）

**底层原理**

java的序列化操作是将对象的状态储存到硬盘上去，等需要的时候就可以再把它读出来使用；所以transient的作用就是把修饰的字段固定在内存中进行操作而不会写到磁盘中里持久化。

**被transient关键字修饰的变量仍然可以被序列化**

Java序列化提供了两种方式

一是实现Serializable接口

二是实现Exteranlizable接口，需要重写writeExternal和readExternal方法，它的效率比Serializable高一些，并且可以决定哪些属性需要序列化

## 10、什么是jetty？

jetty是一个开源的servlet容器，基于Java的web容器，例如JSP和servlet提供运行环境，jetty是使用java语言编写的，它的API是以一组JAR包的形式发布。开发人员可以将Jetty容器实例化成一个对象，可以迅速为一些独立的java应用提供网络和web连接。

<font color="#f15b6c">**Jetty就是一个开源的HTTP服务器和Servlet引擎**</font>，比如ActiveMQ、Maven、Spark等都应用了Jetty。

## 11、为什么使用Jetty

- 异步的Servlet,支持更高的并发量
- 模块化的设计，更加灵活，更容易定制，也意味着更高的资源利用率
- 面对大量长连接业务场景下，Jetty默认采用NIO模型是更好的选择
- 将Jetty嵌入到应用中，使一个普通英勇可以快速支持http服务

## 12、java的定时任务

java系统中主要有三种方式来实现定时任务

1. Timer和TimerTask
2. ScheduledExecutorService
3. 三方框架Quartz

- **Timer和TimerTask**

***demo***:

```java
public static void main(String[] args) throws Interruption{
    Timer timer = new Timer();
    timer.schedule(new TimerTask(){
        @Override
        public void run(){
            System.out.println("hello")
        }
    },1000);
    Thread.slepp(2000);
    timer.cancel();
}
```

这种方式的定时任务主要用到两个类，Timer和TimerTask；其中TimerTask继承接口Runnable，抽象的描述一种任务类型，我们只要重写实现它的run方法就可以实现自定义任务。

Timer类就是用于定时任务调度的核心类，上述代码我们调用schedule并指定延时1000毫秒，所以上述代码会在一秒钟后完成打印操作，接着程序结束。

<u>***内部逻辑***</u>

Timer接口里面，存在着两个核心的字段

```java
/**
*队列，内部由动态数组实现的最小堆结构——优先队列。优先级参考下一次执行时间，越快执行就排在越前面
*/
private final TaskQueue queue = new TaskQueue();

/**
*TimerThread继承Thread并重写了其run方法，该线程实例将在构建Timer实例的时候启动。run方法内部会循环的从队列中取*任务
*/
private final TimerThread thread = new TimerThread(queue);

```

Timer中用于配置一个定时任务进任务队列的方法

```java
//在时刻time执行任务
schedule(TimeTask task,Date time);

//延时 delay毫秒后执行任务
schedule(TimeTask task,long delay);

//固定延时重复执行，firstTime为首次执行时间
//往后每间隔period毫秒执行一次
schedule(TimeTask task,Date firstTime,long period);

//固定延迟重复执行
//首次执行时间为当前时间延时delay毫秒
schedule(TimeTask task,long delay,long period);

//固定频率重复执行，每过period毫秒执行一次
scheduleAtFixedRate(TimeTask task,Date firstTime,long period);

//固定频率重复执行
scheduleAtFixedRate(TimeTask task,long delay,long period);
```

固定延时和固定频率

固定延时：以任务的上一次实际执行时间做参考，往后延时period毫秒。

固定频率：任务的往后每一次执行时间都在任务提交的那一刻得到了确定，无论上一次的任务是否意外延时，定时定点执行下一次的任务。

整个方法的逻辑：

1. 首先使用任务队列的内置对象锁，锁住个队列
2. 接着在去锁住我们的task，并修改内部的一些属性字段值，设置下一次执行的时间为delay
3. 然后将task添加到任务队列，其中add方法内部会进行最小堆重构
4. 判断如果自己就是队列的第一个任务，那么将唤醒Timer中阻塞了的任务线程

***Timer的劣势***

1. Timer的背后只有一个线程，不管有多少个任务，只有一个工作线程，效率上必打折扣
2.  限于单线程，如果第一任务逻辑上死循环了，后续的任务一个也得不到执行，稳定性差

- **ScheduleExecutorService**

整体来说,ScheduledExecutorService区别于Timer的地方就在于前者依赖线程池来执行任务，而任务本身会判断是什么类型的任务，需要重复执行的在任务执行结束后会被重新添加到任务队列

- 第三方库Quartz

体系结构：

1. 调度器：scheduler
2. 任务：JobDetail
3. 触发器：Trigger，包括SimpleTrigger和CronTrigger

***demo***

```java
//待执行任务
public class HelloWorldJob implements Job {
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        String strTime = new SimpleDateFormat("HH-mm-ss").format(new Date());
        System.out.println( strTime + ":Hello World！");
    }
}
```



```java
//执行任务
public class MyScheduler {
    public static void main(String[] args) throws SchedulerException {
        //创建调度器Schedule
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler = schedulerFactory.getScheduler();

        //创建JobDetail实例，并与HelloWordlJob类绑定
        JobDetail jobDetail = JobBuilder.newJob(HelloWorldJob.class).withIdentity("job1", "jobGroup1")
                .build();

        //创建触发器Trigger实例(立即执行，每隔1S执行一次)
        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity("trigger1", "triggerGroup1")
                .startNow()
       .withSchedule(SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(1).repeatForever())
                .build();
        //开始执行
        scheduler.scheduleJob(jobDetail, trigger);
        scheduler.start();
    }
}
```



## 13、java反射

- 什么是反射

  java反射机制实在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象都能调用它的任意一个方法和属性，这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制，反射就是把类中的各个成分映射成一个个java对象

- 反射的作用

  通过反射可以是代码程序访问装在到JVM中的类的内部信息，获取已装载类的成员变量信息，已装载类的构造方法信息和已装载类的构造方法信息。

  使用反射基本上是一种解释操作，用于字段和方法接入时要远慢于直接代码。因此Java反射机制主要应用在对灵活性和扩展性要求很高的系统架构上，普通程序不建议使用。

## 14、强引用、软引用、弱引用、虚引用

引用类型 | 被回收时间 | 用途 | 生存时间 | 类
------ | ----- | ----- | ----- | ---
强引用 | 从来不会 | 对象的一般状态 | JVM停止运行时 | Object
软引用 | 内存不足时 | 对象缓存 | 内存不足时 | SoftReference
弱引用 | jvm垃圾回收时 |对象缓存 |gc运行后 | WeakReference
虚引用 | 未知 | 未知 | 未知 | PhantomReference