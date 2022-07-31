# 1.前提

## 1.微服务技术

| 微服务条目       | 落地技术                      |
| ---------------- | ----------------------------- |
| 服务开发         | SpringBoot、Spring、SpringMVC |
| 服务注册发现     | Eureka、Consul、Zookeeper     |
| 服务调用         | RPC、Rest、gRPC               |
| 服务熔断         | Hystrix、Envoy                |
| 负载均衡         | Nginx、Ribbon                 |
| 服务接口调用     | Feign                         |
| 消息队列         | Kafka、RabbitMQ、ActiveMQ     |
| 服务配置中心     | SpringCloudConfig、Apollo     |
| 服务路由（网关） | Zuul、Gateway                 |
| 全链路追踪       | Zipkin                        |
| 服务部署         | Docker、Kubemetes             |
| 数据流操作开发包 | SpringCloud Stream            |
| 消息总线         | SpringCloud Bus               |



## 2.学习环境

| 环境          | 版本          |
| ------------- | ------------- |
| springboot    | 2.2.2RELEASE  |
| springcloud   | Hoxton.SR1    |
| cloud alibaba | 2.1.0.RELEASE |
| MAVEN         | 3.5及以上     |
| Mysql         | 5.7及以上     |
| jdk           | 1.8           |

## 3.技术概况

![image-20210117184720528](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117184720528.png)

# 2.初步开始

## 1.pom.xml

> dependencyManagement和dependencies区别：
>
> 1. Maven使用dependencyManagement元素提供一种管理依赖版本号的方式，<u>通常会在一个组织或者项目最顶层的父POM中看到这个元素</u>，使子项目和父项目的版本号一致，除非子项目自己声明一个版本<version>
> 2. dependencyManagement里只是声明依赖，**并不实现引入**，因此子项目要显示的声明要用的依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.study.springcloud</groupId>
    <artifactId>cloud2021</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- packaging pom 总工程-->
    <!--统一管理jar包版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>12</maven.compiler.source>
        <maven.compiler.target>12</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <lombok.version>1.18.10</lombok.version>
        <log4j.version>1.2.17</log4j.version>
        <mysql.version>5.1.47</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!--子模块继承之后，提供作用：锁定版本+子module不用写groupId和version-->
    <dependencyManagement><!--定义规范，但不导入-->
        <dependencies>
            <dependency>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-project-info-reports-plugin</artifactId>
                <version>3.0.0</version>
            </dependency>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud 阿里巴巴-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--mysql-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
                <scope>runtime</scope>
            </dependency>
            <!-- druid-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!--mybatis-->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <!--log4j-->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <!--热启动插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## 2.模块开发步骤

1. 建module
2. 改POM
3. 写YML
4. 主启动
5. 业务类

# 3.生产者模块

## 1. 创建子模块

右击父工程，创建module，不选框架，进行创建

## 2.改POM

> 更改为一下POM内容
>
> 提示：可能会出现自动导包出现问题的情况，此时我的解决方法是在控制台输入mvn install，然后再reimport

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

## 3.写yml

在子模块的src/main/resources下创建application.yml

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service  #服务名称
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver    #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2021?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: dangerous123

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities  # 所有Entity别名类所在包

```

## 4.创建主启动类

在src/main/java/包名/下创建java文件，设置为主启动类

<img src="C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117222504126.png" alt="image-20210117222504126" style="zoom:80%;" />

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PaymentMain8001 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }

}

```

## 5.业务类

### 1.建表SQL

```sql
CREATE TABLE payment
(
	id BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT 'ID',
	serial VARCHAR(200) DEFAULT '',
	PRIMARY KEY(id)
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8
```

### 2.建立实体类

#### 1.表实体

```java
package com.atguigu.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Data    //代替get、set方法
@AllArgsConstructor  //全参
@NoArgsConstructor   //空参
public class Payment implements Serializable {
    private Long id;
    private String serial;
}

```

#### 2.返回实体

> 这里要注意，需要安装lombok插件，不然this的地方可能会报错

```java
package com.atguigu.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data    //代替get、set方法
@AllArgsConstructor  //全参
@NoArgsConstructor   //空参
public class CommonResult<T> {
    private Integer code;
    private String message;
    private T data;

    public CommonResult(Integer code, String message) {
        this(code,message,null);
    }
}

```

### 3.dao层

#### 1.创建dao的java文件

```java
package com.atguigu.springcloud.dao;

import com.atguigu.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

@Mapper
public interface PaymentDao {

    public int create(Payment payment);

    public Payment getPaymentById(@Param("id") Long id);
}

```

#### 2.创建mapper配置文件

> 创建的位置可以再application.yml中查看，目前需要配置在resources/mapper下

<img src="C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117225920733.png" alt="image-20210117225920733" style="zoom:80%;" />

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http:/www.mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.atguigu.springcloud.dao.PaymentDao">
    <!--因为在application.yml中配置了别名包，所以这里传参可以直接写Payment,keyProperty是主键指定-->
    <insert id="create" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
        insert into payment(serial) values(#{serial});
    </insert>

    <!--别名映射-->
    <resultMap id="BaseResultMap" type="com.atguigu.springcloud.entities.Payment">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <id column="serial" property="serial" jdbcType="VARCHAR"/>
    </resultMap>
    <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
        select * from payment where id = #{id};
    </select>

</mapper>
```

### 4.Service层

#### 1.抽象类

```java
package com.atguigu.springcloud.service;

import com.atguigu.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Param;

public interface PaymentService {

    public int create(Payment payment);

    public Payment getPaymentById(@Param("id") Long id);
}

```

#### 2.实现类

```java
package com.atguigu.springcloud.dao.impl;

import com.atguigu.springcloud.dao.PaymentDao;
import com.atguigu.springcloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
public class PaymentServiceImpl implements PaymentService {
    
    @Resource
    private PaymentDao paymentDao;
    
    @Override
    public int create(Payment payment) {
        return paymentDao.create(payment);
    }

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDao.getPaymentById(id);
    }
}

```

### 5.Controller层

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    // 尽量写用post
    @PostMapping(value = "/payment/create")
    public CommonResult create(@RequestBody Payment payment) {
        int result = paymentService.create(payment);
        log.info("******插入结果,{}",result);

        if (result > 0) {
            return new CommonResult(200,"插入数据成功",result);
        } else {
            return new CommonResult(444,"插入数据失败",null);
        }
    }

    // 尽量读用get
    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        log.info("******查询结果,{}",payment);

        if (payment != null) {
            return new CommonResult(200,"查询成功",payment);
        } else {
            return new CommonResult(444,"没有对应记录，查询id："+id,null);
        }
    }
}

```

# 4. Devtools热部署

> 代码发生变化时，会自动重启，只允许在开发阶段使用

## 1. 在子模块中引入依赖

已经在上述依赖中引入

## 2.在父模块中添加插件

已经在上述插件中引入

## 3.设置

![image-20210117235243659](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117235243659.png)

## 4.更新

快捷键:ctrl+shift+Alt+/,然后选择

<img src="C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117235421854.png" alt="image-20210117235421854" style="zoom:80%;" />

开启

![image-20210117235725906](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117235725906.png)

## 5.重启IDEA

# 5.消费者订单模块

> 和生产者步骤相似，只记录不同点

## 1.pom

> 消费者用不到数据库的相关内容，所以要把关于操作数据的依赖去除，不然在启动时会报错

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-order80</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```



## 2.yml

```yml
server:
  port: 80
```

## 3.业务类

> 只有controller层和实体类，还有restTemplate的配置类

### 1.配置类

> 位于config包下

![image-20210123173945263](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210123173945263.png)

```java
package com.atguigu.springcloud.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}

```

### 2.Contoller层

> 访问链接后期优化，现在先写死

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://localhost:8001";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create",payment,CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }
}

```

