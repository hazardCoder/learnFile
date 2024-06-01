# 逻辑语句

```python
if total > 50:
	print "You get free shopping"
elif total > 40:
	print "Spend a bit more to get free shopping"
else:
	print "Spend $50 to get free shopping"
```

# try/catch

```python
try:
	5/0
except [ZeroDivisionError]:   //中括号中是可以指定特定的错误，选填
	print "please don't do that"   //如果想在except代码块中不做任何操作，可以写pass
```

# print不换行

可以在print语句结尾加,号

```python
print 'Apple:',
print '$ 1.99 / 1b'
```

# 字符串常用函数

| API                        | 作用                                           |
| -------------------------- | ---------------------------------------------- |
| len(str)                   | 长度                                           |
| str.upper()                | 转大写                                         |
| str.lower()                | 转小写                                         |
| str.capitalize()           | 首字母大写，其余小写                           |
| str.title()                | 单词首字母大写                                 |
| str.isdigit()              | 是否数字，返回布尔值                           |
| str.isalpha                | 是否字符串，返回布尔值                         |
| str.strip()                | 删除开头结尾空格                               |
| str.lstrip('*')            | 删除左边的星号，同样可以写右边                 |
| str.count('str1')          | 查找str1的出现次数                             |
| str.find('str1')           | 查找位置，返回第一次出现的位置，没找到返回-    |
| str.replace('str1','str2') | 将str1替换成str2                               |
| float(str)                 | 字符串转换为浮点数，返回字符串                 |
| int(obj)                   | 将任何值转换为整数，但浮点数样式的字符串会报错 |

#  字符串乘法

```python
s = 'hello'
s*3
=>'hello hello hello'   //乘3相当于重复了三次
```

# 输入输出

```python
>>number = input()
// 可以输入数字和字符串，在输入字符串时，需要用引号包裹

>>number = raw_input()
// raw_input比input安全一点，如果发生上述情况，那么raw_input不会报错，可以方便后续认为清理数据

>>number = raw_input("please input:")
// 括号中为提示语句

>>password = getpass()
// 输入不会显示在屏幕上，相当于密码保护的作用，如果不给提示的话，默认提示password:
```

## 1.格式化输入

```
>>greeting = "Good {},{}.How are you doing?"
>>name = "Hahhh"
>>time = "morning"
>>print greet.format{time,name}
Good moring,Hahhh.How are you doing?
// format函数可以实现字符串拼接

>>special_text = "Good {time}! Today's special are:{special1} and {special2}."
>>time = "afternoon"
>>food1 = "spam with eggs"
>>food2 = "eggs with spam"
>>print special_text.format(time=time,special1=food1,special2=food2)
Good afternoon! Today's special are:spam with eggs and eggs whth spam
//可以用键值对的方式对应上，键可以替换为索引值，比如0、1、2
//给的参数数只能多不能少，少会报错，多会舍弃多余的部分
```

# 列表

> 列表索引从0开始，如果索引取负数，那么是从后往前找

## 1. 创建列表

```python
>>fruit = ['apple','orange','pear']
>>print(fruit)

// 空
>>fruit = []

>>fruit1 = 'apple'
>>fruit2 = 'orange'
>>fruit = [fruit1,fruit2]
```

## 2.列表相关信息

| 方法              | 备注                              |
| ----------------- | --------------------------------- |
| len(list)         | 返回列表长度                      |
| list.count('str') | 统计str在列表中出现的总次数       |
| list.index('str') | 查询str在列表中出现的位置下标     |
| 'str' in list     | 判断列表中是否存在str，返回布尔值 |

## 3.操作列表

```python
//动态添加
>>fruit = []
>>fruit.append('apple')
>>fruit.append('orange')

//将两个列表组合起来，最后存在于fruit1中
>>fruit1 = ['apple','orange']
>>fruit2 = ['pear']
>>fruit1.extend(fruit2)

//删除一个元素
>>fruit = ['apple','orange']
>>fruit.remove('apple')

//插入一个元素
>>fruit = ['apple','orange']
>>fruit.insert(1,'pear')
```

## 4.列表中的数学运算

