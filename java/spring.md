## 1、BeanWrapper及其实现

### a、什么是beanWrapper

BeanWrapper是对Bean的包装，接口定义的功能很简单包括设置获取被包装的对象，获取被包装bean的属性描述器，通过BeanWrapper，Spring ioc容器可以用统一的方式访问bean的属性。

### b、BeanWrapperImpl

BeanWrapperImpl类是对BeanWrapper接口的默认实现，它包装了一个bean对象，缓存了bean中的内省结果，可以访问bean的属性、设置bean的属性值等，

