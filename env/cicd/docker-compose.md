---
autoGroup-1: Docker
title: Docker Compose
---

## 介绍
`Docker Compose` 是 Docker 官方编排（Orchestration）项目之一，负责快速的部署分布式应用。

`Compose` 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。从功能上看，跟 `OpenStack` 中的 `Heat` 十分类似。

`Compose` 定位是 **「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」**，其前身是开源项目 Fig。

我们知道使用一个 `Dockerfile` 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

`Compose` 恰好满足了这样的需求。它允许用户通过一个单独的 `docker-compose.yml` 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

`Compose` 中有两个重要的概念：
- 服务 (`service`)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
- 项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

`Compose` 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

`Compose` 项目由 `Python` 编写，实现上调用了 `Docker` 服务提供的 API 来对容器进行管理。**因此，只要所操作的平台支持 Docker API，就可以在其上利用 Compose 来进行编排管理。**


## Docker Compose 安装与卸载

### 关于版本
配置：
- 阿里云1核2G内存1M带宽
- Ubuntu版本为 18.04（64位）

### Ubuntu 安装
**二进制包** 

1. 从 [官方 GitHub Release](https://github.com/docker/compose/releases) 处直接下载编译好的二进制文件即可。
例如，最新版本为 `1.26.0-rc2`
```sh
curl -L https://github.com/docker/compose/releases/download/1.26.0-rc2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

将下载路径中的 `1.26.0-rc2` 修改为你需要的版本。
这里下载到`/usr/local/bin/`，并重命名为 `docker-compose`

2. **下载完成后，赋予权限**
```sh
chmod +x /usr/local/bin/docker-compose
```

### Ubuntu 卸载
如果是二进制包方式安装的，删除二进制文件即可。
```sh
rm /usr/local/bin/docker-compose
```

## 基本模板

```yml
version: "3"
services:
  tomcat:
    restart: always
    image: examples/web
    ports:
      - "80:80"
    volumes:
      - "/data"
```

- `version`: 当前**docker-compose**的版本
- `services`: 所有的服务
- `tomcat` : 第一个服务
- `restart` : 重启
- `image` : 镜像
- `ports` : 端口映射
- `volumes` : 指定数据卷

### MySQL
```
version: '3.1'
services:
  db:
    image: mysql:8.0.21
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
      - ./conf:/etc/mysql
      - ./logs:/var/log/mysql
```

### Nexus
```
version: '3.1'
services:
  nexus:
    restart: always
    image: sonatype/nexus3
    container_name: nexus
    ports:
      - 8081:8081
    volumes:
      - data:/nexus-data

volumes:
  data:
```