```python
// 相加
>>fruit1 = ['apple','orange']
>>fruit2 = ['pear']
>>print(fruit1 + fruit2)
['apple', 'orange', 'pear']

// 相乘
>>fruit1 = ['apple','orange']
>>print(fruit1 * 3)
['apple', 'orange', 'apple', 'orange', 'apple', 'orange']
```

## 5.排序

```
// 反转
list.reverse()

// 排序，字符串按字母，数字按大小
// 既包含数字，也包含字母时，先排数字，再排字母
list.sort()
```

## 6.比较

> 可以用==号进行比较，只有当每一项都对应相同时，才会返回true
>
> 顺序不同也会被视为不同

# 循环

```
for {var} in {list}:   //其中var可以随意命名

循环中创建的变量{var}在循环结束时不会消失，而是会保留最后一项的内容
```



## 1.获取数字范围

| 函数          | 结果        | 备注                       |
| ------------- | ----------- | -------------------------- |
| range(5)      | [0,1,2,3,4] | 获得[0,5)的连续列表        |
| range(1,5)    | [1,2,3,4]   | 获得[1,5)的连续列表        |
| range(2,10,2) | [2,4,6,8]   | 获得步长为2，从2开始的列表 |

## 2.列表遍历

```python
fruit1 = ['apple','orange']
for fruit in fruit1:
	print(fruit)
```

## 3.跳出循环

```
// 也可以使用continue，break关键字进行中断操作

// 较为特殊的else,当没有发生break时，会执行else中的内容
fruit1 = ['apple','orange']
for fruit in fruit1:
	if fruit == 'pear':
		break
	print(fruit)
else:
	print("no break")

apple
orange
no break
```

## 4.while

```
while 条件:
	xxx
	xxx
```

# 脚本

## 1.函数

```python
def function_name(parameter1,parameter2):
	XXX
	XXX
	XXX
```

```python
// 无参
def hello():
    print("hello world!")
hello();

// 有参
def hello(name):
    print("hello,{}".format(name))
hello("Jack");

// 对应传参
def hello(name, value):
    print("{}->{}".format(name, value))
hello(name="Jack", value="hello");

// 参数默认值，设置参数的默认值只能放在最后，前面让python填充
def hello(name, value, de = ''):
    print("{}->{}".format(name, value))
hello(name="Jack", value="hello");

// 返回值,直接return，如果返回多个值，可以用逗号隔开
def hello()
	return 2,3

// 多个值接收参数
def hello():
    return 2,3

re1,re2 = hello()
print(re1)
print(re2)

// 未知传入参数数量时，末尾加**kwargs，保存在此字典中，相当于Map，替换成*args是替换成元组
def test_args(item_one, item_two, **kwargs):
    print(item_one)
    print(item_two)
    print(kwargs)
test_args(item_one=1, item_two=2, item_three=3)

// 启动函数
if __name__ = "__main__"
	main()
```

# 字典

## 1.创建字典

```python
state = {}

state = {"Virginia":"Richmond","Maryland":"Annapolis"}
```

## 2.读取

```
// 写，键不存在时写入，如果键存在，那么会覆盖值
states['Oregon'] = "Salem"

// 删除
states.pop('Virginia')

// 读
states['Oregeon']

```

## 3.常用函数

| 函数           | 备注                                                  |
| -------------- | ----------------------------------------------------- |
| has_key('str') | 是否存在键str，返回布尔值                             |
| 'str' in d     | d是字典，和上方函数功能相同，查找是否存在，返回布尔值 |
| b.keys()       | 获得键集合                                            |
| b.values()     | 获得值集合                                            |

## 4.比较

> 字典的比较不像列表一样，只要键值对相等，那么字典就相等，无论顺序

# 对象

## 1.创建对象

```python
class myclass(object):
	a = 5
	b = 2
    def function_name(self):
        XXXX
        XXXX
```

## 2.\__init__()

> 在创建新实例的时候默认执行的函数
>
> 可以用于赋予默认值

```python
class Student(object):
    def __init__(self,num):
        self.num = num
```

## 3.\__eq__()

>定义对象**相等**的条件

```
class Student(object):
    def __init__(self,num):
        self.num = num
    def __eq__(self, other):
        if self.num == other.num:
            return True
        else:
            return False
```

