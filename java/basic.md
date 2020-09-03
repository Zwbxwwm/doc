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

## 6、函数式接口









































