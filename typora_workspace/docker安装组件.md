# 安装mysql

## 拉取

```shell
sudo docker pull mysql:5.7
```

## 检查当前镜像

```shell
sudo docker images
```

## 创建实例并启动

```shel
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
```

参数解释：

​	-p 3306:3306：将容器的3306端口映射到主机的3306端口

​	-v /mydata/mysql/log:/var/log/mysql：将日志文件夹挂载到主机

​	-e MYSQL_ROOT_PASSWORD=root：初始化root用户的密码

## 修改配置文件

```shell
vi /mydata/mysql/conf/my.cnf
```

```tex
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
init_connect="SET collation_connection = utf8_unicode_ci"
init_connect="SET NAMES utf8"
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
```

## 重启实例

```shell
docker restart mysql
```



# 安装redis

## 拉取

```shell
docker pull redis
```

## 创建实例并启动

```shell
mkdir -p /mydata/redis/conf
```

```shell
touch /mydata/redis/conf/redis.conf
```

```shell
docker run -p 6379:6379 --name redis \
-v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf
```

## 测试

```shell
docker exec -it redis redis-cli
```

看是否能连接上

## redis持久化

```shell
vi /mydata/redis/conf/redis.conf
```

增加以下配置

```tex
appendonly yes
```

## 重启

```shell
docker restart redis
```



# 其他

## 查看目前运行的镜像

```shell
docker ps
```

## 进入镜像内部

```shell
docker exec -it mysql /bin/bash
```

## 删除容器实例

```shell
docker rm 容器ID或容器名
```

## 停止运行中的实例

```shell
docker stop 容器ID或容器名
```

## 设置实例自启动

```shell
sudo docker update 容器名 --restart=always
```

