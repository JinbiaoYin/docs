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