### 3.启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

@SpringBootApplication(exclude= {DataSourceAutoConfiguration.class})
public class OrderMain80 {

    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }
}

```

# 6.工程重构

## 1.提重复代码

1. 新建模块

2. 改pom

   ```java
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>cloud2021</artifactId>
           <groupId>com.study.springcloud</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>cloud-api-commons</artifactId>
   
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-devtools</artifactId>
               <scope>runtime</scope>
               <optional>true</optional>
           </dependency>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <optional>true</optional>
           </dependency>
           <!---->
           <dependency>
               <groupId>cn.hutool</groupId>
               <artifactId>hutool-all</artifactId>
               <version>5.5.7</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

3.将公共代码复制到新模块中

4.分别点击新模块maven工程中的clean和install

5.删除其余模块中公共部分代码

6.引入包，在其他模块中引入刚刚的打包，在pom中添加

```xml
<!--引入自己定义的api通用包，可以使用Payment支付Entity-->
<dependency>
    <groupId>com.study.springcloud</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

7.更改8001项目中的mapper，有一些映射错误

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http:/www.mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.atguigu.springcloud.dao.PaymentDao">
    <!--因为在application.yml中配置了别名包，所以这里传参可以直接写Payment,keyProperty是主键指定-->
    <insert id="create" parameterType="com.atguigu.spring.cloud.entities.Payment" useGeneratedKeys="true" keyProperty="id">
        insert into payment(serial) values(#{serial});
    </insert>

<!--    别名映射-->
    <resultMap id="BaseResultMap" type="com.atguigu.spring.cloud.entities.Payment">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <id column="serial" property="serial" jdbcType="VARCHAR"/>
    </resultMap>
    <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
        select * from payment where id = #{id};
    </select>

</mapper>
```



 # 7.Eureka

> 服务注册中心，保证了AP，不保证强一致性

## 1.1.X和2.X的对比

**以前**

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

**现在**

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

## 2.创建新模块

## 3.写模块yml

```yml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost   #eureka服务端的实例名称
  client:
    # false表示不向注册中心注册自己
    register-with-eureka: false
    #false表示自己端就是注册中心，职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url: 
      #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

```

## 4.主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}

```

## 5.访问eureka页面

输入localhost:7001

## 6.注册服务

### 1.1.X和2.X的对比

**以前**

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

**现在**

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### 2.改pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>

    <dependencies>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

### 3.改yml

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service  #服务名称
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver    #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2021?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: dangerous123

eureka:
  client:
    # 表示是否将自己注册进EurekaServer，默认为true
    register-with-eureka: true
    # 是否从EurekaServer抓取已有的注册信息，默认为true，单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities  # 所有Entity别名类所在包


```

### 4.改主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }

}

```

### 5.启动后可发现

![image-20210123235928128](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210123235928128.png)

### 6.同样将80项目入住eureka

![image-20210124000412023](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210124000412023.png)

## 7.Eureka集群

> 防止单点故障
>
> 功能：负载均衡+负载容错
>
> 实现：互相注册，相互守望，将集群看成一个整体

### 7.1 新建一个EurekaServer

### 7.2 修改映射文件

 1. 找到C:\Windows\System32\drivers\etc下的hosts文件

 2. 增加内容

    > 127.0.0.1 eureka7001.com
    >
    > 127.0.0.1 eureka7002.com

### 7.3 改yml

1. 7001项目yml

   ```yml
   server:
     port: 7001
   eureka:
     instance:
       hostname: eureka7001.com   #eureka服务端的实例名称
     client:
       # false表示不向注册中心注册自己
       register-with-eureka: false
       #false表示自己端就是注册中心，职责就是维护服务实例，并不需要去检索服务
       fetch-registry: false
       service-url:
         #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
         defaultZone: http://eureka7002.com:7002/eureka/
   
   ```

 2. 7002项目yml

    ```yml
    server:
      port: 7002
    eureka:
      instance:
        hostname: eureka7002.com   #eureka服务端的实例名称
      client:
        # false表示不向注册中心注册自己
        register-with-eureka: false
        #false表示自己端就是注册中心，职责就是维护服务实例，并不需要去检索服务
        fetch-registry: false
        service-url:
          #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
          defaultZone: http://eureka7001.com:7001/eureka/
    
    ```

    

### 7.4 更改其他项目yml

以8001为例，其他一样

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service  #服务名称
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver    #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2021?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: dangerous123

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities  # 所有Entity别名类所在包

eureka:
  client:
    # 表示是否将自己注册进EurekaServer，默认为true
    register-with-eureka: true
    # 是否从EurekaServer抓取已有的注册信息，默认为true，单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
#      defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka    #集群版

```

## 8.服务提供者集群

### 8.1 新建模块8002

> 参照8001模块

![image-20210210180101691](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210180101691.png)

### 8.2 修改8001/8002的控制层

![image-20210210181106509](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210181106509.png)

```java
package com.atguigu.springcloud.controller;

import com.atguigu.spring.cloud.entities.CommonResult;
import com.atguigu.spring.cloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    // 尽量写用post
    @PostMapping(value = "/payment/create")
    public CommonResult create(@RequestBody Payment payment) {
        int result = paymentService.create(payment);
        log.info("******插入结果,{}",result);

        if (result > 0) {
            return new CommonResult(200,"插入数据成功,端口号为："+serverPort,result);
        } else {
            return new CommonResult(444,"插入数据失败",null);
        }
    }

    // 尽量读用get
    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        log.info("******查询结果,{}",payment);

        if (payment != null) {
            return new CommonResult(200,"查询成功,端口号为："+serverPort,payment);
        } else {
            return new CommonResult(444,"没有对应记录，查询id："+id,null);
        }
    }
}

```

### 8.3 结果

![image-20210210181337608](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210181337608.png)

### 8.4 目前问题

> 消费者访问接口时，调用的端口总是8001，没有达到负载均衡，集群的效果。

![image-20210210181922101](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210181922101.png)

**原因**：消费者代码中访问位置写死了

![image-20210210182126412](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210182126412.png)

**更改**:

```JAVA
package com.atguigu.springcloud.controller;

import com.atguigu.spring.cloud.entities.CommonResult;
import com.atguigu.spring.cloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderController {

//    public static final String PAYMENT_URL = "http://localhost:8001";
    public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create",payment,CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }
}

```

和配置类中增加负载均衡功能(默认轮询)：

```java
package com.atguigu.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced    // 负载均衡
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}

```

### 8.5 完善细节

完善前：

![image-20210210190123745](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210190123745.png)

完善后：

![image-20210210190619266](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210210190619266.png)

**实现**：

在服务yml中增加

```yml
  instance:
    instance-id: payment8001     #设置后面状态名
    prefer-ip-address: true      #访问路径显示IP地址
```

完整：

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service  #服务名称
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  #当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver    #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2021?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: dangerous123

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities  # 所有Entity别名类所在包

eureka:
  client:
    # 表示是否将自己注册进EurekaServer，默认为true
    register-with-eureka: true
    # 是否从EurekaServer抓取已有的注册信息，默认为true，单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
#      defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka    #集群版
  instance:
    instance-id: payment8001     #设置后面状态名
    prefer-ip-address: true      #访问路径显示IP地址

```

## 9.服务发现Discovery

> 对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息

**实现**：

以8001为例，增加了DiscoveryClient和信息打印接口

```java
package com.atguigu.springcloud.controller;

import com.atguigu.spring.cloud.entities.CommonResult;
import com.atguigu.spring.cloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import java.util.List;

