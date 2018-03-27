spring cloud是一个微服务框架。如果非与dubbo hsf进行对比，spring cloud可能在他们的基础上更抽象一层。
通过看spring cloud的架构，可以对这种服务治理的框架有一个更好的认识。spring cloud分为多个不同的功能模块。可以挨个看下；

1 eureka（读 优瑞克）意思是我发现了
是一个服务注册与发现模块。分为两个功能看：eureka server服务注册中心--用于管理所有注册服务，eureka client服务注册客户端--其实也是微服务提供者，它提供实际的业务服务，但是要将自身信息注册到eureka server上，以便于服务消费者使用。
创建一个eureka很简单，依赖eureka server的stater，并使用相应注解启动一个springboot服务，一个注册中心应用就搞定了
创建一个eureka client也很简单，也是依赖eureka相关的服务，添加相应注解，并启动一个springboot web服务，某个http服务即可作为服务提供者

2 ribbon 意思是带状物，丝带（什么鬼？）
在spring cloud中的服务与服务之间的调用都是基于http的。spring cloud有两种服务调用方式：ribbon + restTemplate 以及feign
ribbon是一个负载均衡客户端，也作为一个spring boot应用启动，也需要注册到服务中心，可以使用RestTemplate，直接调用服务，并获得返回结果

3 Feign 意思是假装、伪装、虚构（这又是什么鬼？）
Feign是一个声明式的伪Http客户端，它使得写Http客户端变得更简单。使用Feign，只需要创建一个接口并注解。Feign也整合了ribbon。
使用起来很方便，有点类似hsf客户端的声明

4 Hystrix 意思是红锥（。。。感觉名称越来越过分了。。）
被称为断路器。通常为了某个服务的高可用，我们会将这个服务集群部署。集群部署还要解决另一个问题：如果某个单个服务器出现问题，不能在这个服务器上卡住，或者详细点说，如果一台服务器出现问题，要可以立刻返回错误，不能阻塞在这边，否则上游阻塞的服务器会越来越多，导致大部分服务不可用。
为了解决这个问题，业界提出了断路器模型。
hystrix是用在服务消费端的，可以自己设置超时时间，以及fallback方法（熔断方法）。通过注解的方式即可完成。

5 Zuul 意思是祖鲁（去你妈的。。）
Zuul的主要功能是路由转发和过滤器。
在微服务架构中，客户端会先经过负载均衡（zuul，nginx），再到达服务网管，再到某个具体服务。
zuul服务也需要注册到服务中心，然后需要配置路径和服务的映射，将某些特定的path映射到具体的服务上，不过我不太确定zuul是面向用户还是面向服务消费者，亦或者全都可以。
它还可以做一些服务过滤以及安全验证

6 spring cloud config分布式配置中心组件
分为config-server和config-client。这个组件中主要用于存储key-value类型的properties，类似阿里的diamond。
支持将配置放在服务器内存中，也支持放在git仓库中。config-server启动后可以直接通过http请求访问到配置。
不过spring提供了config-client，这样可以通过注解的方式，更加方便的取得所有配置
config-server可以结合eureka作为分布式的配置中心

7 Spring Cloud Bus 后续补充

8 zipkin 服务追踪组件
Spring Cloud Sleuth集成了zipkin组件
微服务架构上通过业务来划分服务的，通过REST调用，对外暴露的一个接口，可能需要很多个服务协同才能完成这个接口功能，所以可能一个功能需要调用很多接口才可以完成，这是如果能梳理出整个服务调用的链路，将会对问题的排查有很大帮助
zipkin通过启动一个server来收集所有调用，另外所有服务调用系统都要配置zipkin server地址，整个配置过程无侵入性，不过内部实现可能会监听每次请求的发送。


