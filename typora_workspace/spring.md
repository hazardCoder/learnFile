# 一、概述

## （一）介绍

* 解释：是一个全栈式（full-stack）的轻量级开源框架
* 内核：
    * IoC(Inverse Of Control：反转控制)
    * AOP(Aspect Oriented Programming：面向切面编程)

## （二）解耦
* 思路：
    * 使用反射来创建对象，而避免使用new关键字（减少类之间依赖）
    * 通过读取配置文件来获取要创建的对象全限定类名
* 方法：
    * 工厂模式解耦。利用上述思路来写工厂类，达到类中通过反射机制创建对象的操作。

## （三）IOC（反转控制）
> 功能：削减计算机程序的耦合（解除我们代码中的依赖关系）

* 两种创建对象方式

```java
private IAccountDao accountDao = new AccountDaoImpl();
private IAccountDao accountDao = (IAccountDao)BeanFactory.getBean("accountDao");
```
* 解释：
    * 第一种方式：控制权在当前类手里
    * 第二种方式：将创建权给工厂类（框架）

# 二、Spring实现

* 创建配置文件(Bean.xml)

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
<!--    把对象的创建交给Spring管理-->
    <bean id="accountService" class="com.learnspring.service.impl.AccountServiceImpl"></bean>

    <bean id="accountDao" class="com.learnspring.dao.impl.AccountDaoImpl"></bean>