@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @Resource
    private DiscoveryClient discoveryClient;

    // 尽量写用post
    @PostMapping(value = "/payment/create")
    public CommonResult create(@RequestBody Payment payment) {
        int result = paymentService.create(payment);
        log.info("******插入结果,{}",result);

        if (result > 0) {
            return new CommonResult(200,"插入数据成功,端口号为："+serverPort,result);
        } else {
            return new CommonResult(444,"插入数据失败",null);
        }
    }

    // 尽量读用get
    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        log.info("******查询结果,{}",payment);

        if (payment != null) {
            return new CommonResult(200,"查询成功,端口号为："+serverPort,payment);
        } else {
            return new CommonResult(444,"没有对应记录，查询id："+id,null);
        }
    }

    // 打印服务的各类信息
    @GetMapping(value = "/payment/discovery")
    public Object discovery() {

        // 获得服务清单列表
        List<String> services = discoveryClient.getServices();
        for (String service : services) {
            log.info("*********element:"+service);
        }

        // 获得指定微服务的实例清单列表
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
}

```

并在主启动类中增加注解@EnableDiscoveryClient

```JAVA
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }

}

```

**结果**：

![image-20210211231047802](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210211231047802.png)

## 10.Eureka自我保护

![image-20210211231057814](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210211231057814.png)

> 当某个微服务在一定时间内不可用时，不会删除该微服务，默认开启，是高可用的设计思想。

**禁用方法**：

在eureka服务中(application.yml)中增加如下语句

```yml
server:
    # 关闭自我保护机制，保证不可用服务及时从微服务中删除
    enable-self-preservation: false
    # 2秒钟不发送心跳则删除微服务
    eviction-interval-timer-in-ms: 2000
```

![image-20210214202107756](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210214202107756.png)

**客户端心跳配置讲解**：

```yml
# Eureka客户端向服务端发送心跳的时间间隔，单位秒（默认：30）
lease-renewal-interval-in-seconds: 1
# Eureka服务端在收到最后一次心跳后等待的时间上限，单位为秒（默认90），超时则删除服务
lease-expiration-duration-in-seconds: 2
```

![image-20210214202553886](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210214202553886.png)

# 8. Zookeeper

## 1.CentOS7上安装Zookeeper

### 1.下载安装包

```shell
wget -c https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.5.9/apache-zookeeper-3.5.9-bin.tar.gz
```

### 2. 转移并解压

```shell
> mv apache-zookeeper-3.5.9-bin.tar.gz /usr/local/zookeeper/apache-zookeeper-3.5.9-bin.tar.gz
>
> tar -zxvf apache-zookeeper-3.5.9-bin.tar.gz
```

### 3. 配置

进入目录

```shell
cd /usr/local/zookeeper/apache-zookeeper-3.5.9-bin/conf
```

配置和创建快照和日志文件

```shell
cp zoo_sample.cfg zoo.cfg

#位于/usr/local/zookeeper/apache-zookeeper-3.5.9-bin
mkdir data
mkdir logs
```

配置环境变量 /etc/profile

> export ZOOKEEPER_HOME=/usr/local/zookeeper/zookeeper-3.4.10/ export PATH=$ZOOKEEPER_HOME/bin:$PATH 
>
> export PATH

变量生效

```shell
source /etc/profile
```

修改配置(zoo.cfg)

>修改zoo.cfg文件
>tickTime=2000
>zookeeper中最小的时间单位长度 （ms）
>initLimit=10
>follower节点启动后与leader节点完成数据同步的时间
>syncLimit=5
>leader节点和follower节点进行心跳检测的最大延时时间
>dataDir=/usr/local/zookeeper/apache-zookeeper-3.5.9-bin/data
>dataLogDir = /usr/local/zookeeper/apache-zookeeper-3.5.9-bin/logs
>zookeeper服务器存储快照文件的目录
>dataLogDir
>配置 zookeeper事务日志的存储路径，默认指定在dataDir目录下
>clientPort
>客户端和服务端建立连接的端口号： 2181

### 4. 服务

```shell
启动服务：zkServer.sh start
查看状态：zkServer.sh status
关闭服务：zkServer.sh stop
重启服务：zkServer.sh restart
```

## 2. zookeeper替换eureka

### 1.关闭防火墙

```shell
# 关闭
systemctl stop firewalld
# 查看状态
systemctl status firewalld
```

### 2. 新建8004模块

#### 1.改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8004</artifactId>

    <dependencies>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--SpringBoot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--SpringBoot 整合 zookeeper客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

#### 2.改YML

```yml
server:
  port: 8004

spring:
  application:
    name: cloud-payment-service  #服务名称
  cloud:
    zookeeper:
      connect-string: 192.168.72.94:2181
```

#### 3.主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient //该注解用于像使用consul或zookeeper作为注册中心时注册服务
public class PaymentMain8004 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8004.class, args);
    }

}
```

#### 4.Controller

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @PostMapping(value = "/payment/zk")
    public String create() {
        return "springcloud with zookeeper:"+serverPort+"\t"+ UUID.randomUUID().toString();
    }
}

```

### 3.启动zookeeper

进入到zookeeper的bin目录下，执行

```shell
./zkServer.sh start
./zkCli.sh
```

成功启动结果

![image-20210301211410783](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301211410783.png)

启动后，可查看当前有的结点

```shell
ls /
```

![image-20210301211609748](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301211609748.png)

### 4.启动8004模块

这里可能会产生jar包冲突

### 5.查看

```shell
ls /
```

![image-20210301212039082](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301212039082.png)

![image-20210301212104639](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301212104639.png)

### 6.访问

> http://localhost:8004/payment/zk

![image-20210301212327668](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301212327668.png)

成功！

### 7.思考

临时节点：发送心跳一段时间后没有收到回应则会直接删掉服务

持久结点：发送心跳一段时间后没有收到回应还会保留一段时间服务

zookeeper是**临时结点**

## 3.订单服务注册zookeeper

### 1. 新建cloud-consumerzk-order80模块

### 2.改POM

依赖和8004模块相同

### 3.改YML

```yml
server:
  port: 80
spring:
  application:
    name: cloud-consumer-order
  cloud:
    zookeeper:
      connect-string: 192.168.72.94:2181

```

### 4.主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class OrderZKMain80 {

    public static void main(String[] args) {
        SpringApplication.run(OrderZKMain80.class,args);
    }
    
}

```

### 5.Config

```java
package com.atguigu.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}

```

### 6.Controller

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderZKController {

    public static final String INVOKE_URL = "http://cloud-payment-service";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/zk")
    public String paymentInfo() {
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/zk",String.class);
        return result;
    }
}

```

### 6.启动观察

![image-20210301214458471](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210301214458471.png)

成功！

# 9. Consul

这还是个服务注册中心

## 1. 介绍

> consul.io/intro/index.html
>
> springcloud.cc/spring-cloud-consul.html   //中文学习页面

## 2. 功能特性

1. 服务发现
2. 健康检查
3. 键值对存储
4. 安全服务加固
5. 多数据中心

![image-20210302231435375](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210302231435375.png)

## 3. 安装

官网安装说明

> https://learn.hashicorp.com/consul/getting-started/install.html

### 1. 下载

> Linux环境

```shell
wget https://releases.hashicorp.com/consul/1.9.3/consul_1.9.3_linux_amd64.zip

unzip consul_1.9.3_linux_amd64.zip

cp consul /usr/local/bin/
```

### 2. 检查

```shell
consul version
```

![image-20210303155755261](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210303155755261.png)

### 3. 启动

```shell
consul agent -data-dir /tmp/node0 -node=node0 -bind=192.168.72.94 -datacenter=dc1 -ui -client=192.168.72.94 -server -bootstrap-expect 1
```

### 4. 验证

> 192.168.72.94:8500

![image-20210303160647128](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210303160647128.png)

## 4. 服务提供者注册进consul

### 1. 新建模块

cloud-providerconsul-payment8006

### 2.改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-providerconsul-payment8006</artifactId>

    <dependencies>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--SpringBoot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--SpringBoot consul server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

### 3. 改YML

```yml
server:
  port: 8006
