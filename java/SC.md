# **SC服务框架笔记**

1. 为什么使用SC服务的框架
   - 降低复杂性，传统的单体应用项目往往包含整个应用的所有模块，在进行项目的迭代的时候，代码不断臃肿，每次修改代码说不定都会造成难解的bug。
   - 提高部署效率，单体应用在部署的时候往往都需要重新部署这个项目，全量部署往往部署时间长、影响范围大、风险高，所以单体应用的部署频率都比较低，从而导致两次项目部署的期间存在这许多需要进行变更或者是修改的功能点。
   - 提高可靠性，在单体应用中，某一个应用的bug往往会造成oom,整个系统崩溃。
2. 服务提供者、服务消费者、服务发现组件这三者之间关系
   - 各个微服务启动的时候会把该微服务的网址等信息注册到服务发现组件中去，服务发现组件会保存这些信息。
   - 服务消费者从服务组件里面查询服务提供者的网址信息，并使用该网址调用提供者的服务。
   - 各个微服务与服务组件之间通过一定的通信机制进行交流，服务组件发现某个微服务长时间无响应的时候就会销毁该实例。
   - 当服务提供者变更网络地址等信息时，会重新注册到服务发现组件。
3. 