## 4.\__ne__()

> 定义对象**不相等**的条件

```python
class Student(object):
    def __init__(self,num):
        self.num = num
    def __eq__(self, other):
        if self.num == other.num:
            return True
        else:
            return False
    def __ne__(self, other):
        if self.num != other.num:
            return True
        else:
            return False

student1 = Student(5)
student2 = Student(5)
student3 = Student(7)
print(student1 != student2)
print(student2 != student3)
```

## 5.其他函数

### 1.比较

| 方法       | 备注     |
| ---------- | -------- |
| \__gt__()  | 大于     |
| \__lt__()  | 小于     |
| \__gte__() | 大于等于 |
| \__lte__() | 小于等于 |

### 2.打印

> \__str__()
>
> 打印返回的字符串

```python
class Student(object):
    def __init__(self, num):
        self.num = num

    def __eq__(self, other):
        if self.num == other.num:
            return True
        else:
            return False

    def __ne__(self, other):
        if self.num != other.num:
            return True
        else:
            return False

    def __str__(self):
        return str(self.num)


student1 = Student(5)
print(student1)

```

## 6.继承

```python
// 说明Book继承自InventoryItem，代替上述的object
class Book(InventoryItem):
    def __init__(self,title,price):
        super(Book,self).__init__(title = title)
        	self.description = description
```

# 使用包

```python
// 多种导入形式
import module
from module import class
from module import function
from module import *
```

## 1.常见包

| 包       | 备注                                     |
| -------- | ---------------------------------------- |
| random   | 生成随机数                               |
| os       | 用于与操作系统交互                       |
| json     | 读取json文件，提供了存储和共享数据的方法 |
| sqlite3  | 创建、编辑和读取SQLite数据库             |
| datetime | 操作日期和时间                           |
| getpass  | 获取用户敏感信息                         |
| this     | 复活节彩蛋，将输出Python的信息           |
| pprint   | 以更易于阅读的格式打印数据               |

## 2.应用

### 1.randint

> 打印指定范围的随机数

```python
from random import randint
for i in range(10):
    print(randint(1,10))
```

### 2.random

> 打印0到1之间的浮点数

```python
from random import random
for i in range(10):
    print(random())
```

### 3.uniform

> 指定区间的浮点数

```python
from random import uniform
for i in range(10):
    print(uniform(1,5))
```

### 4.choice

> 从指定的列表或元组中随机返回一项
>
> 其余的random函数可以通过help(random)显示模块文档

```python
from random import choice
names = ['Hannah','Jacob','Jim','Katie']
print(choice(names))
```

### 5.time

> 用于创建和操作时间对象
>
> time()不需要任何参数，但是可以接受4个整数，分别复制给小时、分钟、秒和微妙。
>
> time还可以进行比较时间先后

```python
from datetime import time
lunch = time(11,30)
morning = time(9,20)
print(lunch)
print(lunch.hour)
print(morning > lunch)
```

### 6.datetime

> 可以保存日期和时间

```
import datetime
hm = datetime.datetime(year=2020,day=9,month=1)
jt = datetime.datetime(year=2021,day=14,month=2)
print(jt-hm)   //显示的是两个日期相隔的日期和时间

import datetime
d = datetime.datetime(2020,1,17,15,12,30,00)
print(d)

// 今天
today = datetime.datetime.now()
// +1周
week = datetime.timedelta(days = 7)
today + week
```

## 3.导包提示

> 比如文件结构如下
>
> <img src="C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117153154475.png" alt="image-20210117153154475" style="zoom:80%;" />
>
> 在其他程序中导入monsters.py时，不能够只加上"from monsters import Monster"，需要包含文件的文件目录，因为python是从“我的电脑”中查找所有目录
>
> 如果想告诉python将目录下的一个文件夹当作包一样使用，需要增加一个文件：\__init__.py，通常是一个空文件，目的就是告诉python，想使用该目录的代码。
>
> 新的文件结构如下：
>
> <img src="C:\Users\Qian\AppData\Roaming\Typora\typora-user-images\image-20210117154809559.png" alt="image-20210117154809559" style="zoom:80%;" />
>
> 此时可以像如下一样导入类
> from classes.monsters import Monster

