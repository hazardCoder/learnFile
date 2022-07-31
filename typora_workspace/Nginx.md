# 一、概述
> Nginx是一个高性能的HTTP和反向代理服务器，特点是占有内存少，并发能力强，支持热部署

> 专为性能优化而开发，能够经受高负载的考验，能支持高达50,000个并发连接数

# 二、概念
### 1.反向代理
> **正向代理**：依赖代理服务器去访问目标网页（找中间人），需要在客户端（浏览器）配置代理服务器。

> **反向代理**：客户端对代理是==无感知==的，客户端不需要任何配置就可以访问，只需要将请求发送到**反向代理服务器**，由反向代理服务器去选择目标服务器获取数据后，再返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，++暴露的是代理服务器的地址，隐藏了真实服务器的IP地址++

### 2.负载均衡
> 增加服务器数量，将请求分发到各个服务器上，将原先请求解中到单个服务器的情况改为将请求分发到多个服务器上，将负载分到不同的服务器，也就是**负载均衡**

### 3.动静分离
* **目的**：加快网站解析速度
* **方法**：把动态页面和静态页面由不同的服务器来解析，加快解析速度，降低原来单个服务器的压力

# 三、安装（Linux环境）
### 1.使用secureCRT（远程连接工具）连接上Linux
* 确保Linux可以联网，能ping通baidu

```
ping www.baidu.com
```
* 通过输入IP地址，账号和密码可以进行远程连接
* 查看IP地址

```
ifconfig
```
### 2.安装依赖包
#### 1）包括：

```
pcre
openssl
zlib
```

#### 2）下载方法
* **方式一**：
    * 将以下命令输入到远程控制台上回车运行（要先将目录切换到要下载的目录下）

    ```
    wget https://netix.dl.sourceforge.net/project/pcre/pcre/8.40/pcre-8.40.tar.gz
    ```
    * 解压缩
    
    ```
    tar -zxvf   pcre-8.40.tar.gz
    ```
    * 完成后，进入到解压文件中，执行
      
    ```
    进入：cd pcre-8.40/
    
    执行：./configure
    ```

    * 编译并安装
    
    ```
    make && make install
    ```
    * 查看版本号
    
    ```
    pcre-config --version
    ```
* **方式二**：
    * 执行以下命名，下载并安装
    
    ```
    yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
    ```
