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



## ArrayList序列化特点

ArrayList底层是数组，在其序列化的时候会对数组的每一个元素进行序列化，这样就可以去掉无效元素，节省序列化空间

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    private static final long serialVersionUID = 8683452581122892189L;
    transient Object[] elementData; // non-private to simplify nested class access
    private int size;
}
```

readObject：

```java
private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }
```

writeObject：

```java
private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }
```



# 字符集

## Unicode和UTF-8

Unicode 是字符集。UTF-8 是编码规则。

UTF-8使用可变长度字节来储存 Unicode字符

| 语言                           | 所占字节 |
| ------------------------------ | -------- |
| ASCII                          | 1        |
| 重音文字、希腊字母或西里尔字母 | 2        |
| 常用汉字                       | 3        |
| 辅助平面字符                   | 4        |

## GBK GBK2312 GB18030

GB2312（1980年）：16位字符集，收录有6763个简体汉字，682个符号，共7445个字符； 优点：适用于简体中文环境，属于中国国家标准，通行于大陆，新加坡等地也使用此编码； 缺点：不兼容繁体中文，其汉字集合过少。

GBK（1995年）：16位字符集，收录有21003个汉字，883个符号，共21886个字符； 优点：适用于简繁中文共存的环境，为简体Windows所使用（代码页cp936），向下完全兼容gb2312，向上支持 ISO-10646 国际标准 ；所有字符都可以一对一映射到unicode2.0上； 缺点：不属于官方标准，和big5之间需要转换；很多搜索引擎都不能很好地支持GBK汉字。

GB18030（2000年）：32位字符集；收录了27484个汉字，同时收录了藏文、蒙文、维吾尔文等主要的少数民族文字。 优点：可以收录所有你能想到的文字和符号，属于中国最新的国家标准； 缺点：目前支持它的软件较少。



# 语法糖

## switch支持string类型

```java
String str = "world";
        switch (str) {
        case "hello":
            System.out.println("hello");
            break;
```

反编译后：

```java
String str = "world";
        String s;
        switch((s = str).hashCode())
        {
        case 99162322:
            if(s.equals("hello"))
                System.out.println("hello");
            break;
        }
```

**字符串的switch是通过`equals()`和`hashCode()`方法来实现的**，且hashCode()返回的是int类型

## 泛型擦除

虚拟机中没有泛型，只有普通类和普通方法，所有泛型类的类型参数在**编译时**都会被擦除，泛型类并没有自己独有的`Class`类对象。比如并不存在`List<String>.class`或是`List<Integer>.class`，而只有`List.class`。

## 自动拆装箱

1. 自动装箱

   ```java
   int i = 10;
   Integer n = i;
   // 等同于
   int i = 10;
   Integer n = Integer.valueOf(i);
   ```

2. 自动拆箱

   ```java
   Integer i = 10;
   int n = i;
   // 等同于
   Integer i = Integer.valueOf(10);
   int n = i.intValue();
   ```

## 方法变长参数

```java
String... strs
// 编译后为
String strs[]
```

## 枚举

```java
public enum t {
    SPRING,SUMMER;
}
```

反编译后：

```java
public final class T extends Enum
{
    private T(String s, int i)
    {
        super(s, i);
    }
    public static T[] values()
    {
        T at[];
        int i;
        T at1[];
        System.arraycopy(at = ENUM$VALUES, 0, at1 = new T[i = at.length], 0, i);
        return at1;
    }

    public static T valueOf(String s)
    {
        return (T)Enum.valueOf(demo/T, s);
    }

    public static final T SPRING;
    public static final T SUMMER;
    private static final T ENUM$VALUES[];
    static
    {
        SPRING = new T("SPRING", 0);
        SUMMER = new T("SUMMER", 1);
        ENUM$VALUES = (new T[] {
            SPRING, SUMMER
        });
    }
}
```

## 内部类

`outer.java`里面定义了一个内部类`inner`，一旦编译成功，就会生成两个完全不同的`.class`文件了，分别是`outer.class`和`outer$inner.class`。

进行反编译时，会同时把两个文件全部进行反编译。

## 条件编译

但有时候出于对程序代码优化的考虑，希望只对其中一部分内容进行编译，此时就需要在程序中加上条件，让编译器只对满足条件的代码进行编译，将不满足条件的代码舍弃，这就是条件编译。

```java
final boolean DEBUG = true;
if(DEBUG) {
    System.out.println("Hello, DEBUG!");
}
// 编译后
boolean DEBUG = true;
System.out.println("Hello, DEBUG!");
```

## 断言

```java
int a = 1;
int b = 1;
assert a == b;
System.out.println("next");
```

编译后：

```java
int a = 1;
int b = 1;
if(!$assertionsDisabled && a != b)
    throw new AssertionError();
System.out.println("abc");
```

断言底层就是if语句，断言为false就抛出异常，true就继续执行。

## 数值字面量

java 7中，可以在数字之间插入任意多下划线，方便阅读

```java
int i = 10_000;
// 反编译后
int i = 10000;
```

## for-each

for-each的实现原理其实就是使用了**普通的for循环**和**迭代器**。

## try-with-resource

```java
public static void main(String... args) {
    try (BufferedReader br = new BufferedReader(new FileReader("d:\\ hollischuang.xml"))) {
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        // handle exception
    }
}
```

看似关资源的方式很方便，但是背后是编译器帮我们做了关闭资源的操作。

## lambda