spring:
  application:
    name: consul-provider-payment
    # consul注册中心地址
  cloud:
    consul:
      host: 192.168.72.94
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true  #允许检查心跳
```

### 4. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8006 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8006.class,args);
    }

}

```

### 5. Controller

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
@Slf4j
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    @RequestMapping(value = "/payment/consul")
    public String paymentConsul() {
        return "springcloud with consul:"+serverPort+"\t"+ UUID.randomUUID().toString();
    }
}

```

## 5. 消费者注册进consul

### 1.新建模块

cloud-consumerconsul-order80

### 2. 改POM

和8006的pom相同

### 3. 改YML

```yml
server:
  port: 80
spring:
  application:
    name: consul-consumer-order
    # consul注册中心地址
  cloud:
    consul:
      host: 192.168.72.94
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true    #允许检查心跳

```

### 4. 主启动类

不赘述

### 5. Bean

同zookeeper中的消费者相同

### 6. Controller

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderConsulController {
    public static final String INVOKE_URL = "http://consul-provider-payment";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/consul")
    public String paymentInfo() {
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/consul",String.class);
        return result;
    }
}

```

### 7. 验证

![image-20210303163630962](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210303163630962.png)

# 10. 三个注册中心异同点

![image-20210303164112463](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210303164112463.png)

CAP理论的核心是：一个分布式系统不可能同时很好的满足**一致性**，**可用性**和**分区容错性**这三个需求，

最多只能同时较好的满足两个。

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：

1. CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。

2. CP - 满足一致性，分区容忍性的系统，通常性能不是特别高。

3. AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

![image-20210303163933474](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210303163933474.png)

# 11. Ribbon

## 1. 概述

> 官网：github.com/Netflix/ribbon/wiki/Getting-Started

实现：负载均衡（LB）+RestTemplate调用

## 2. 功能

> 客户端、负载均衡、服务调用的工具

## 3. 与Nginx区别

1. Nginx是**服务器**负载均衡，客户端所有请求都会交给nginx，然后由nginx实现转发请求。即负载均衡是由服务端实现的（集中式LB）
2. Ribbon**本地**负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而本地实现RPC远程服务调用技术（进程内LB）

## 4. Ribbon使用

新版的Eureka中带有ribbon的依赖包，所以可以不用加Ribbon的依赖包

上述笔记中的Eureka集群就是用了Ribbon的负载均衡

## 5. 负载规则

* 轮询
* 随机
* 先按轮询获取服务，获取失败后在指定时间再进行重试
* 权重，相应速度越快的实例权重越大
* 先过滤多次故障断路器跳闸的服务，然后选择一个并发量小的服务
* 先过滤掉故障实例，再选择并发较小实例
* 默认规则，符合判断server所在区域的性能和server的可用性选择服务器

## 6. 修改规则配置

> 以order80为例

### 1. 注意配置细节

官方文档明确给出警告：

​	自定义配置类不能够放在@ComponentScan所扫描的当前包下以及子包下，否则我们自定义的配置类就会被所有的Ribbon客户端所共享，达不到特殊化定制的目的了。

### 2. 新建packeage

com.atguigu.myrule

![image-20210304222736258](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210304222736258.png)

### 3. 新建规则类

![image-20210304222803959](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210304222803959.png)

```java
package com.atguigu.myrule;

import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MySelfRule {

    @Bean
    public IRule myRule() {
        return new RandomRule();
    }

}
```

### 4. 主启动类增加注解RibbonClient

```java
package com.atguigu.springcloud;

import com.atguigu.myrule.MySelfRule;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.ribbon.RibbonClient;

@SpringBootApplication
@EnableEurekaClient
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MySelfRule.class)
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }
}
```

# 12. OpenFeign

## 1. 概述

是一个声明式的Web服务客户端，让编写Web服务客户端变得非常容易，**只需要创建一个接口并在接口上添加注解即可**。

相当于对RestTemplate得http请求进行了封装，简化了使用Ribbon时，自动封装服务调用客户端得开发量。

实际上就是个服务接口绑定器，将已经开发的接口的功能绑定到另一个接口上，使其拥有同样的功能，从而实现接口调用。

## 2. 功能

服务调用

因为整合了Ribbon，所以也有负载均衡

## 3. 实现

> 微服务调用接口+@FeignClient

### 1. 新建模块cloud-consumer-feign-order80

![image-20210306175434487](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210306175434487.png)

### 2. 改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-feign-order80</artifactId>

    <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

### 3. 改YML

```yml
server:
  port: 80
eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/

```



### 4. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients   //激活开启
public class OrderFeignMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderFeignMain80.class,args);
    }
}

```

### 5. 业务类

#### 1. service

![image-20210306180752788](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210306180752788.png)

```java
package com.atguigu.springcloud.service;

import com.atguigu.spring.cloud.entities.CommonResult;
import com.atguigu.spring.cloud.entities.Payment;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")   //微服务名称
public interface PaymentFeignService {

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
}

```

#### 2. controller

![image-20210306181116032](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210306181116032.png)

```java
package com.atguigu.springcloud.controller;

import com.atguigu.spring.cloud.entities.CommonResult;
import com.atguigu.spring.cloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentFeignService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderFeignController {

    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping(value = "/consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        return paymentFeignService.getPaymentById(id);
    }
}
```

## 4. 超时控制

openfeign-ribbon:客户端一般默认等待1秒钟，即客户端在1秒钟之内拿不到结果，则会产生超时报错。

若想自定义超时长度，则可以在yml文件中进行配置

```yml
server:
  port: 80
eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/

ribbon:
# 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
# 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```

## 5. 日志打印

> Feign提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解Feign中Http请求的细节。
>
> 就是对Feign接口的调用情况进行监控和输出

### 1. 配置类

![image-20210306185026962](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210306185026962.png)

```java
package com.atguigu.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}

```

### 2. YML中再开启

```yml
server:
  port: 80
eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/

ribbon:
# 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
# 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.atguigu.springcloud.service.PaymentFeignService: debug

```

# 13. Hystrix

## 1. 概述

官网：https://github.com/Netflix/Hystrix/wiki/How-To-Use

> 主要解决分布式系统**延迟**和**容错**的开源库，使不会因局部故障导致整体服务失败，避免级联，提高分布式系统的弹性。既也可以使用在服务端，也可以使用在客户端，一般在客户端。

> 会向调用方返回一个符合预期的、可处理的备选相应（Fallback），而不是长时间等待或抛出异常。

## 2. 功能

1. **服务降级**

   程序运行异常时，给调用方一个友好的返回

2. **服务熔断**

   当服务器达到最大服务访问后，直接拒绝访问，拉闸限电，相当于保险丝。

   降级->熔断->恢复调用链路

3. **服务限流**

   控制并发量

4. **接近实时的监控**

## 3. 实操前置

### 1. 新建模块cloud-provider-hystrix-payment8001

### 2. 改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-hystrix-payment8001</artifactId>

    <dependencies>
        <!-- hystrix -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--actuator监控信息-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--SpringBoot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

### 3. 改YML

```yml
server:
  port: 8001
spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
    register-with-eureka: true
    fetch-registry: true

```

### 4. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class PaymentHystrixMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentHystrixMain8001.class,args);
    }
}

```

### 5. 业务类

#### 1. service

```java
package com.atguigu.springcloud.service;

import org.springframework.stereotype.Service;
import java.util.concurrent.TimeUnit;

@Service
public class PaymentService {

