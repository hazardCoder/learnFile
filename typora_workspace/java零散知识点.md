# JVM

对于非volatile类型的long和double变量，JVM允许将64位的读操作或写操作分解成两个32位的操作。

---

Runtime.getRuntime().exec()非常消耗资源；执行的过程是：

1. 首先复制和当前虚拟机拥有一样的环境变量的进程
2. 再用这个新进程取执行外部命令
3. 最后退出这个进程

---

类加载过程中有且仅有六种情况必须立即对类进行==初始化==

1. 遇到**new**、**getstatic**、**putstatic**、**invokestatic**四条字节码指令时，没有经过初始化会触发初始化阶段
   - 生成实例化对象
   - 读取或设置一个类型的静态字段
   - 调用一个类型的静态方法
2. 使用java.lang.reflect包对类型进行**反射**时，类型没有经过初始化会触发初始化阶段
3. 当初始化类时，**父类**没有经过初始化，会触发父类的初始化阶段
4. 虚拟机启动时，用户要指定执行的**主类**，会触发这个主类的初始化阶段
5. JDK7的动态语言支持如果一个java.lang.invoke.MethodHandle实例最后的解析结果为**REF_getStatic**、**REF_putStatic**、**REF_invokeStatic**、**REF_newInvokeSpecial**四种类型的方法句柄，并且这个句柄对应的类没有进行过初始化，会触发初始化阶段
6. JDK8中新加入的默认方法(default)，如果**接口**的实现类发生了初始化，接口要在这之前触发初始化阶段

---

虚拟机（编译器）在**重载**时是通过参数的**静态类型**而不是实际类型作为判定依据的

```java
Human man = new Man();
Human women = new Women();
StaticDispatch sr = new StaticDispatch();
sr.sayHello(man);
sr.sayHello(women);

// -----------------
hello,guy!
hello,guy!
```

# 线程同步

加锁机制既可以确保可见性又可以确保原子性，而volatile变量只能确保可见性

# 数据类型

类型转换：char->int->long->float->double

char到byte或short的转型是不安全的

# 基础

## switch

switch底层最终是比较的整型值，其他类型会进行转换，如：

| 类型   | 比较     |
| ------ | -------- |
| int    | int      |
| char   | ascii    |
| string | hashcode |

## String长度

**首先**是源码中，string内部为一个char数组，可支持的最大长度为2^31-1，是值实际运行时能支持的最大长度；

**然后**在JVM中，CONSTANT_String_info 用于表示 java.lang.String 类型的常量对象

```
CONSTANT_String_info {
    u1 tag;
    u2 string_index;
}
```

u2表示两个字节的无符号数，那么1个字节有8位，2个字节就有16位，可表示的最大值位2^16 - 1 = 65535。

**最后**在源码中，在Gen类中有如下代码

```java
private void checkStringConstant(DiagnosticPosition var1, Object var2) {
    if (this.nerrs == 0 && var2 != null && var2 instanceof String && ((String)var2).length() >= 65535) {
        this.log.error(var1, "limit.string", new Object[0]);
        ++this.nerrs;
    }
}
```

综上，可知：

| 时期   | 最大长度              |
| ------ | --------------------- |
| 编译器 | 65534                 |
| 运行期 | 2^31-1（int最大长度） |

## 序列化类型

要求被序列化的类必须属于**Enum**、**Array**和**Serializable**类型其中的任何一种。



## Arrays.asList

1. asList 得到的只是一个 Arrays 的内部类，一个原来数组的视图 List，因此如果对它进行**增删**操作会报错
2. 用 ArrayList 的构造器可以将其转变成真正的 ArrayList



## SPI

**API和SPI的关系：**

API Application Programming Interface

大多数情况下，都是实现方来制定接口并完成对接口的不同实现，调用方仅仅依赖却无权选择不同实现。

SPI Service Provider Interface

而如果是调用方来制定接口，实现方来针对接口来实现不同的实现。**调用方来选择自己需要的实现方**。