</beans>
```

* 使用以及问题
    * ApplicationContext的三个常用实现类：
        * ==ClassPathXXmlApplicationContext==：可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在就加载不了
        * ==FileSystemXmlApplicationContext==：可以加载磁盘任意路径的配置文件（必须有访问权限）
        * ==AnnotationConfigApplicationContext==：用于读取注解创建容器的
    * 核心容器的两个接口引发出的问题：
    
        | 容器名             | 解释                                                         | 使用场景 |
        | ------------------ | ------------------------------------------------------------ | -------- |
        | ApplicationContext | ApplicationContext是BeanFactory的子接口，拥有BeanFactory的全部功能；在创建核心容器时，创建对象采取的策略是采用立即加载的方式。一读取完配置文件马上就创建配置文件中配置的对象。 | 单例对象 |
        | BeanFactory        | 创建核心容器时，创建对象采取的策略是采用延迟加载的方式。什么时候根据id获取对象，什么时候才真正的创建对象。 | 多例对象 |
    * 示例代码

    ```java
    public class Client {
    
        /**
         * 获取spring的Ioc核心容器，并根据ID获取对象
         * @param args
         */
        public static void main(String[] args){
            //获取核心容器对象
            ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
            //根据ID获取Bean对象
            AccountService as = (AccountService) ac.getBean("accountService");     //进行强转
            AccountDao adao = ac.getBean("accountDao",AccountDao.class);           //传入字节码，不用强转
    
            System.out.println(as);
            System.out.println(adao);
        }
    }
    
    ```

# 三、创建Bean的三种方式

* **第一种**：
    * 在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时。采用的就是默认构造函数创建bean对象，此时如果类中没有默认构造函数，则对象无法创建。

```xml
<bean id="accountService" class="com.learnspring.service.impl.AccountServiceImpl"></bean>
```

* **第二种**：
    * 使用普通工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器）

```xml
<bean id="instanceFactroy" class="com.learnspring.factory.InstanceFactory"></bean>  //工厂类，用于获得accountService
<bean id="accountService" factory-bean="instanceFactroy" factory-method="getAccountService"></bean>   //通过工厂类获得对象
```

* **第三种**：
    * 使用工厂中的静态方法创建对象（使用某个类中的静态方法创建对象，并存入spring容器）
    
```xml
<bean id="accountService" class="com.learnspring.factory.StaticFactory" factory-method="getAccountService"></bean>
```

# 四、bean的作用范围调整

* bean的scope属性：
    * 功能：用于指定bean的作用范围
    * 取值：常用多例和单例
      
    | 取值           | 解释                                 |
    | -------------- | ------------------------------------ |
    | singleton      | 单例（默认值）                       |
    | prototype      | 多例                                 |
    | request        | 作用于web应用的请求范围              |
    | session        | 作用于web应用的会话范围              |
    | global-session | 作用于集群环境的会话范围（全局会话） |

# 五、bean的生命周期

* 单例对象
    * 出生：当容器创建时，对象出生
    * 活着：只要容器存在，对象一直存在
    * 死亡：容器销毁，对象消亡
* 多例对象
    * 出生：当使用对象时，spring框架创建出来
    * 活着：对象在使用过程中，就会一直活着
    * 死亡：当对象长时间不用，且没有别的对象引用时，由java的垃圾回收器回收。

# 六、spring中的依赖注入（Dependency Injection）

> 依赖注入：依赖关系的维护

## （一）注入类型分类：

* 基本类型和String
* 其他bean类型
* 复杂类型/集合类型

* 注入方式分类

## （二）使用构造函数提供

* 使用的标签：constructor-arg
* 出现位置：bean内部

* 标签中的属性：

    | 属性  | 功能                                                         |
    | ----- | ------------------------------------------------------------ |
    | type  | 用于指定要注入的数据类型                                     |
    | index | 用于指定要注入的数据在构造参数中指定索引位置的参数赋值，从0开始 |
    | name  | 用于指定给构造函数中指定名称的参数赋值（**常用**）           |
    | value | 用于给基本类型和String类型赋值                               |
    | ref   | 用于指定其他的bean类型数据                                   |
    * 代码示例：
    ```xml
    <bean id="accountService" class="com.learnspring.service.impl.AccountServiceImpl">
            <constructor-arg name="name" value="test"></constructor-arg>
            <constructor-arg name="age" value="18"></constructor-arg>
            <constructor-arg name="birthday" ref="now"></constructor-arg>
    </bean>
    
    <bean id="now" class="java.util.Date"></bean>
    ```
    * 优势：
      
        * 在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
    * 弊端：
    
        * 改变了bean对象的实例化方法，使我们在创建对象时，如果用不到这些数据，也必须提供。
    
    * **使用set方法提供**，需要有setter方法
      
        * 使用的标签：property
            * 用于给List结构集合注入的标签：list,array,set
            * 用于给Map结构集合注入的标签：map,props
            * 结构相同，标签可以互换
        * 出现位置：bean标签内部
        * 标签的属性：
        
        | 属性  | 功能                          |
        | ----- | ----------------------------- |
        | name  | 指定注入时所调用的set方法名称 |
        | value | 提供基本类型和String类型赋值  |
        | ref   | 用于指定其他的bean类型数据    |
        
        * 代码示例：
        
        ```xml
        <bean id="accountService" class="com.learnspring.service.impl.AccountServiceImpl">
                <property name="name" value="TEST"></property>
                <property name="age" value="21"></property>
                <property name="birthday" ref="now"></property>
        </bean>
        
        <bean id="now" class="java.util.Date"></bean>
        ```
        
         ```xml
        <property name="myMap">
            <map>
                <entry key="testA" value="aaa"></entry>
                <entry key="testB">
                    <value>bbb</value>
                </entry>
            </map>
        </property>
        <property name="mySet">
            <set>
                <value>aaa</value>
                <value>bbb</value>
            </set>
        </property>
         ```
        
        * 优势：
            * 创建对象时没有明确的限制，可以直接使用默认构造函数
        * 弊端：
            * 如果某个成员必须有值，则set方法无法保证一定注入
    * **使用注解方式提供**
        * 配置准备：
          
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:context="http://www.springframework.org/schema/context"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/context/spring-context.xsd">
        
        <!--    注解扫描-->
            <context:component-scan base-package="com.learnspring"></context:component-scan>
        </beans>
        ```
        * 注解：==@Component==
            * 功能：用于把当前类对象存入spring容器中
            * 属性：
                * value：用于指定bean的id。如果不写，默认值为当前类名，且首字母小写。
            * 代码示例：
            ```java
            @Component(value = "accountService")
            public interface AccountService {
                public void saveAccount();
            }
            ```
            
            ```java
            AccountService as = (AccountService) ac.getBean("accountService");     
            ```
        * 注解：==@Controller==（表现层）,==@Service==（业务层）,==@Repository==（持久层）
          
            * 功能：作用和属性与Component是一模一样的，是spring框架为我们提供的三层使用的注解，使三层对象更加清晰。
        * 注解：==@Autowired==
            * 功能：自动按照**类型**注入。（先类型再名称）
                * 只要容器中有唯一的一个bean对象类型和要注入的类型匹配，就可以注入成功。
                * 如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
                * 如果ioc容器有多个类型匹配时，这时会在这些类型中按照创建对象的**名称**来进行查找，如果有相同的，那么就匹配成功，否则匹配失败。
            * 出现位置：可以是变量上，也可以是方法上
            * 代码示例：
            
            ```java
            @Component(value = "accountService")
            public class AccountServiceImpl implements AccountService {
                @Autowired
                private AccountDao accountDao;
            
                public AccountServiceImpl() {
                    System.out.println("对象创建了");
                }
                @Override
                public void saveAccount() {
                    accountDao.saveAccount();
                }
            }
            ```
        * 注解：==@Qualifier==
            * 功能：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以。
            * 属性：
                * value：用于指定注入的id
            * 代码示例：
            
            ```java
            @Component(value = "accountService")
            public class AccountServiceImpl implements AccountService {
                @Autowired
                @Qualifier("accountDao")   //不能独立使用
                private AccountDao accountDao;
            
                public AccountServiceImpl() {
                    System.out.println("对象创建了");
                }
                @Override
                public void saveAccount() {
                    accountDao.saveAccount();
                }
            }
            ```
        * 注解：==@Resource==
            * 作用：直接按照bean的**id**注入。可以独立使用。
            * 属性：
                * name：用于指定bean的id
            * 代码示例：
                        
            ```java
            @Resource(name = "accountDao")
            private AccountDao accountDao;
            ```
        
        * 注解：==@Value==
            * 作用：用于注入基本类型和String类型的数据
            * 属性：
                * value：用于指定数据的值。可以使用spring中SpEL（也就是spring的el表达式）（SpEL的写法：${表达式}）
        * 注解：==@Scope==
            * 作用：用于指定bean的作用范围
            * 属性：
                * value：指定范围的取值。
                    * singleton：单例
                    * prototype：多例
        * 注解：==@PreDestroy==
          
            * 功能：用于指定**销毁**方法
        * 注解：==@PostConstruct==
          
            * 功能：用于指定**初始化**方法