    /**
     * 正常访问方法
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id) {
        return "线程池："+Thread.currentThread().getName()+"  PaymentInfo_OK,id:"
                + id + "\t" + "yeah!";
    }

    /**
     * 模拟异常方法
     * @param id
     * @return
     */
    public String paymentInfo_TIMEOUT(Integer id) {
        int timeNumber = 3;
        // 模拟3秒
        try {
            TimeUnit.SECONDS.sleep(timeNumber);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池："+Thread.currentThread().getName()+"  PaymentInfo_TIMEOUT,id:"
                + id + "\t" + "耗时"+timeNumber+"秒!";
    }

}

```

#### 2. controller

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentController {
    
    @Resource
    private PaymentService paymentService;
    
    @Value("${server.port}")
    private String serverPort;
    
    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentService.paymentInfo_OK(id);
        log.info("**********result:{}",result);
        return result;
    }

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id) {
        String result = paymentService.paymentInfo_TIMEOUT(id);
        log.info("**********result:{}",result);
        return result;
    }
}

```

### 6. 压力测试

操作：用JMETER向timeout接口发出20000个线程请求

结果：此时正常的OK接口和timeout接口都会变得响应缓慢

分析：tomcat的默认工作线程被打满了，没有多余的线程来分解压力和处理

### 7. 新建模块cloud-consumer-feign-hystrix-order80

### 8. 改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-feign-hystrix-order80</artifactId>

    <dependencies>
        <!-- hystrix -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

### 9. 改YML

```yml
server:
  port: 80

eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
    register-with-eureka: false

ribbon:
  # 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
  # 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```

### 10. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class OrderHystrixMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderHystrixMain80.class,args);
    }
}

```

### 11. 业务类

#### 1. service

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
public interface PaymentHystrixService {
    
    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id);
}

```

#### 2. controller

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentHystrixService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderHystrixController {
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        return paymentHystrixService.paymentInfo_OK(id);
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id){
        return paymentHystrixService.paymentInfo_TIMEOUT(id);
    }
}

```

## 4. 降级配置

```java
@HystrixCommand
```

### 1. 在8001上设置

#### 1.位于controller层中

```java
/**
     * 模拟异常方法
     * @param id
     * @return
     */
    // 此时表示，超过3秒，则会调用paymentInfo_TIMEOUTHandler方法
    @HystrixCommand(fallbackMethod = "paymentInfo_TIMEOUTHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds" ,value = "3000")
    })
    public String paymentInfo_TIMEOUT(Integer id) {
        int timeNumber = 5;
        // 模拟3秒
        try {
            TimeUnit.SECONDS.sleep(timeNumber);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池："+Thread.currentThread().getName()+"  PaymentInfo_TIMEOUT,id:"
                + id + "\t" + "耗时"+timeNumber+"秒!";
    }

    public String paymentInfo_TIMEOUTHandler(Integer id) {
        return "线程池："+Thread.currentThread().getName()+"  paymentInfo_TIMEOUTHandler,id:"
                + id + "\t" + "兜底";
    }
```

![image-20210313153945475](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313153945475.png)

#### 2. 在主启动类中启用

![image-20210313154049995](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313154049995.png)

#### 3.测试

> http://localhost:8001/payment/hystrix/timeout/31

![image-20210313154158899](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313154158899.png)

### 2. 在80order端设置

#### 1. 改yml

```yml
server:
  port: 80

eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
    register-with-eureka: false
ribbon:
  # 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
  # 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
feign:
  hystrix:
    enabled: true
```

#### 2. 主启动类中启用

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
@EnableHystrix    // 启用Hystrix
public class OrderHystrixMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderHystrixMain80.class,args);
    }
}

```

#### 3.业务类controller层

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentHystrixService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderHystrixController {
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        return paymentHystrixService.paymentInfo_OK(id);
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    @HystrixCommand(fallbackMethod = "paymentInfo_TIMEOUTHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds" ,value = "1500")  //只等1.5s
    })
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id){
        return paymentHystrixService.paymentInfo_TIMEOUT(id);
    }

    public String paymentInfo_TIMEOUTHandler(Integer id) {
        return "我是消费者80，服务器繁忙，请稍后再试";
    }
}
```

#### 4.测试

> http://localhost/consumer/payment/hystrix/timeout/31

![image-20210313160136590](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313160136590.png)

### 3. 此时问题

1. 每个业务都需要一个兜底的方法，代码膨胀
2. 统一的代码和自定义的代码糅合在一起

### 4. 全局服务降级

> 位于80客户端，主要是针对于**客户端**出错时的解决方案

```java
@DefaultProperties(defaultFallback="")
```

![image-20210313161556092](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313161556092.png)

解决了代码膨胀问题

### 5. 通配服务降级

> 位于80客户端，service接口再增加一个实现类，用于服务降级使用，主要是针对于**服务端**出错时的解决方案

#### 1. 新增实现类

```java
package com.atguigu.springcloud.service;

import org.springframework.stereotype.Component;

@Component
public class PaymentFallbackService implements PaymentHystrixService {
    @Override
    public String paymentInfo_OK(Integer id) {
        return "---------PaymentFallbackService fall back-paymentInfo_OK";
    }

    @Override
    public String paymentInfo_TIMEOUT(Integer id) {
        return "---------PaymentFallbackService fall back-paymentInfo_TIMEOUT";
    }
}

```

![image-20210313162500149](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313162500149.png)

#### 2. 在服务接口中调用实现类

![image-20210313162713786](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313162713786.png)

#### 3. 此时的80controller层

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentHystrixService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
//@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
public class OrderHystrixController {
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        return paymentHystrixService.paymentInfo_OK(id);
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
//    @HystrixCommand(fallbackMethod = "paymentInfo_TIMEOUTHandler", commandProperties = {
//            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds" ,value = "1500")  //只等1.5s
//    })
//    @HystrixCommand
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id){
//        int age = 10/0;
        return paymentHystrixService.paymentInfo_TIMEOUT(id);
    }

    // 全局fallback方法
    public String payment_Global_FallbackMethod() {
        return "Global异常处理信息，请稍后再试";
    }

    public String paymentInfo_TIMEOUTHandler(Integer id) {
        return "我是消费者80，服务器繁忙，请稍后再试";
    }
}

```

#### 4.测试

1. 启动80模块
2. 关闭8001模块
3. 访问http://localhost/consumer/payment/hystrix/timeout/31

![image-20210313163953162](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313163953162.png)

## 5. 服务熔断

> 位于8001服务端

### 1. 修改PaymentService中内容

```java
package com.atguigu.springcloud.service;

import cn.hutool.core.util.IdUtil;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.PathVariable;

import java.util.concurrent.TimeUnit;

@Service
public class PaymentService {

    /**
     * 正常访问方法
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id) {
        return "线程池："+Thread.currentThread().getName()+"  PaymentInfo_OK,id:"
                + id + "\t" + "yeah!";
    }

    /**
     * 模拟异常方法
     * @param id
     * @return
     */
    // 此时表示，超过3秒，则会调用paymentInfo_TIMEOUTHandler方法
    @HystrixCommand(fallbackMethod = "paymentInfo_TIMEOUTHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds" ,value = "3000")
    })
    public String paymentInfo_TIMEOUT(Integer id) {
//        int age = 10/0;
        int timeNumber = 5;
        // 模拟3秒
        try {
            TimeUnit.SECONDS.sleep(timeNumber);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池："+Thread.currentThread().getName()+"  PaymentInfo_TIMEOUT,id:"
                + id + "\t" + "耗时"+timeNumber+"秒!";
    }

    public String paymentInfo_TIMEOUTHandler(Integer id) {
        return "线程池："+Thread.currentThread().getName()+"  paymentInfo_TIMEOUTHandler,id:"
                + id + "\t" + "兜底";
    }

    // ==========================服务熔断

    @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
            @HystrixProperty(name="circuitBreaker.enabled", value = "true"),      // 是否开启断路器
            @HystrixProperty(name="circuitBreaker.requestVolumeThreshold", value = "10"),  // 请求次数
            @HystrixProperty(name="circuitBreaker.sleepWindowInMilliseconds", value = "10000"),  // 时间窗口期
            @HystrixProperty(name="circuitBreaker.errorThresholdPercentage", value = "60"), // 失败率达到多少后跳闸
    })
    public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
        if (id < 0) {
            throw new RuntimeException("*********id不能是负数");
        }
        String serialNumber = IdUtil.simpleUUID();
        return Thread.currentThread().getName()+"\t"+"调用成功，流水号："+serialNumber;
    }

    public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
        return "id不能为负数，请稍后再试,id:"+id;
    }

}
```

