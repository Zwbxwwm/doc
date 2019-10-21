# JAVA基础
## Java创建对象的4种方式
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