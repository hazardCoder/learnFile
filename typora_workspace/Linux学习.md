# 一、初始Linux

## （一）文件目录

| 装置                 | Linux中的文件名                  |
| -------------------- | -------------------------------- |
| SCSI/SATA/USB 硬盘机 | /dev/sd[a-p]                     |
| USB 快闪碟           | /dev/sd[a-p]                     |
| Virtl/O 界面         | /dev/vd[a-p]（用于虚拟机内）     |
| 软盘驱动器           | /dev/fd[0-7]                     |
| 打印机               | /dev/lp[0-2]（25针打印机）       |
|                      | /dev/usb/lp[0-15]（USB界面）     |
| 鼠标                 | /dev/input/mouse[0-15]（通用）   |
|                      | /dev/psaux（PS/2界面）           |
|                      | /dev/mouse（当前鼠标）           |
| CDROM/DVDROM         | /dev/scd[0-1]（通用）            |
|                      | /dev/sr[0-1]（通用，CentOS常见） |
|                      | /dev/cdrom（当前 CDROM）         |
| 磁带机               | /dev/ht0（IDE 界面）             |
|                      | /dev/st0（SATA/SCSI界面）        |
|                      | /dev/tape（当前磁带）            |
| IDE硬盘机            | /dev/hd[a-d]（旧式系统才有）     |

## （二）分区

### 分类

1. 主要分区

 	2. 延伸分区
 	3. 逻辑分区

### 图例

![image-20210206160332059](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210206160332059.png)

### 区别

1. 主要分区与延伸分区最多可以有四笔（硬盘限制）

 	2. 延伸分区最多只能有一个(操作系统的限制) 
 	3. 逻辑分区是由延伸分区持续切割出来的分区槽
 	4. 能够被格式化后，作为数据存取的分区槽为**主要分区**与**逻辑分区**。延伸分区无法格式化
 	5. 逻辑分区的数量依操作系统而不同，在Linux 系统中SATA 硬盘已经可以突破63 个以上的分区限制

### 思考

**1. 如果我想将一颗大硬盘『暂时』分区成为四个 partitions，同时还有其他的剩余容量可以让我在未来的时候进行规划， 我能不能分区出四个Primary？若不行，那么你建议该如何分区？** 
答： 

 	1. 由于Primary+Extended 最多只能有四个，其中Extended 最多只能有一个，这个例题想要分区出四个分区槽，且还要预留剩余容量， 因此P+P+P+P 的分区方式是不适合的。因为如果使用到四个P，则即使硬盘还有剩余容量， 因为无法再继续分区，所以剩余容量就被浪费掉了。 
 	2. 假设你想要将所有的四笔记录都花光，那么P+P+P+E 是比较适合的。所以可以用的四个partitions 有3 个主要及一个逻辑分区， 剩余的容量在延伸分区中。
 	3. 如果你要分区超过4 槽以上时，一定要有Extended 分区槽，而且必须将所有剩下的空间都分配给Extended， 然后再以logical 的分区来规划Extended 的空间。 另外，考虑到磁盘的连续性，一般建议将Extended 的磁柱号码分配在最后面的磁柱内。

**2. 假如我的 PC 有两颗 SATA 硬盘，我想在第二颗硬盘分区出 6 个可用的分区槽(可以被格式化来存取数据之用)， 那每个分区槽在Linux 系统下的装置文件名为何？且分区类型各为何？至少写出两种不同的分区方式。**

答：

 1. P+P+P+E 的环境：

    ![image-20210206161016543](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210206161016543.png)

    实际可用的是/dev/sdb1, /dev/sdb2, /dev/sdb3, /dev/sdb5, /dev/sdb6, /dev/sdb7 这六个，至于/dev/sdb4 这个延伸分区本身仅是提供来给逻辑分区槽建立之用。

	2. P+E 的环境：

    ![image-20210206161047332](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210206161047332.png)

    

看到52

# 二、常用命令

## 常用

