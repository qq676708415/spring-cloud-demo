## 简介
该项目的学习来源于B站千锋教育公开的[视频教程](https://www.bilibili.com/video/av36042649/)，讲师[博客地址](https://www.funtl.com/zh/spring-cloud-netflix/),自我感觉讲师讲的还挺好的，通俗易懂。


## 项目结构

```
.
├── README.MD
├── spring-cloud-admin         # 基于spring-boot-admin的微服务管理
├── spring-cloud-admin-service # 服务提供者
├── spring-cloud-admin-web-feign # 服务消费者（基于feign）
├── spring-cloud-admin-web-ribbon # 服务消费者（基于ribbon）
├── spring-cloud-config        # 分布式配置中心
├── spring-cloud-config-file   # 非服务，基于github管理各个微服务的配置文件
├── spring-cloud-dependencies  # 统一项目依赖管理
├── spring-cloud-eureka        # 服务注册与发现
├── spring-cloud-zipkin        # 服务链路追踪
└── spring-cloud-zuul          # 统一网关访问服务
```

### 1、统一的依赖管理

没有具体代码，统一指定了spring-boot的版本，spring-cloud版本

### 2、服务注册与发现（Eureka）

使用了Spring Cloud Netflix的Eureka作为服务注册与发现中心，依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```
### 3、服务提供者

通过注解@EnableEurekaClient表明是一个服务提供者,注册到Eureka

### 4、服务消费者

#### 4.1、基于Ribbon

Ribbon 是一个负载均衡客户端，服务与服务之间的调用基于http RESTfull，通过@EnableDiscoveryClient注册到Eureka，依赖jar包

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
```
#### 4.2、基于Feign

Feign是一个声明式的伪Http客户端，默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果，一般情况使用Feign。依赖jar

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
#### 4.3、熔断器

类似于保险丝，比如单个服务提供者故障，此时若有大量请求涌入，Servlet容器的线程池资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 “雪崩” 效应。

通过Hystrix 组件，实现了熔断器模式，较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystrix 是 5 秒 20 次） 熔断器将会被打开。
![](https://www.funtl.com/assets/Lusifer201805292246008.png)
熔断器打开后，为了避免连锁故障，通过 fallback 方法可以直接返回一个固定值

依赖jar包

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

### 5、网关统一访问接口
采用了Zuul主要功能就是路由转发和过滤器。
![](https://www.funtl.com/assets/Lusifer201805292246011.png)
在 Spring Cloud 微服务系统中，一种常见的负载均衡方式是，客户端的请求首先经过负载均衡（Zuul、Ngnix），再到达服务网关（Zuul 集群），然后再到具体的服。服务统一注册到高可用的服务注册中心集群，服务的所有的配置文件由配置服务管理，配置服务的配置文件放在 GIT 仓库，方便开发人员随时改配置。

依赖jar

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

### 6、分布式配置中心

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。在 Spring Cloud 中，有分布式配置中心组件 Spring Cloud Config ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程 Git 仓库中。在 Spring Cloud Config 组件中，分两个角色，一是 Config Server，二是 Config Client。

#### 6.1 Config Server

负责从远程git仓库读取配置文件,服务提供者

依赖jar

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
``` 

#### 6.2 Config Client

即各个业务微服务，通过依赖jar

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

### 7、服务链路追踪
采用zipkin，收集服务与服务之间调用的链路信息，耗时等来解决微服务架构的延迟问题

### 8、基于Spring-boot-admin的微服务管理

也分客户端、服务端，客户端即各个微服务，服务端单独服务项目。

## 启动顺序

```
├── README.MD
├── 8、spring-cloud-admin         # 基于spring-boot-admin的微服务管理
├── 3、spring-cloud-admin-service # 服务提供者
├── 4、spring-cloud-admin-web-feign # 服务消费者（基于feign）
├── 5、spring-cloud-admin-web-ribbon # 服务消费者（基于ribbon）
├── 2、spring-cloud-config        # 分布式配置中心
├── spring-cloud-config-file   # 非服务，基于github管理各个微服务的配置文件
├── spring-cloud-dependencies  # 统一项目依赖管理
├── 1、spring-cloud-eureka        # 服务注册与发现
├── 7、spring-cloud-zipkin        # 服务链路追踪
└── 6、spring-cloud-zuul          # 统一网关访问服务

```




