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


## Maven 无法下载依赖

一般是由于网络问题，建议本地项目配置 repository 为阿里云的仓库（一般公司都是配好的，如果没配，那就自己在 `settings.xml` 中配置阿里云镜像）。

如果配置阿里云镜像还是无法下载，且自己公司没有搭建私服，可以使用就近节点，例如我自己搭建的私服。

在Maven `Settings.xml` 中配置就近节点的私服。

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


