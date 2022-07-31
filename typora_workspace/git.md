# 下载

地址：https://npm.taobao.org/mirrors/git-for-windows/v2.30.2.windows.1/

然后疯狂next

# 配置

进入git bash，输入以下

```shell
# 用户名
git config --global user.name "username"
# 邮箱
git config --global user.email "username@email.com"
```

配置ssh免密登录

```shell
ssh-keygen -t rsa -C "邮箱地址"
```

三次回车后生成密钥

查看密钥内容

```shell
cat ~/.ssh/id_rsa.pub
```

然后将密钥内容放入码云中的SSH公钥内

![image-20210315155232615](https://chengdu-edu.oss-cn-chengdu.aliyuncs.com/pic-markdown/image-20210315155232615.png)

测试是否成功

```shell
ssh -T git@gitee.com
```

# 使用

## 新建仓库

![image-20210315155854229](https://chengdu-edu.oss-cn-chengdu.aliyuncs.com/pic-markdown/image-20210315155854229.png)

## 克隆项目

![image-20210315160232871](https://chengdu-edu.oss-cn-chengdu.aliyuncs.com/pic-markdown/image-20210315160232871.png)

![image-20210315160250254](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210315160250254.png)

![image-20210315160307365](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210315160307365.png)

