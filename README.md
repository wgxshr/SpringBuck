# SpringBucks点餐系统

## 微服务

**Spring Cloud**微服务项目，共三个微服务，分别是customer-service，waiter-service和barista-service。

系统架构如下：

![image-20210306163254706](C:\Users\wgx11\AppData\Roaming\Typora\typora-user-images\image-20210306163254706.png)

## 系统健康检查、审计、统计和监控

使用了**Spring Boot Actuator**进行系统的健康检查、审计、统计和监控，通过实现**Actuate**的**Health Indicator**实现了一个自定义的健康检查，用来检查咖啡数量的健康状态，防止咖啡数量出现小于0的情况。

## 缓存系统

使用了**[Redis](https://redis.io/)**作为旁路缓存，用来缓存coffee菜单等读多写少的数据。

> Redis是一种键值型数据库，值（value）可以是字符串(String)、哈希(Hash)、列表(list)、集合(sets)和有序集合(sorted sets)等类型。

## 服务注册和服务发现

使用了**[Consul](https://www.consul.io/)**作为服务注册中心

> **Concul**是一个**分布式、高可用，能感受数据中心变化**的分布式基础设施，可以用来做服务的连接、配置

使用**[Feign](https://github.com/OpenFeign/feign)**搭配Consul作为服务调用。

> feign是声明式的web service客户端，它让微服务之间的调用变得更简单了，类似controller调用service。

## 服务熔断和服务限流

为了能让系统在不同压力的环境下有比较正常的表现，使用了**[Resilience4J](https://github.com/resilience4j/resilience4j)**服务熔断和服务限流。

>Resilience4j 是一个轻量级的容错组件，其灵感来自于 Hystrix，但主要为 Java 8 和函数式编程所设计。轻量级体现在其只用 Vavr 库（前身是 Javaslang），没有任何外部依赖。而 Hystrix 依赖了 Archaius ，Archaius 本身又依赖很多第三方包，例如 Guava、Apache Commons Configuration 等。是NetFlex官方推荐的替代Hystrix的容错组件。

**waiter-service**

使用Resilience4J的RateLimiter做了限流

**customer-service**

 使用Resilience4J的CircuitBreaker做了短路保护

 使用Resilience4J的bulkhead隔舱做了隔舱模式的实现

 ## 配置中心

 使用**[Consul](https://www.consul.io/)**做配置中心使系统支持动态地更新配置，使得系统具有良好的扩展性。

 ## 消息队列

 使用**[RabbitMQ](https://www.rabbitmq.com/)**做消息队列，通过消息队列，完成customer-service，waiter-service和barista-service之间的消息传递。
 >RabbitMQ是一个开源的面向消息的中间件，它有两个高级特性：
 >- 可伸缩性：集群服务
 >- 消息持久化：从内存持久化消息到硬盘，再从硬盘加载到内存 



## 服务链路追踪

使用**Sleuth**和**[Zipkin](https://zipkin.io/)**服务链路追踪。在微服务架构中，由于服务单元数量众多，业务的复杂性，如果出现了错误和异常，很难去定位。所以必须实现分布式链路追踪，去跟进一个请求到底有哪些服务参与，参与的顺序又是怎样的，从而达到每个请求的步骤清晰可见，出了问题，很快定位。

>spring cloud提供了spring-cloud-sleuth-zipkin来方便集成zipkin实现，该jar包可以通过spring-cloud-starter-zipkin依赖来引入。

>**Zipkin**分布式跟踪系统；它可以帮助收集时间数据，解决在microservice架构下的延迟问题；它管理这些数据的收集和查找。
>每个应用程序向Zipkin报告定时数据，Zipkin UI呈现了一个依赖图表来展示多少跟踪请求经过了每个应用程序；如果想解决延迟问题，可以过滤或者排序所有的跟踪请求，并且可以查看每个跟踪请求占总跟踪时间的百分比。