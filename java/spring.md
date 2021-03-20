# Spring基础

## IOC、DI以及AOP是什么？

### IOC(控制反转)

Ioc容器获取对象是程序再加载xml配置文件的时候通过反射调用构造函数来创建对象。

Ioc的思想最核心在于：资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。

- 资源集中管理，实现资源的可配置和易管理

- 降低了使用资源双方的依赖程度

### DI依赖注入

依赖注入是控制反转的一个例子，在xml中通过ref属性将其他bean放到当前bean对象，注入方式：

1. 文件注入
    - 属性注入
    - 构造器注入
2. 注解注入
    - Autowired（Spring，默认byType）
    - Resource（J2EE，默认byName）

## 1、BeanWrapper及其实现

### a、什么是beanWrapper

BeanWrapper是对Bean的包装，接口定义的功能很简单包括设置获取被包装的对象，获取被包装bean的属性描述器，通过BeanWrapper，Spring ioc容器可以用统一的方式访问bean的属性。

### b、BeanWrapperImpl

BeanWrapperImpl类是对BeanWrapper接口的默认实现，它包装了一个bean对象，缓存了bean中的内省结果，可以访问bean的属性、设置bean的属性值等，