注意：中划线(-)在python中会被特殊对待。

# 文件操作

## 1.open()

> 打开一个文件，根路径位于当前文件的根目录
>
> 使用a或w+模式时，文件不存在，则会创建，存在则打开
>
> 区别在于，a会保留内容，w+会擦除内容

```python
f = open('users.txt')

# 读写权打开
f = open('users.txt','r+w')

# 用此方式打开需要注意，会擦除文件中所有数据，所以要确保保存该文件
f = open('users.txt','w')

# 文件打开，游标自动移动到文件的末尾，可以实现末尾插入
f = open('users.txt','a')


```

## 2.readlines()

> 读取文件全部内容

```python
test = f.readlines()

# 只读取一行，执行后游标下移，再次读取就是第二行的内容
f.readline()
```

## 3.close()

> 关闭文件

```python
f.close()
```

## 4.write()

> 写文件，需要有写权
>
> 写的**起点**是文件的**最开头**

```python
f.write('test')
```

## 5.writelines()

> 和readlines相反
> readlines会输出一个列表
>
> writelines会接收一个列表

```python
f = open('users.txt')
lines = f.readlines()
f.close()
f = open('users.txt','w')
f.writelines(lines)
f.close()
```

# 获取目录信息

> os库

## 1.getcwd()

	>获取当前文件目录

```python
import os
os.getcwd()
```

## 2.listdir()

> 获取目录所有文件，接收参数为目录

```python
import os
index = os.getcwd()
files = os.listdir(index)
print(files)

# 获取当前目录
os.listdir('.')

# 其他
os.listdir('/tmp/')
```

## 3.walk()

> 遍历目录
>
> 返回一个对象，对象内容包括目录的路径、目录中的子目录、目录中的文件

```python
# 当再次next时，则会进到子目录，开始以子目录为对象输出内容
import os
index_dirs = os.walk('.')
print(index_dirs.__next__())
```

## 4.makedir() 和makedirs()

> 创建目录
>
> 接收的参数为路径
>
> makedir最多创造一级，makedirs可以多级

```python
# 其中，/Users/test已经存在
os.makedir('/Users/test/test1')

# 其中，/Users/test已经存在
os.makedirs('/Users/test/test1/test2')
```

## 5.stat()

> 获取文件信息
>
> 接收参数为文件路径

| 参数              | 备注                 |
| ----------------- | -------------------- |
| st_size           | 文件大小，字节为单位 |
| st_atime/st_mtime | 访问时间，秒数为单位 |

```python
import os
index_dirs = os.stat('users.txt')
print(index_dirs)

输出：
os.stat_result(st_mode=33206, st_ino=5910974511109719, st_dev=4133075926, st_nlink=1, st_uid=0, st_gid=0, st_size=72, st_atime=1611388520, st_mtime=1611388444, st_ctime=1611388338)
```

# JSON

> 一个JSON文件只能保存一个JSON对象
>
> JSON实质上也是一个字典

## 1.读

> 先打开，然后用load加载成一个对象

```python
import json
f = open('car.json')
car = json.load(f)
```

## 2.写

>dump函数可以写入json，第一个参数是待写入的对象，第二个是写入的文件
>
>第三个参数indent可选，传入整数后，每个键值对都是独立的一行

```python
import json
f = open('car.json')
car = json.load(f)
f.close()
car['mycar']['color'] = 'red'
f = open('car.json','w')
json.dump(car,f,indent=2)
f.close()
```

## 3.打印到屏幕

>dumps函数，以字符串的形式返回一个有效的JSON

```python
import json
f = open('car.json')
car = json.load(f)
print json.dumps(car,indent)
```

## 4.对象转JSON

> vars，把一个对象中所存储的所有属性返回到一个字典中

```python
import json
f = open('newcar.json','w')
json.dump(vars(mycar),f,idnent=2)
f.close()
```

## 5.创建定制字典

> 使用环境：当一个类中包含另一个类
>
> 解决方案：增加一个函数，返回想要编码的字典
>
> 例子：教室包含了多个学生，那么就在教室类中增加如下函数
>
> 解释：相当于将学生对象循环抽取出来，另外弄了一个字典将多个学生对象保存

