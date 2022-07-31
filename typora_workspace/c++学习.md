# Struct

## c和c++中struct的区别

| C                                                      | C++                                                          |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| 不能将函数放在结构体声明                               | 能将函数放在结构体声明                                       |
| 在C结构体声明中不能使用C++访问修饰符。                 | public、protected、private 在C++中可以使用。                 |
| 在C中定义结构体变量，如果使用了下面定义必须加struct。  | 可以不加struct                                               |
| 结构体不能继承（没有这一概念）。                       | 可以继承                                                     |
| 若结构体的名字与函数名相同，可以正常运行且正常的调用！ | 若结构体的名字与函数名相同，使用结构体，只能使用带struct定义 |

* 继承案例

```c++
#include<iostream>
#include<stdio.h>
struct Base {         
    int v1;
//    private:   //error!
        int v3;
    public:   //显示声明public
        int v2;
    virtual void print(){       
        printf("%s\n","Base");
    };    
};
struct Derived:Base {         

    public:
        int v2;
    void print(){       
        printf("%s\n","Derived");
    };    
};
int main() {
    Base *b=new Derived();
    b->print();
    return 0;
}
```

* 同名函数

```c++
#include<iostream>
#include<stdio.h>

struct Base {         
    int v1;
//    private:   //error!
        int v3;
    public:     //显示声明public
        int v2;
    void print(){       
        printf("%s\n","hello world");
    };    
};

typedef struct Base1 {         
    int v1;
//    private:   //error!
        int v3;
    public:     //显示声明public
        int v2;
    void print(){       
        printf("%s\n","hello world");
    };    
}B;
void Base(){
    printf("%s\n","I am Base func");
}
//void B() {}  //error! 符号 "B" 已经被定义为一个 "struct Base1" 的别名
int main() {
    struct Base base;  //ok
    //Base base1;  // error!
    base.v1=1;
    base.v3=2;
    base.print();
    printf("%d\n",base.v1);
    printf("%d\n",base.v3);
    Base();
    return 0;
}
```

## struct和class的区别

**struct**作为数据结构的实现体，它默认的数据访问控制是**public** 的，

**class**作为对象的实现体，它默认的成员变量访问控制是**private**的。