1. 上述参数解释
   1. 快照时间窗：统计时间，用于是否打开断路器，默认10s。
   2. 请求总数阈值：在快照时间窗内，请求达到总数阈值时才有资格熔断，默认20；意味着，如果调用次数不足20次，即使所有请求都超时或失败，也不会打开断路器。
   3. 错误百分比阈值：当有资格熔断时，如果超时和失败的请求数超过了总数的60%，那么会将断路器打开。

2. 属性值搜索方法：

   全局搜索HystrixCommandProperties

![image-20210313172945443](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313172945443.png)

### 2. PaymentController中增加接口

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentService.paymentInfo_OK(id);
        log.info("**********result:{}",result);
        return result;
    }

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TIMEOUT(@PathVariable("id") Integer id) {
        String result = paymentService.paymentInfo_TIMEOUT(id);
        log.info("**********result:{}",result);
        return result;
    }

    // 服务熔断
    @GetMapping("payment/circuit/{id}")
    public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
        return paymentService.paymentCircuitBreaker(id);
    }
}
```

### 3. 测试

1. 连续发出接口请求：

> http://localhost:8001/payment/circuit/-1

2. 在访问正确接口:

> http://localhost:8001/payment/circuit/11

结论：此时会发现，访问正确接口也是降级的响应，此时说明断路器已经打开；然后过一段时间后，会恢复到正常状态。

![image-20210313171746534](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313171746534.png)

### 4. 小总结

1. 当断路器打开后，后面再有调用请求，不会调用主逻辑，而是直接调用降级fallback。
2. 断路器打开后，会启动一个休眠时间窗，时间窗到期后，断路器处于半开状态，释放一次请求到主逻辑上，如果正常返回，那么断路器将闭合，主逻辑恢复；如果依然有问题，那么断路器继续打开，休眠时间窗重新计时。

## 6. 服务监控

### 1. 新建模块cloud-consumer-hustrix-dashboard9001

### 2. 改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-hustrix-dashboard9001</artifactId>

    <dependencies>
        <!-- hystrix dashboard-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

### 3. 改YML

```yml
server:
  port: 9001
```

### 4. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;

@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashBoardMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashBoardMain9001.class,args);
    }
}

```

### 5. 监控配置

所以被监控的服务都需要加上依赖才能对其监控 

```xml
<!--actuator监控信息-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

然后在被监控服务的主启动类中加入如下内容

![image-20210313175846813](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313175846813.png)

```java
@Bean
    public ServletRegistrationBean getServlet() {
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }
```



### 6. 访问地址

> http://localhost:9001/hystrix

![image-20210313175042195](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313175042195.png)

### 7. 监控8001服务

![image-20210313180037123](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313180037123.png)

进入后，正常访问8001的接口地址，然后到仪表盘中查看结果

### 8. 仪表盘看法

1. 7色

   ![image-20210313180515005](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313180515005.png)

2. 1圈

   ![image-20210313180542885](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313180542885.png)

   颜色代表健康程度，大小代表流量大小

3. 1线

   ![image-20210313180620623](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313180620623.png)

   观察流量的上升和下降趋势

4. 全图解释

![image-20210313180911875](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210313180911875.png)

# 14. Gateway

## 1. 概述

官网：https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/

> Gateway是在Spring生态系统上构建的API网关服务，基于Spring5,Spring Boot 2和Project Reactor等技术，相当于进入系统前的过滤器

> 为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty。
>
> 是基于**异步非阻塞模型**进行开发的

## 2. 概念

1. 路由（Route）

   > 路由是构建网关的基本模块，它由ID，目标URI，一系列断言和过滤器组成，如果断言为TRUE则匹配该路由

2. 断言（Predicate）

   > 开发人员可以匹配HTTP请求中的所有内容，是个布尔值

3. 过滤（Filter）

   >可以在请求被路由前或后对请求进行修改

## 3. 实操

### 1. 新建模块cloud-gateway-gateway9527

### 2. 改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-gateway-gateway9527</artifactId>

    <dependencies>
        <!--gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.study.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

### 3. 改YML

```yml
server:
  port: 9527