```python
def get_JSON_dict(self):
	d = vars(self)
	student_list = []
	for student in self.students:
		student_list.append(vars(student))
	d['students'] = student_list
	return d
```

# 数据库

> 需要导入数据库相关包，如sqlite3
>
> 具体的导包可以进行搜索，每个数据库不一样
>
> 使用步骤：
>
> 1. 导包
>
>  	2. 建立数据库连接
>  	3. 获取游标
>  	4. 执行sql，可以使用通用的sql语句
>  	5. 关闭游标

# Flask

首先要安装setuptools和pip，可以使得安装、升级和卸载Python包过程变得便利

## 1.setuptools下载

> https://pypi.python.org/pypi/setuptools

pip安装和setuptools安装类似，最新版的python已经自带这两个工具，所以无需重装

### 1.pip下载包

> pip install [-i https://pypi.tuna.tsinghua.edu.cn/simple] 包名[==版本号]   清华源
>
> pip install [-i https://pypi.doubanio.com/simple/] 包名[==版本号]   豆瓣源

## 2.flask安装

控制台下运行pip install flask

## 3.入门应用

```python
from flask import Flask
# 设置Flask实例
app = Flask(__name__)

#装饰器，类似注解，表明页面从何处提供
@app.route('/')
def hello_world():
    return "Hello World!"

@app.route('/test/')
def test_page():
    return "This is a test page."

if __name__ == '__main__':
    app.run()
```

可以看到提示，此时已经开启了一个web应用

### 1.添加变量

```python
@app.route('/<name>/')
def name_page(name):
    return f"Hello,{name}"
```

## 4.使用模板

> 将HTML和Jinja组合
>
> HTML任务：如何架构网页
>
> JInja任务：把动态的信息插入到页面中

### 1.HTML应用

在同等目录下创建templates，用于放置模板

创建html文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p>Hello there, new user</p>
<p>Here is your lucky number:</p>
<p>5</p>
</body>
</html>
```

### 2.Jinja使用

> 添加动态内容，主要时在html文件中进行操作

| 占位符 | 备注       |
| ------ | ---------- |
| {{}}   | 数据占位符 |
| {%%}   | 流程控制   |

首先更新html代码，如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p>Hello there, new user</p>
<p>Here is your lucky number:</p>
<p>{{ lucky_num }}</p>
{% if lucky_num == 99 %}
    <p>Wow!That's super lucky!</p>
{% endif %}
</body>
</html>
```

再更新python代码

```python
from flask import Flask
from flask import render_template
from random import randint

app = Flask(__name__)

@app.route('/')
def lucky():
    lucky_num = randint(1,99)
    return render_template('lucky.html',lucky_num=lucky_num)

if __name__ == '__main__':
    app.run()
```

# PyGame

> 如你所见，这是个制作游戏的库

## 1.安装

下载地址：

www.pygame.org/download.shtml

安装命令：

```shell
py -m pip install -U pygame --user
```

注意：上述命令需要pip支持

## 2.入门

### 1.开始程序

```python
import pygame
from pygame.locals import *

# 初始化
pygame.init()
# 创建一个窗口
screen = pygame.display.set_mode((400,400))
# 维持窗口显示，不然会瞬关
while True:
    pass

if __name__ == '__main__':
    main()

```

此时会出现无法关闭的情况，此时将引入事件点击的概念

### 2.事件

> from pygame.locals import *
>
> 当解析用户输入的时候，会创建许多有用的变量，比如鼠标键盘的各个按钮

此时代码变为：

```python
import pygame, sys
from pygame.locals import *

# 初始化
pygame.init()
# 创建一个窗口
screen = pygame.display.set_mode((400,400))
# 维持窗口显示，不然会瞬关，并提供关闭点击事件
while True:
    for event in pygame.event.get():
        if event.type == QUIT:
            sys.exit()

if __name__ == '__main__':
    main()

```

### 3.创建形状

#### 1.添加颜色

定义为元组颜色：红、绿、蓝

比如红色：RED = (255,0,0)

#### 2.一个圆

此时代码更新为：

```python
import pygame, sys
from pygame.locals import *

# 初始化
pygame.init()
# 创建一个窗口
screen = pygame.display.set_mode((400,400))
# 圆
RED = (255,0,0)
# 写目标，颜色，圆心，半径
pygame.draw.circle(screen,RED,(20,20),10)
pygame.display.update()
# 维持窗口显示，不然会瞬关
while True:
    for event in pygame.event.get():
        if event.type == QUIT:
            sys.exit()

if __name__ == '__main__':
    main()

```

### 4.移动效果

#### 1.初步

```python
import pygame, sys
from pygame.locals import *

RED = (255,0,0)
BLACK = (0,0,0)

def main():
    # 初始化
    pygame.init()
    # 创建一个窗口
    screen = pygame.display.set_mode((400, 400))
    x=20
    y=20
    # 写目标，颜色，圆心，半径
    screen.fill(BLACK)
    pygame.draw.circle(screen, RED, (x, y), 10)
    pygame.display.update()
    # 维持窗口显示，不然会瞬关
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                sys.exit()
        x += 1
        # 重绘，用黑色填满的方式
        pygame.draw.rect(screen,BLACK,(0,0,400,300))
        pygame.draw.circle(screen,RED,(x,y),10)
        pygame.display.update()

if __name__ == '__main__':
    main()

```

#### 2.用户手动移动

> 去除自动移动功能块，增加简单的逻辑操作

```python
import pygame, sys
from pygame.locals import *

RED = (255,0,0)
BLACK = (0,0,0)

def main():
    # 初始化
    pygame.init()
    # 创建一个窗口
    screen = pygame.display.set_mode((400, 400))
    x=20
    y=20
    # 写目标，颜色，圆心，半径
    screen.fill(BLACK)
    pygame.draw.circle(screen, RED, (x, y), 10)
    pygame.display.update()
    # 维持窗口显示，不然会瞬关
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                sys.exit()
            if event.type == KEYDOWN:
                if event.key == K_LEFT:
                    x -= 1
                elif event.key == K_RIGHT:
                    x += 1
                elif event.key == K_UP:
                    y -= 1
                elif event.key == K_DOWN:
                    y += 1
        # 重绘，用黑色填满的方式
        pygame.draw.rect(screen, BLACK, (0, 0, 400, 300))
        pygame.draw.circle(screen, RED, (x, y), 10)
        pygame.display.update()

if __name__ == '__main__':
    main()

```

### 5.绘制文本

```python
import pygame, sys
from pygame.locals import *

RED = (255,0,0)
BLACK = (0,0,0)

def main():
    # 初始化
    pygame.init()
    # 创建一个窗口
    screen = pygame.display.set_mode((400, 400))
    
    screen.fill(BLACK)
    pygame.display.update()

    # 获取所有字体
    fonts = pygame.font.get_fonts()
    font = fonts.pop()

    while fonts:
        try:
            new_font = pygame.font.SysFont(font,30)
        except:
            pass

        text = new_font.render(font,1,(255,255,255))
        screen.fill(BLACK)
        # 渲染
        screen.blit(text,(40,40))
        pygame.display.update()
        for event in pygame.event.get():
            if event.type == KEYDOWN:
                font = fonts.pop()

if __name__ == '__main__':
    main()

```

# 调试

## 1.看traceback日志

## 2.pdb调试器

> 可以逐行执行代码，并且能够看其中的变量

如果执行了一个python代码

```shell
python usingpdb.py
```

增加pdb调试后

```shell
python -m pdb usingpdb.py
```

调试后命令表：

| 命令   | 说明                                       | 缩写 |
| ------ | ------------------------------------------ | ---- |
| args   | 显示传送给函数的参数                       | a    |
| break  | 增加一个断点                               | b    |
| cont   | 继续到下一个断点                           | c    |
| clear  | 清除所有断点                               | cl   |
| list   | 显示当前所在位置附近代码                   | l    |
| next   | 执行代码的当前行                           | n    |
| step   | 执行代码当前行，如果进入一个函数，停止执行 | s    |
| tbreak | 和break一样，但是区别是会把该断点删除      | none |

