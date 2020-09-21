# Nocas

## Nocas Docker 安装

进入`/usr/local/docker`文件夹，准备下载`nacos`镜像。

```sh
$ cd /usr/local/docker

# 克隆 nacos 镜像，没有 git 就先装 git 再操作。
$ git clone https://github.com/nacos-group/nacos-docker.git
```

选择不同方式部署

- 单机模式 Derby
```
docker-compose -f example/standalone-derby.yaml up 
```
- 单机模式 Mysql
```
docker-compose -f example/standalone-mysql-8.yaml up 
```
- 集群模式
```
docker-compose -f example/cluster-hostname.yaml up 
```

启动完成后，访问`http://ip:port/nacos`，默认用户名密码 `nacos/nacos`

## 服务注册与发现
在服务的 `application.yml` ，加入配置：

```yml
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848

server:
  port: 8081

management:
  endpoints:
    web:
      exposure:
        include: "*"
```

## OpenFeign

### POM
```yml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

服务间调用和负载均衡要通过`Feign`来进行。服务消费者引入`Feign`依赖后，在启动类上加`@EnableFeignClients`注解开启`Feign`。

### 创建 Feign 接口

通过 `@FeignClient("服务名")` 注解来指定调用哪个服务。代码如下：

```java
package com.funtl.hello.spring.cloud.alibaba.nacos.consumer.feign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(value = "nacos-provider")
public interface EchoService {

    @GetMapping(value = "/echo/{message}")
    String echo(@PathVariable("message") String message);
}
```

`Controller`正常调用。

### 配置负载均衡
`Feign`默认开启轮询的负载均衡策略。还提供更多策略，具体需要可根据文档更改。


## 分布式配置中心
使用 Spring Cloud Alibaba Nacos Config，您可以在 Nacos Server 集中管理你 Spring Cloud 应用的外部属性配置，支持动态更新。

### nacos 配置
Nacos Server 中创建配置文件，我们依然采用 YAML 的方式部署配置文件，操作流程如下：

1. 浏览器打开 `http://localhost:8848/nacos` ，访问 Nacos Server
2. 新建配置文件，此处我们以之前创建的 服务提供者 项目为例，注意：Data ID 的默认扩展名为 .properties ，希望使用 YAML 配置，此处必须指明是 .yaml
3. 将原本的`application.yml`中的配置，拷贝进来保存提交。

### POM
在项目中加入`nacos-config`的依赖。
```yml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

### bootstrap.properties
创建名为 bootstrap.properties 的配置文件并删除之前创建的 application.yml 配置文件。

```properties
# 这里的应用名对应 Nacos Config 中的 Data ID，实际应用名称以配置中心的配置为准
spring.application.name=nacos-provider-config
# Nacos Server 的地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
# 指定查找名为 nacos-provider-config.yaml 的配置文件
spring.cloud.nacos.config.file-extension=yaml
```
**注意：配置优先级顺序为 bootstrap.properties -> bootstrap.yml -> application.properties -> application.yml**