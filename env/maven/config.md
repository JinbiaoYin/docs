---
title: maven配置
---

> 配置的优先级逐渐递增。

## 配置全局镜像仓库
在 Maven 的 `setting.xml` 中配置 <mirror/>，例如：

```xml
<mirrors>
  <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
  </mirror>
</mirrors>  
```
- `<mirrorOf>central</mirrorOf>` 表示 此镜像将会覆盖 `id` 为 `central` 的仓库。
- `<mirrorOf>*</mirrorOf>` 表示 此镜像将会过滤所有仓库。


### 配置本地项目仓库
在项目的 `pom.xml` 中配置 `<repositories/>`。例如：

```xml
    <repositories>
        <repository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>https://maven.aliyun.com/repository/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>sonatype-repos</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>sonatype-repos-s</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>https://maven.aliyun.com/repository/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
```

### 本地项目 profile 配置仓库
在项目的 `pom.xml` 中配置 `<profile/>` 类似上述

### 全局 profile 配置仓库

在 Maven 的 `settings.xml` 中 `<profile/>` 标签中配置。如下：

```xml
  <profiles>
    <profile>
      <activation>
        <!-- 默认激活此 profile -->
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>yinjinbiao</id>
          <url>http://118.190.101.57:8081/repository/maven-public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>yinjinbiao_plugin</id>
          <url>http://118.190.101.57:8081/repository/maven-public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
```


## Maven 使用规范
正常来说，我们会在自己公司的内部网络中部署 `Nexus` ，用来管理我们的依赖。

在搭建`Nexus`完成后，可以在自己来创建`Repositories`。例如`aliyun`，`spring`的私服地址。
```
http://maven.aliyun.com/nexus/content/groups/public/
https://repo.spring.io/milestone
https://repo.spring.io/snapshot
```

然后把它们聚合到`maven-public`中。

在 maven 的`setting.xml`中，配置`server`节点
```xml
      <server>
        <id>nexus-public</id>
        <username>admin</username>
        <password>yinjinbiao!2020</password>
      </server>
      <server>
          <id>nexus-releases</id>
          <username>admin</username>
          <password>yinjinbiao!2020</password>
      </server>
      <server>
          <id>nexus-snapshots</id>
          <username>admin</username>
          <password>yinjinbiao!2020</password>
      </server>
```

配置`mirror`节点
```xml
      <mirror>
          <id>nexus-public</id>
          <mirrorOf>*</mirrorOf>
          <name>Nexus Public</name>
          <url>http://118.190.101.57:8081/repository/maven-public/</url>
      </mirror>
```
这样，所有的依赖都会从`http://118.190.101.57:8081/repository/maven-public/`上下载，而这个低质上我们上述配置了聚合的地址。所以这里只需要配置私服的`maven-public`就相当于配置了多个。

如果项目中，想要上传jar包，需要配置：
```xml
    <distributionManagement>
        <repository>
            <id>nexus-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://118.190.101.57:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://118.190.101.57:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```
