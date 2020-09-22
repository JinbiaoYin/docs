# Sentinel 服务熔断

## 服务雪崩
在微服务架构中，根据业务来拆分成一个个的服务，服务与服务之间可以通过 RPC 相互调用，在 `Spring Cloud` 中可以用 `RestTemplate` + `LoadBalanceClient` 和 `Feign` 来调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证 100% 可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，`Servlet` 容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 “雪崩” 效应。

## 什么是 Sentinel
随着微服务的流行，服务和服务之间的稳定性变得越来越重要。 Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。


## 使用

### 下载

[https://github.com/alibaba/Sentinel/releases/tag/v1.8.0](https://github.com/alibaba/Sentinel/releases/tag/v1.8.0)  

### 依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

### 配置
```yml
spring:
  sentinel:
    transport:
      dashboard: localhost:8888

feign:
  sentinel:
    enabled: true
```

**创建熔断器类并实现对应的 Feign 接口**

```java
@Component
public class EchoServiceFallback implements EchoService {
    @Override
    public String echo(String message) {
        return "echo fallback";
    }
}
```

**在 `Service` 中增加 `fallback` 指定类**
```java
@FeignClient(value = "nacos-provider", fallback = EchoServiceFallback.class)
public interface EchoService {

    @GetMapping(value = "/echo/{message}")
    String echo(@PathVariable("message") String message);
}
```