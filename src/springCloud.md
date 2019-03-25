## spring微服务学习

![1550108269570](E:\git\hello-world\src\img\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1550108269570.png)

eureka.server.enableselfPreservation=false（来关闭保护机制，以确保注册中心可以将不可用的实例正确剔除）

Eureka客户端配置只要分为两方面：

1.服务注册相关的配置信息，包括服务注册中心的地址、服务获取的时间间隔、可用区域等

2.服务实例相关的配置信息，包括服务实例的名称、IP地址、端口号、健康检查路径等

## 服务实例配置

eureka.instance.<properties>=<value>的格式对标准化元数据直接进行配置，其中<properties>就是EurekaInstanceConfigBean对象中的成员变量名。

自定义元数据: eureka.instance.metadataMap.<key>=<value>的格式来进行配置，比如：eureka.instance.metadataMap.zone=shanghai

# 客户端负载均衡spring cloud Ribbon

RestTemplate @LoadBalanced

```
LoadBalancerAutoConfiguration
```

在该自动化配置类中，主要做了下面三件事：

●创建了一个 LoadBalancerInterceptor 的 Bean，用于实现对客户端发起请求时进行拦截，以实现客户端负载均衡。

●创建了一个RestTemplateCustomizer的Bean，用于给RestTemplate增加LoadBalancerInterceptor拦截器。

### 自动配置

●IClientConfig:Ribbon 的客户端配置，默认采用 com.netflix.client.config.DefaultClientConfigImpl实现。

●IRule:Ribbon 的负载均衡策略，默认采用 com.netflix.loadbalancer.ZoneAvoidanceRule实现，该策略能够在多区域环境下选出最佳区域的实例进行访问。

●IPing:Ribbon的实例检查策略，默认采用com.netflix.loadbalancer.NoOpPing实现，该检查策略是一个特殊的实现，实际上它并不会检查实例是否可用，而是始终返回true，默认认为所有服务实例都是可用的。

●ServerList<Server>：服务实例清单的维护机制，默认采用 com.netflix.loadbalancer.ConfigurationBasedServerList实现。

●ServerListFilter<Server>：服务实例清单过滤机制，默认采用 org.springframework.cloud.netflix.ribbon.ZonePreferenceServerLis tFilter实现，该策略能够优先过滤出与请求调用方处于同区域的服务实例。

●ILoadBalancer：负载均衡器，默认采用 com.netflix.loadbalancer.ZoneAwareLoadBalancer实现，它具备了区域感知的能力。

### 重试机制

由于Spring Cloud Eureka实现的服务治理机制强调了CAP原理中的AP，即可用性与可靠性，它与ZooKeeper这类强调CP（一致性、可靠性）的服务治理框架最大的区别就是，Eureka为了实现更高的服务可用性，牺牲了一定的一致性，在极端情况下它宁愿接受故障实例也不要丢掉“健康”实例，比如，当服务注册中心的网络发生故障断开时，由于所有的服务实例无法维持续约心跳，在强调 AP 的服务治理中将会把所有服务实例都剔除掉，而Eureka则会因为超过85%的实例丢失心跳而会触发保护机制，注册中心将会保留此时的所有节点，以实现服务间依然可以进行互相调用的场景，即使其中有部分故障节点，但这样做可以继续保障大多数的服务正常消费。