| 解释                     | 命令                            |
| ------------------------ | ------------------------------- |
| 挂起当前进程             | ctrl+z                          |
| 恢复后台                 | fg                              |
| 软链接                   | ln -s slink source              |
| 硬链接                   | ln link source                  |
| 创建文件                 | touch                           |
| 显示全部文件内容         | cat                             |
| 分页显示文件内容         | more                            |
| 和more相似，可以往前翻页 | less                            |
| 内存数据写入磁盘         | sync                            |
| 显示网络状态信息         | netstat                         |
| 网络IO                   | ifstat                          |
| CPU                      | vmstat                          |
| 内存                     | free                            |
| 硬盘                     | df                              |
| 磁盘IO                   | iostat                          |
| 查看后台任务             | job -l                          |
| 文件查找                 | find 指定目录 指定条件 指定动作 |
| 文件查找                 | whereis 参数/文件名             |
| 文件查找                 | locate 文件名                   |

## RPM

> 解决软件的安装、升级、卸载和库的依赖关系等

| 解释              | 命令                  |
| ----------------- | --------------------- |
| 安装软件          | rpm -ivh filename.rpm |
| 升级软件          | rpm -Uvh filename.rpm |
| 卸载              | rpm -e filename.rpm   |
| 查询软件描述信息  | rpm -qpi filename.rpm |
| 列出软件文件信息  | rpm -qpl filename.rpm |
| 查询文件的rpm属于 | rpm -qf filename      |

## Yum

> 分析出所需软件包以及相关的依赖关系，然后自动从服务器进行下载

| 命令                      | 作用                         |
| ------------------------- | ---------------------------- |
| yum repolist all          | 列出所有仓库                 |
| yum list all              | 列出仓库中所有软件包         |
| yum info 软件包           | 查看软件包信息               |
| yum install 软件包        | 安装软件包                   |
| yum reinstall 软件包      | 重新安装软件包               |
| yum update 软件包         | 升级软件包                   |
| yum remove 软件包         | 移除软件包                   |
| yum clean all             | 清除所有仓库缓存             |
| yum check-update          | 检查可更新的软件包           |
| yum grouplist             | 查看系统中已经安装的软件包组 |
| yum groupinstall 软件包组 | 安装指定的软件包组           |
| yum groupremove 软件包组  | 移除指定的软件包组           |
| yum groupinfo 软件包组    | 查询指定的软件包组信息       |

## 关机

| 命令                 | 功能             |
| -------------------- | ---------------- |
| sync                 | 将数据同步到硬盘 |
| shutdown             | 关机             |
| reboot,halt,poweroff | 重启             |

## 文件权限

| 命令  | 解释             |
| ----- | ---------------- |
| chgrp | 改变文件所属群组 |
| chown | 改变文件拥有者   |
| chmod | 改变文件权限     |

例：

1. 改变test.txt文件的群组users

   ```shell
   chgrp users test.txt
   ```

2. 将test.txt文件的拥有者改为bin

   ```shell
   chown bin test.txt
   ```

3. 改变test.txt的权限，其中

   > r=4,w=2,x=1
   >
   > user=u,group=g,others=o,all=a

   ```shell
   chmod 754 test.txt
   chmod u=rwx,g=rx,o=r test.txt
   # 为所有用户加写权限
   chmod a+w test.txt
   # 为所有用户去除写权限
   chmod a-w test.txt
   ```




# 三、零碎知识点

## (一)文件权限

![image-20210211122146751](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210211122146751.png)

* 其中档案类型：

  | 字符 | 备注                       |
  | ---- | -------------------------- |
  | d    | 目录                       |
  | -    | 文件                       |
  | l    | 连接档（link file）        |
  | b    | 可供存储接口设备           |
  | c    | 串行端口设备，如键盘，鼠标 |

  看到222

## (二)网络配置

如果搞不清楚，那就一开始先重置

![image-20210227181613565](https://s2.loli.net/2022/08/01/V5pmCLqhNnOXTDi.png)

记住网段

![image-20210227181803221](https://s2.loli.net/2022/08/01/g3QBiRDx14FOYuH.png)

比如这里就是192.168.72.XX

进入虚拟机后，进入目录

```shell
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

设置如图

![image-20210227182005250](https://s2.loli.net/2022/08/01/1wQOV34exIKA8N9.png)

重启完事。