spring:
  application:
    name: cloud-gateway
  # ---------------------------
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route  路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001   #匹配提供服务的路由地址
          predicates:
            - Path=/payment/get/**     #断言，路径相匹配的进行路由

        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/discovery/**
  # -----------------------------

eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka

```

### 4. 主启动类

```java
package com.atguigu.springcould;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class GateWayMain9527 {
    public static void main(String[] args) {
        SpringApplication.run(GateWayMain9527.class,args);
    }
}
```

### 5. 测试

添加网关前：

> http://localhost:8001/payment/get/31

添加网关后：

> http://localhost:9527/payment/get/31

### 6. 另一种配置方式

#### 1. 建立配置类

![image-20210329144416012](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210329144416012.png)

```java
package com.atguigu.springcould.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GateWayConfig {
    
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder) {
        
        // 获得路由
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        routes.route("path_route_atguigu",
                r -> r.path("/guonei")
                        .uri("http://news.baidu.com/guonei"))
                .build();

        return routes.build();
    }
    
    //可以再加
    
}
```

#### 2. 测试

访问http://localhost:9527/guonei，看能不能跳到百度国内新闻

### 7. 动态路由

先启动8001和8002两个微服务

![image-20210329150026470](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210329150026470.png)

改yml

```yml
server:
  port: 9527
spring:
  application:
    name: cloud-gateway
  # ---------------------------
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true  # 开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh
          uri: lb://cloud-payment-service   # 匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**

        - id: payment_routh2
          uri: lb://cloud-payment-service   # 匹配后提供服务的路由地址
          predicates:
            - Path=/payment/discovery/**
  # -----------------------------

eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka

```

测试

访问http://localhost:9527/payment/discovery，实现两个端口切换

## 4. 常用的Predicate

主要是以下圈中的地方

![image-20210329150631302](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210329150631302.png)

### 分类

> https://blog.csdn.net/qq1328585964/article/details/107204386

## 5. Filter

### 1. 作用

1. 全局日志记录
2. 统一鉴权
3. ...

### 2. 生命周期

1. pre
2. post

### 3. 种类

1. GatewayFilter

 	2. GlobalFilter

### 3. 自定义过滤器

> 主要实现两个接口：GlobalFilter, Ordered

#### 1. 建立配置类

![image-20210329153050324](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210329153050324.png)

```java
package com.atguigu.springcould.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("***********全局过滤器："+new Date());
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");
        if (uname == null) {
            log.info("非法用户XXXXXXXX");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    // 过滤器顺序，值越小越前
    @Override
    public int getOrder() {
        return 0;
    }
}

```

#### 2. 测试

> http://localhost:9527/payment/discovery

> http://localhost:9527/payment/discovery?uname=11

# 15. Config

## 1. 概述

> 1. 配置中心，集中化的外部配置支持
> 2. 分为服务端和客户端
> 3. 动态调整配置，统一从配置中心拉取

## 2. 实操

### 1. 服务端

#### 1. git建立新的repo

#### 2. 上传配置文件

涉及命令

```shell
git add
git commit -m "提交信息"
git push origin master
```

![image-20210511164604879](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210511164604879.png)

#### 3. 新建cloud-config-center-3344模块

#### 4. 写POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-config-center-3344</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

#### 5. 写YML

```yml
server:
  port: 3344

spring:
  application:
    name: cloud-config-center
  cloud:
    config:
      server:
        git:
          # 仓库名
          uri: https://github.com/hazardCoder/spring-cloud-config/
          # 搜索目录
          search-paths:
            - spring-cloud-config
          username: -----
          password: -----
      # 读取分支
      label: master
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka

```

#### 6. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer   // 激活
public class ConfigCenterMain3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigCenterMain3344.class, args);
    }
}
```

#### 7. 测试

1. 启动微服务3344

2. 访问

   >http://localhost:3344/master/config-dev.yml

#### 8. 配置读取规则

1. /{label}/{application}-{profile}.yml

   >http://localhost:3344/master/config-dev.yml

2. /{application}-{profile}.yml

   > http://localhost:3344/config-dev.yml
   >
   > // 默认master分支

3. /{application}/{profile}[/{label}]

   > http://localhost:3344/config/dev/master

### 2. 客户端

#### 1. 新建cloud-config-client-3355模块

#### 2. 写POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-config-client-3355</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

#### 3. bootstrap.yml

> bootstrap.yml的有限级比application.yml的优先级更高，先加载bootstrap，再加载application
>
> bootstrap是取外部资源（Config），然后由application覆盖，类似子类重写父类

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    # config客户端配置
    config:
      label: master   # 分支
      name: config    # 配置名
      profile: dev    # 环境
      uri: http://localhost:3344  # 配置中心地址
eureka:
  client:
    service-url: 
      defaultZone: http://localhost:7001/eureka

```

#### 4. 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class ConfigClientMain3355 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigClientMain3355.class, args);
    }
}

```

#### 5. 业务类

```java
package com.atguigu.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ConfigClientController {
    
    @Value("${config.info}")
    private String configInfo;
    
    @GetMapping("/configInfo")
    public String getConfigInfo() {
        return configInfo;
    }
}

```

#### 5. 修改config-dev.yml内容

```yml
config:
    info: "master branch,spring-cloud-config/config-dev.yml verson=1"
```

#### 6. 测试

1. 启动eureka

2. 启动配置中心3344

3. 启动测试类3355

4. 访问

   > http://localhost:3355/configInfo

### 3. 动态刷新

> 避免每次更新配置后都要重启客户端微服务

#### 1. 引入依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

#### 2. yml

```yml
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

#### 3. Controller

```java
package com.atguigu.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RefreshScope    // 新增
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String getConfigInfo() {
        return configInfo;
    }
}

```

#### 4. 测试

修改 github 上config-dev.yml内容，查看服务端和客户端的响应内容为

```yml
config:
    info: "master branch,spring-cloud-config/config-dev.yml verson=2"
```

服务端跟随github更新实时更新，但是对于客户端来说，在更改配置后，需要发送POST请求到客户端进行刷新

```shell
curl -X POST "http://localhost:3355/actuator/refresh"
```

思考：如果有100台服务器，那么需要发送100次POST请求，很麻烦

解决方案：消息总线

# 16. SpringCloud Bus

## 1. 概述

> SpringCloud Bus能管理和传播分布式系统间的消息，就像一个分布式执行器，用于广播状态更改、事件推送和通信通道等。

1. 分布式自动刷新配置
2. 配合SpringCloud Config实现配置的动态刷新
3. 支持两种消息代理：RabbitMQ和Kafka

## 2. 原理

ConfigClient实例都监听MQ中同一个topic（默认是springCloudBus）。当一个服务刷新数据的时候，它会把这个消息放入到Topic中，这样其他监听同一Topic的服务就能够得到通知，然后去更新自身的配置

## 3. RabbitMQ环境配置

### 1. 安装erlang

> 因为rabbitmq是用erlang写的，所以需要erlang运行环境

下载：

> http://erlang.org/download/otp_win64_21.3.exe

### 2. 安装RabbitMQ

> https://www.rabbitmq.com/install-windows.html

### 3. 配置

1. 进入到sbin目录下

2. 输入命令

   > rabbitmq-plugins enable rabbitmq_management

   ![image-20210527132945738](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210527132945738.png)

### 4. 启动

![image-20210527133101594](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210527133101594.png)

### 5.  访问

> localhost:15672

默认账号密码：guest guest

## 4. 实操

### 1. 新建3366模块

### 2. POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-config-client-3366</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

### 3. yml

```yml
server:
  port: 3366

spring:
  application:
    name: config-client
  cloud:
    # config客户端配置
    config:
      label: master   # 分支
      name: config    # 配置名
      profile: dev    # 环境
      uri: http://localhost:3344  # 配置中心地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka

# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

### 4. controller层

```java
package com.atguigu.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author Qian
 * @date 2021年05月27日 13:41
 */
@RestController
@RefreshScope
public class ConfigClientController {

    @Value("${server.port}")
    private String serverPort;

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String configInfo() {
        return "serverPort:"+serverPort+"\t\n\n configInfo:"+configInfo;
    }
}
```

### 5. 设计思想

1. 利用消息总线触发一个**客户端**，而刷新所有客户端配置（×）
2. 利用消息总线触发一个**服务端**，而刷新所有客户端配置（√）

### 6. 添加消息总线支持

1. **服务端**

   （1）pom

   ```xml
   <!--消息总线支持-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-bus-amqp</artifactId>
           </dependency>
   ```

   （2）yml

   ```yml
   server:
     port: 3344
   
   spring:
     application:
       name: cloud-config-center
     cloud:
       config:
         server:
           git:
             # 仓库名
             uri: https://github.com/hazardCoder/spring-cloud-config/
             # 搜索目录
             search-paths:
               - spring-cloud-config
             username: xxx
             password: xxx
         # 读取分支
         label: master
     devtools:
       add-properties: false
       
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
         
   ## rabbitmq相关配置
   rabbitmq:
     host: localhost
     port: 5672
     username: guest
     password: guest
   
     ## rabbitmq相关配置,暴露bus刷新配置的端点
   management:
     endpoints:
       web:
         exposure:
           include: 'bus-refresh'
   ```

2. **客户端**

   （1）pom

   ```xml
   <!--消息总线支持-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-bus-amqp</artifactId>
           </dependency>
   ```

   （2）yml

   

   ```yml
   server:
     port: 3355
   
   spring:
     application:
       name: config-client
     cloud:
       # config客户端配置
       config:
         label: master   # 分支
         name: config    # 配置名
         profile: dev    # 环境
         uri: http://localhost:3344  # 配置中心地址
     ## rabbitmq相关配置
     rabbitmq:
       host: localhost
       port: 5672
       username: guest
       password: guest
       
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
   
   # 暴露监控端点
   management:
     endpoints:
       web:
         exposure:
           include: "*"
   ```

### 7. 测试

1. 启动eureka

2. 启动配置中心模块

3. 启动客户端模块

4. 更改github上配置文件内容

5. 发送post请求

   >curl -X POST "http://localhost:3344/actuator/bus-refresh"

6. 测试内容

   > http://localhost:3344/master/config-dev.yml
   >
   > http://localhost:3355/configInfo
   >
   > http://localhost:3366/configInfo

   观察配置内容是否改变

### 8. 定点通知

> 指定具体某一实例生效而不是全部

公式：

>curl -X POST "http://localhost:配置中心端口号/actuator/bus-refresh/{destination}"

比如：

> curl -X POST "http://localhost:3344/actuator/bus-refresh/config-client:3355"

# 17. SpringCloud Stream

## 1. 背景

主要的消息中间件（MQ）使用有4种

1. ActiveMQ
2. RabbitMQ
3. RocketMQ
4. Kafka

程序员学习成本大，而且在实际项目时，可能在项目中同时存在多个消息中间件，SpringCloud Stream适配其他消息中间件，屏蔽底层消息中间件差异，统一消息的编程模型

## 2. 概念

SpringCloud Stream是一个构建消息驱动微服务的框架。

应用程序通过inputs（生产）和outputs（消费）来与SpringCloud Stream中binder对象交互

通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动，提供了个性化配置，引用了发布-订阅、消费组、分区的三个核心概念

目前支持RabbitMQ和Kafka

## 3. 流程

### 1. 消息生产者

1. Source（输入）
2. Channel（通道）
3. Binder（绑定器）

### 2. 消息消费者

1. Sink（输出）
2. Channel（通道）
3. Binder（绑定器）

## 4. 常见注解

| 组成            | 说明                                              |
| --------------- | ------------------------------------------------- |
| Middleware      | 中间件，目前只支持Kafka和RabbitMQ                 |
| Binder          | 动态改变消息类型（Kafka:topic,RabbitMQ:exchange） |
| @Input          | 注解标识输入通道                                  |
| @Output         | 注解标识输出通道                                  |
| @StreamListener | 监听队列，用于消费者队列接收                      |
| @EnableBinding  | 指channel和exchange绑定在一起                     |

## 5. 实操

### 1. 生产者

#### 1. 新建8801模块

#### 2. POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2021</artifactId>
        <groupId>com.study.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-stream-rabbitmq-provider8801</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

#### 3. YML

```yml
server:
  port: 8801