## 一些新注解（可以用于去掉bean.xml）
* ==@Configuration==
    * 作用：指定当前类是一个配置类。
    * 细节：当配置类作为AnnotationConfigApplicationContext对象创建时，这个注解可以不写。
* ==@ComponentScan==
    * 作用：用于通过注解指定spring在创建容器时要扫描的包
    * 属性：
        * value:和basePackages的作用是一样的，用于指定创建容器时要扫描的包，等同xml中的配置
        
        ```xml
        <context:component-scan base-package="com.learnspring"></context:component-scan>
        ```
* ==@Bean==
    * 作用：用于把当前方法的返回值作为bean对象存入spring的ioc容器中
    * 属性：
        * name：用于指定bean的id。默认值：当前方法的名称
    * 注意：
        * 当使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象。查找方式与Autowired注解的作用是一样的。
* ==@Import==
    * 作用：用于导入其他的配置类
    * 属性：
        * value:用于指定其他配置类的字节码。
    * 说明：当使用Import的注解之后，有Import注解的类就是父配置类，而导入的都是子配置类
    * 代码示例：
      
    ```java
    @Import(JdbcConfig.class)
    public class SpringConfiguration(){
        
    }
    ```
* ==@PropertySource==
    * 作用：用于指定properties文件的位置
    * 属性
        * value：指定文件的名称和路径。
            * 关键字：classpath,表示类路径下
    * 代码示例：
    
    ```java
    @PropertySource("classpath:jdbcConfig.properties")
    public class SpringConfiguration(){
            
    }
    ```


* 最后，在读取配置时，使用AnnotationConfigApplicationContext()的对象读取配置。

## spring整合junit的配置
* 导入spring整合junit的jar（坐标）
* 使用Junit提供的一个注解把原有的main方法替换了，替换成spring提供的（@Runwith）
* 告知spring的运行期,spring的ioc创建是基于xml还是注解的，并且说明位置(@ContextConfiguration)
    * 属性：
        * locations:指定xml文件的位置，加上classpath关键字，表示在类路径下
        * classes:指定注解类所在的位置