### 3.nginx下载安装
* [下载地址](http://nginx.org/download/)，下载nginx-1.12.2.tar.gz版本
* 安装
    * 将安装包放到虚拟机中
    * 解压缩安装包
    * 进入解压缩目录，执行
    ```
    ./configure
    ```
    * 执行
    ```
    make && make install
    ```
* 安装完成后，可以在/usr/local中发现nginx文件夹，其中就有nginx的启动脚本，在/nginx/sbin中
# 四、启动nginx
* 进入目录
```
cd /usr/local/nginx/sbin/
```
* 启动

```shell
./nginx
./nginx -c /usr/local/nginx/conf/nginx.conf #指定配置文件启动
```
* 查看进程

```
ps -ef | grep nginx
```
# 五、谈谈配置文件
### 1. 配置文件位置

```
/usr/local/nginx/conf/nginx.conf
```
### 2. 配置文件组成
#### 1）第一部分：全局块

> 负责服务器整体运行的配置指令

> work_processes：值越大，说明可处理的并发量越多，但要受到硬件、软件的限制

#### 2）第二部分：events块

> 影响服务器和用户之间的网络连接

> worker_connections：支持的最大连接数

#### 3）第三部分：http块

> 配置中最频繁的部分，代理、缓存和日志等功能

##### a. http全局块

> 包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单连接请求数上限等

##### b. server块

> 与虚拟主机有密切关系，又分为两个部分

###### · 全局server
###### · location部分

# 六、配置实例
### 1.反向代理

> 达到的效果：浏览器中输入地址，然后使之跳转到tomcat主界面

* **准备工作**：
    * 在Linux系统上安装tomcat
    * 对外开放端口并重启防火墙（第七部分）
    * 在windows系统中通过浏览器访问tomcat服务器进行测试
* **具体配置**
    * 在windows系统的host文件进行域名和ip对应关系的配置
    
    ```
    host目录:C:\Windows\System32\drivers\etc\hosts
    ```
    * 进入文件后，在最后一行添加IP地址+域名
    
    ```
    例：192.168.0.94 www.123.com
    ```
    * 在nginx进行请求转发的配置（反向代理），在配置文件中的server部分中的location部分
     ```
    root html;
    proxy_pass http://127.0.0.1:8080;
    index index.html index.htm;
     ```
    * 如需多个端口监听进行反向代理，复制粘贴server部分重写listen
    
    ```xml
    server{
        listen 9001;
        server_name localhost;
        
        #~/edu/代表正则表达式，如果目录下含有edu，则会进行访问
        location ~/edu/{     //想转发的地址
            proxy_pass http://127.0.0.1:8080;     //转发后的地址  
        }
        location ~/vod/{
            proxy_pass http://127.0.0.1:8081;
        }
    }
    ```
    
    * 启动nginx进行测试
    
### 2.负载均衡

> 达到的效果：在地址栏输入网址，使流量平均到分配的端口

* **准备工作**：
    * 两台tomcat服务器（不同端口）
    * 在tomcat里面的webapp目录中，创建页面（a.html），用于测试
* **具体配置**：
    * 同样是在上述反向代理配置文件中进行配置
    
    ```xml
    http{
        ....
        upstream myserver{
            //ip_hash;
            //fair;
            server ip地址:端口 weight=1;
            server ip地址:端口 weight=1;
        }
        ...
        server{
            location /{
                ...
                server_name  IP地址（别用localhost）
                proxy_pass http://myserver;   //通过myserver这个名称去定位负载的地址群
                //proxy_connect_timeout 10;
            }
        }
    }
    ```
* **分配策略**：
    * 轮询（默认）
    
    > 每个请求按事件顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除
    
    * weight（权重）
    
    > weight代表权重，默认为1，权重越高代表被分配的客户端越多
    
    * ip_hash
    
    > 每个请求按访问的ip的hash结果分配，这样每个访客固定一个后端服务器，可以解决session问题。

    > 就是指：后续的这个IP地址访问的服务器始终是第一次访问的那个服务器
    
    * fair（第三方）
    
    > 按后端服务器的响应时间进行分配，响应时间短的优先分配

### 3.动静分离

```
动态请求：客户端->nginx->tomcat
静态请求：客户端->nginx->静态资源服务器(html,css,image...)
```
* **方式**：
  
    * 事前分离：纯粹把静态文件独立成一个单独的域名，放在独立的服务器上（**主流**）
    * 事中分离：混合发布，通过nginx分开
    
* **准备工作**
  
    * 在linux系统中设置一些静态资源 
* **具体配置**
    * 配置文件中：
    
    ```xml
    更改location如下
    
    location /www/{
        root /data/;
        index index.html index.htm;
    }
    location /image/{
        root /data/;
        autoindex on;    //列出当前文件夹中的内容
    }
    ```
* **测试**
  
    * 输入地址:
    
    ```
    http://IP地址/image/01.jpg
    http://IP地址/www/a.html
    ```

# 七、其他
### 1. 查看端口
* 位于/usr/local/nginx/conf/nginx.conf，可以执行命令进入

```shell
vi /usr/local/nginx/conf/nginx.conf
```
* 进入后可以找到server模块中的listen，默认是80端口，可以通过浏览器进行访问(ip地址+端口，80端口可以省略不写)

### 2. 防火墙设置
* 查看开放的端口号

```shell
firewall-cmd --zone=public --list-ports
```
* 设置开放的端口号

```shell
firewall-cmd --add-service=http -permanent
sudo firewall-cmd --add-port=80/tcp --permanent
```
* 重启防火墙

```shell
firewall-cmd --reload
```

### 3. 常用命令


>注意：需要进入到/usr/local/nginx/sbin目录中输入命令

* 查看nginx版本号

```shell
./nginx -v
```

* 启动nginx

```shell
./nginx
```

* 关闭nginx

```shell
./nginx -s stop
```

* 重新加载nginx

```shell
./nginx -s reload
```