spring:
  application:
    name: cloud-stream-provider
  cloud:
    stream:
      binders: # rabbitmq的配置信息
        defaultRabbit: # 表示定义的名称，用于binding集合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        output: # 通道
          destination: studyExchange # 表示使用的Exchange的名称
          content-type: application/json # 消息类型
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client:
    service-url: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2  # 设置心跳的时间间隔
    lease-expiration-duration-in-seconds: 5
    instance-id: send-8801.com   # 在信息列表时显示主机名称
    prefer-ip-address: true # 访问的路径变为IP地址
```

#### 4. 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author Qian
 * @date 2021年06月10日 11:10
 */
@SpringBootApplication
public class StreamMQMain8801 {
    public static void main(String[] args) {
        SpringApplication.run(StreamMQMain8801.class, args);
    }
}
```

#### 5. 业务类

```java
package com.atguigu.springcloud.service;

/**
 * 消息发送者
 */
public interface IMessageProvider {
    public String send();
}
```

```java
package com.atguigu.springcloud.service.impl;

import com.atguigu.springcloud.service.IMessageProvider;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.messaging.Source;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.support.MessageBuilder;

import javax.annotation.Resource;
import java.util.UUID;

/**
 * @author Qian
 * @date 2021年06月10日 11:13
 */
@EnableBinding(Source.class)  // 定义消息的推送管道
public class MessageProviderImpl implements IMessageProvider {

    /**
     * 消息发送管道
     */
    @Resource
    private MessageChannel output;

    @Override
    public String send() {
        String serial = UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("*****serial: "+serial);
        return null;
    }
}
```

#### 6. Controller

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.IMessageProvider;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @author Qian
 * @date 2021年06月10日 11:19
 */
@RestController
public class SendMessageController {
    
    @Resource
    private IMessageProvider messageProvider;
    
    @GetMapping(value = "/sendMessage")
    public String sendMessage() {
        return messageProvider.send();
    }
}
```

#### 7. 测试

1. 启动eureka

2. 启动rabbitmq

   1. sbin/rabbitmq-server.bat
   2. http://localhost:15672/

3. 启动8801

   ![image-20210610112830670](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610112830670.png)

4. 访问

   http://localhost:8801/sendMessage

### 2. 消费者

#### 1. 新建模块8802

#### 2. POM

​	和生产者相同

#### 3. YML

```yml
server:
  port: 8802

spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # rabbitmq的配置信息
        defaultRabbit: # 表示定义的名称，用于binding集合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        input: # 通道
          destination: studyExchange # 表示使用的Exchange的名称
          content-type: application/json # 消息类型
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2  # 设置心跳的时间间隔
    lease-expiration-duration-in-seconds: 5
    instance-id: receive-8802.com   # 在信息列表时显示主机名称
    prefer-ip-address: true # 访问的路径变为IP地址
```

#### 4. 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author Qian
 * @date 2021年06月10日 12:14
 */
@SpringBootApplication
public class StreamMQMain8802 {
    public static void main(String[] args) {
        SpringApplication.run(StreamMQMain8802.class, args);
    }
}
```

#### 5. 业务类

```java
package com.atguigu.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.cloud.stream.messaging.Sink;
import org.springframework.messaging.Message;
import org.springframework.stereotype.Component;

/**
 * @author Qian
 * @date 2021年06月10日 12:16
 */
@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {
    
    @Value("${server.port}")
    private String serverPort;
    
    @StreamListener(Sink.INPUT)
    public void input(Message<String> message) {
        System.out.println("消费者1号--->"+message.getPayload()+"\t port:"+serverPort);
    }
}
```

#### 6. 测试

1. 启动eureka

2. 启动MQ

3. 启动8801生产者

4. 启动8802消费者

5. 发送信息

   http://localhost:8801/sendMessage

6. 查看生产者和消费者的日志是否打印

## 6. 分组消费

### 1. 增加消费者

1. 克隆8802模块为8803模块

 	2. 启动eureka
	3. 启动MQ
	4. 启动8801生产者
	5. 启动8802消费者
	6. 启动8803消费者
	7. 发送信息

### 2. 产生问题

1. 重复消费
2. 消息持久化问题

解决：**分组**

> 不同组是可以全面消费的（重复消费）
>
> 同一个组会发生竞争关系，只有其中一个可以消费

例如：

![image-20210610131547249](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610131547249.png)

目前就是有两个组

### 3. 解决

消费者中添加配置，统一到同一个组：

![image-20210610132048508](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610132048508.png)

## 7. 持久化

**问题背景**

> 当消费者宕机以后，但生产者还在继续生产消息，如何保证消费者服务器恢复后能够拿到宕机过程中产生的消息，从而避免消息丢失

**解决**

加了group标签配置后，自动有了持久化的功能，当消费者重启后，能够消费到宕机过程中产生的消息

# 18. Sleuth

## 1. 背景

> 在微服务框架中，一个由客户端发起的请求在后端系统中可能会经过多个不同的服务节点调用来协同产生最后的请求结果，每一个前段请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引起整个请求最后的失败。

## 2. 功能

一套完整的**服务跟踪**解决方案

## 3. 概念

### 1. 术语

表示一个请求链路，一条链路通过traceId唯一标识，Span标识发起的请求信息，各Span通过parentId关联起来

![未命名文件 (1)](D:\Downloads\未命名文件 (1).png)



## 4. 实操

### 1. zipkin搭建

> 用于展示链路信息

1. 下载

   https://github.com/openzipkin/zipkin/

2. 启动

   ```shell
   java -jar zipkin.jar
   ```

3. 验证

   http://your_host:9411/zipkin/

### 2. 引入maven坐标

在需要监控的模块上添加坐标，其中包含了sleuth

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

### 3. 引入yml配置

![image-20210610141916411](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610141916411.png)

### 4. 测试Controller

```java
// 8001模块
@GetMapping("/payment/zipkin")
public String paymentZipkin() {
    return "hi,zipkin print";
}

// 80模块
@GetMapping("/consumer/payment/zipkin")
    public String paymentZipkin() {
        String result = restTemplate.getForObject("http://localhost:8001/payment/zipkin", String.class);
        return result;
    }
```

### 5. 测试

http://your_host:9411/zipkin/

页面中可视化界面，易操作

![image-20210610144258731](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610144258731.png)

![image-20210610144313745](C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210610144313745.png)
