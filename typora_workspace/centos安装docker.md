# 介绍

docker是虚拟化容器，基于镜像，可以秒级启动各种容器，每一个容器都是一个完整的运行环境，互相隔离

镜像仓库：https://hub.docker.com/

# 安装

安装文档：https://docs.docker.com/install/linux/docker-ce/centos/

1. 卸载系统之前的docker

   ```shell
    sudo yum remove docker \
                     docker-client \
                     docker-client-latest \
                     docker-common \
                     docker-latest \
                     docker-latest-logrotate \
                     docker-logrotate \
                     docker-engine
   ```

2. 安装基本的依赖包

   ```shell
    sudo yum install -y yum-utils
   ```

3. 仓库设定

   ```shell
    sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

4. 安装docker引擎

   ```shell
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

# 启动

```shell
sudo systemctl start docker
```

# 检查

1. 查看docker版本

   ```shell
   docker -v
   ```

2. 尝试命令

   ```shell
   sudo docker run hello-world
   ```

# 后续设置

1. 开机自启

   ```shell
   sudo systemctl enable docker
   ```

2. 配置镜像加速（阿里云）

   1. 百度搜索阿里云

   2. 登录

   3. 控制台

      ![image-20210315122437701](https://s2.loli.net/2024/06/01/uwvF6oTmBlzk9an.png)

   4. 镜像服务

      ![image-20210315122531514](https://s2.loli.net/2024/06/01/raAhuGZdK7wJSxT.png)

   5. 加速器

      ![image-20210315132222492](https://s2.loli.net/2024/06/01/EjqDn2FkiSacpdt.png)

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://qihfik4b.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   