* 代码示例

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:bean.xml")
public class Test{
    
}
```

## AOP(面向切面编程)
* 作用：在程序运行期间，不修改源码对已有方法进行增强（动态代理）
* 优势：
    * 减少重复代码
    * 提高开发效率
    * 维护方便
* 一些术语
    * 连接点（Joinpoint）：被代理对象的所有方法
    * 切入点（Pointcut）：被增强的方法
    * 通知/增强（Advice）：指拦截到Joinpoint之后要做的事情
        * 代码示例：
        
        ```java
        try{
            //开启事务
            txManager.beginTransaction();     //前置通知
            //执行操作
            rtValue = method.invoke(accountService,args);
            //提交事务
            txManager.commit();               //后置通知
            //返回结果
            return rtValue;
        } catch(Exception e){
            //回滚操作
            txManager.rollback();             //异常通知
            throw new RuntimeException(e);
        } finally{
            //释放连接
            txManager.release();              //最终通知
        }
        ```

# spring中基于XML的AOP配置步骤
* 把通知Bean也交给spring来管理
* 使用==aop:config==标签表明开始AOP的配置
* 使用==aop:aspect==标签表明配置切面
    * id：给切面配置一个唯一标志
    * ref属性：是指定通知类bean的id
* 在aop:aspect标签的内部使用对应标签来配置通知的类型
    * ==aop:before==:表示配置前置通知
        * method属性：用于指定Logger类中哪个方法是前置通知
        * pointcut属性：用于指定切入点表达式，该表达式的含义指的是对业务层中哪个方法增强
        
        ```markdown
        切入点表达式的写法：
            * 关键字：execution(表达式)
            * 表达式：
                访问修饰符  返回值  全类名.方法名(参数列表)
            例：
                public void com.learn.service.impl.AccountServiceImpl.saveAccount()
            * 全统配写法
                * *..*.*(..)   
            * 实际开发中切入点表达式的通常写法：
                切到业务层实现类下的所有方法
        ```
* 代码示例
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--配置spring的ioc，把service对象配置进来-->
    <bean id="accountService" class="com.learn.service.impl.AccountServiceImpl"></bean>

    <!--spring中基于XML的AOP配置步骤-->
    <!--配置Logger类-->
    <bean id="logger" class="com.learn.utils.Logger"></bean>

    <!--配置AOP-->
    <aop:config>
        <!--配置切面-->
        <aop:aspect id="logAdvice" ref="logger">
            <!--配置通知的类型，并且建立通知方法和切入点方法的关联-->  
            <!--
                前置：before            切入点方法之前执行
                后置：after-returning   切入点方法正常执行之后执行
                异常：after-throwing    切入点方法执行产生异常之后执行
                最终：after             无论切入点方法是否正常执行，最后都要执行
            -->
            <aop:before method="printLog" pointcut="execution(public void com.learn.service.impl.AccountServiceImpl.saveAccount())"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```

```java
//测试类
public static void main(String[] args) {
    //获取容器
    ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
    //获取对象
    IAccountService as = (IAccountService) ac.getBean("accountService");
    //执行方法
    as.saveAccount();
}
```

* 对代码中的切入点表达式的写法优化

```xml
<!--配置AOP-->
<aop:config>
    <!--配置切入点表达式  id属性用于指定表达式唯一标识。 expression属性用于指定表达式内容
        放在aspect内外作用域不同，如果放在外面，必须放在aspect标签前，不然会报错-->
    <aop:pointcut id="pt1" expression="execution(public void com.learn.service.impl.AccountServiceImpl.saveAccount())"/>
    <!--配置切面-->
    <aop:aspect id="logAdvice" ref="logger">
        <!--配置通知的类型，并且建立通知方法和切入点方法的关联-->
        <aop:before method="printLog" pointcut-ref="pt1"></aop:before>
    </aop:aspect>
</aop:config>
```

# spring代码配置通知
* 接口：ProceedingJoinPoint
* 方法：proceed()，相当于明确调用切入点方法
* 作用：spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式。
* 代码示例：

```java
public Object aroundPringLog(ProceedingJoinPoint pjp){
    Object rtValue = null;
    try{
        Object[] args = pjp.getArgs();    //得到方法执行所需的参数
        /*
            前置通知
        */
        rtValue = pjp.proceed(args);
        /*
            后置通知
        */
    }catch(Throwable t){
        /*
            异常通知
        */
    }finally{
        /*
            最终通知
        */
    }
    return rtValue;
}
```

# 注解配置AOP

| 注解            | 作用                   |
| --------------- | ---------------------- |
| @Aspect         | 提示为通知类，放在类上 |
| @Before         | 前置通知               |
| @AfterReturning | 后置通知               |
| @AfterThrowing  | 异常通知               |
| @After          | 最终通知               |
| @Around         | 环绕通知               |
| @Pointcut       | 切入点表达式           |

* 开启spring注解AOP的支持

```xml
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
或
@EnableAspectJAutoProxy
```







​        