# 1.基础语法

## 1.注释

### 1.1.单行注释

注释后面需要一个空格

单行注释和代码之间至少要有两个空格

```python
# 这是注释
print("hello")

print("hello")  # 这是单行注释
```

### 1.2.多行注释

需要用一对三个引号(单引号,双引号都可以)

```python
"""
这是多行注释
"""
---------------------
'''
这也是多行注释
'''
```

## 2.算数运算符

```python
print("取整数:4",9//2)
print("取余数:1",9%2)
print("幂次方:81",9**2)
```

乘法的使用,用`*`可以拼接字符串
```python
In [1]: "A" * 30
Out[1]: 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAA'

In [2]: 1 * 30
Out[2]: 30
```

优先级

`幂` >` *` > ` /`  > `%` > `//` >`+` > `-`

## 3.变量

### 3.1.变量的类型

数字型

* 整数 int(python2.x 分 `int` 和 `long`,Python3 只有`int`)
* 浮点 float
* 布尔
* 复数(用于科学技术的)

非数字型

- String（字符串）
- List（列表）
- Tuple（元组）
- Dictionary（字典）

### 3.2.type函数

```python
In [6]: name = "lihao"

In [7]: type(name)
Out[7]: str
```

### 3.3.不同类型的变量之间的计算

```python
In [10]: age =13

In [11]: sex =True

In [12]: height = 180.1

In [13]: age + sex
Out[13]: 14

In [14]: age + height
Out[14]: 193.1

In [15]: age + height
Out[15]: 193.1

# 字符串的拼接
In [17]: first_name ="东尼"
In [18]: last_name="安"
In [19]: last_name+first_name
Out[19]: '安东尼'

# 字符串不能和数字相加
In [20]: last_name + 10
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-20-45feb354f2d0> in <module>
----> 1 last_name + 10

TypeError: can only concatenate str (not "int") to str
```

### 3.4.变量的输入

```python
# 可以不需要参数
In [24]: input()
123

# 加参数,做提示
In [27]: pwd = input("输入数字")
输入数字123

In [28]: pwd + "456"
Out[28]: '123456'
```

### 3.5.类型转换

|         |            |
| ------- | ---------- |
| int()   | 转成int    |
| str()   | 转成字符串 |
| float() | 转成浮点   |

```python
# 数字和字符串相互转
In [3]: age = 23

In [4]: type(age)
Out[4]: int

In [5]: age_str = str(age)

In [6]: type(age_str)
Out[6]: str

In [7]: type(int(age_str))
Out[7]: int
    
# 浮点转数值,缺失精度
In [9]: pi = "3.14"
In [10]: pi
Out[10]: '3.14'
    
In [13]: type(float(pi))
Out[13]: float
    
In [15]: int(float(pi))
Out[15]: 3
```

### 3.6.变量的格式化输出

| 符号 |  描述 |
|:-------------:|:-------------:|
| %c | 格式化字符(输出数值对应的ASCII码) |
| **%s** |格式化字符串 |
| **%d** | 格式化整数(%06d,不足的补0) |
| %x | 格式化十六进制数（小写） |
| %X | 格式化十六进制数（大写） |
| %o | 格式化八进制数 |
| **%f** | 格式化浮点数字，可以指定小数点精度(%.2f) |
| **%%** |输出%号|

```python
"""'
我的名字是:anthony,请多多关照
我的学号是:000001,请多多关照
单价是:1.00,购买了2.00斤,总价是:2.000
数据的比例是:20%
"""
print("我的名字是:%s,请多多关照" % "anthony")
print("我的学号是:%06d,请多多关照" % 1)
print("单价是:%.2f,购买了%.2f斤,总价是:%.3f" % (1,2,2))
print("数据的比例是:%02d%%" % 20)
```

### 3.7.变量的命名

1. 字母,和下划线和数字组成

2. 不能以数字开始

3. 不能与关键字重名

4. 其余的符号都不行
5. 区分大小写
6. `=`左右都要添加空格
7. 两个单词之间用`_`

## 4.条件控制

### 4.1.if

```python
age = 15
if age >= 18:
    print("成年了")

    print("在一个缩进的,是一个代码块")
elif age<=18:
    print("没有成年")
else:
    print("输入错误")
print("结束了")
```

### 4.2.逻辑运算符

```python
print(True and False)
print(True or False)
print(not True)
```

### 4.3.随机数

```python
In [17]: import random

In [18]: random.randint(12,20)
Out[18]: 12
```

### 4.3.while

```python
i = 0
while i < 5:
    print("....")
    i += 1
```

### 4.4.continue 和 break 

如果是嵌套循环,用这个两个关键字,也只是结束当前的循环,不会影响外层的循环

## 5.函数

### 5.1.函数注释

```python
# 这个也是可以注释的
def test():
    """这个也是可以注释的"""
    print("打印乘法表")
```

### 5.2.函数调用

index.py

```python
def chengfabiao():
    print("打印乘法表")
```

test.py

```python
import index
index.chengfabiao()
```

### 5.3.局部方法修改全局变量

```python
num =10

def mo():
    # 声明num是全部变量
    global num
    num=100
    print(num)

mo()
print(num)
```

### 5.4.多个返回值

```python
def measure():
    return (1,2)

a,b = measure()
print(a)
print(b)
```

### 5.5.缺省函数

```python
def measure(age,gender=1):
    print(age)
    print(gender)

def measure2(age,gender=1,name="anthony"):
    print(age)
    print(gender)
    print(name)

measure(1)
measure(1,2)

# 有多个缺省的时候,需要指定参数名称
measure2(1,name="anthonyyang",gender=2)

# 拆包
measure(1,*(2,3,4),**{"name":"anthony"})
```

### 5.6.多值参数

习惯存元祖的使用用`*args`,存字典的时候用`**args`



## 6.容器

### 6.1.列表(Java的数据)

虽然列表可以存储不同类型的数据,但是在开发中,存储的都是相同类型数据,因为要迭代

### 6.2.元祖

元祖用的是括号

与列表比较,元祖元素不能修改

```python
In [34]: info_tuple = ("zhangsan",18,1.75)

In [35]: type(info_tuple)
Out[35]: tuple

In [36]: info_tuple[0]
Out[36]: 'zhangsan'
```

```python
# 只有一个元素的时候,就不是tuple类型
In [39]: int_tuple = (5)
In [41]: type(int_tuple)
Out[41]: int

# 只想声明一个元素的元祖,可以加个逗号
In [42]: ints_tuple = (5,)
In [43]: type(ints_tuple)
Out[43]: tuple
```

```python
In [45]: names_tumple = ("a","b","c")

In [46]: type(names_tumple)
Out[46]: tuple

In [47]: names_tumple[0]
Out[47]: 'a'

# a在元祖里有多少个
In [50]: names_tumple.count("a")
Out[50]: 1

# 查看c的索引
In [52]: names_tumple.index("c")
Out[52]: 2
```

元祖和列表相互转换

```python
# 声明元祖
In [54]: num_list = (1,2,3,4)

In [55]: type(num_list)
Out[55]: tuple

# 元祖转成列表
In [56]: my_list = list(num_list)

# 修改值
In [57]: my_list[0]=5

# 再转成元祖
In [58]: print(tuple(my_list))
(5, 2, 3, 4)
```

### 6.2.字典(java的map)

列表是有序的

字典是无序的

```python
names={"name":"xiaoming",
       "age":"23"}

# 取值
print(names["name"])

# 新增和修改(key存在,就是新增,不存在就是修改)
names["address"] ="feilvb"
names["name"] ="anthony123"
print(names)

# 删除
names.pop("name")
print(names)

# 统计键值对的数量
print(len(names))

# 合并键值对,如果合并的时候有相同的key,那个value就是更新值
temp = {"a":"b"}
names.update(temp)
print(names)

# 遍历字典
for k in names:
    print("遍历",k,names[k])

# 清空字典
names.clear()
```

### 6.3.字符串

```python
str ="hello hello"
print("字符串长度",len(str))
print("字符串出现次数",str.count("llo"))
print("取索引",str.index("llo"))
print("取值",str[1])
# 换行符,都是空白字符
print("判断空白字符",str.isspace())
print("是否以指定字符串开始",str.startswith("hello"))
print("是否以指定字符串结束",str.endswith("LLO"))
print("查找指定字符串",str.find("llo"))
print("替换字符串",str.replace("hello","HELLO"))
print(str[0:9:2])
```

## 7.公共方法

1. 内置函数:

* len
* max 只能比较字典的key
* min 只能比较字典的key

2.字符串,列表,元祖都可以切片

3.查看地址值

```python
id(str)
```

## 8.文件操作



# 2.面向对象

类名需要大驼峰命名法

## 1.基本语法

### 1.1.创建对象

```python
class Cat:

    def eat(self):
        print("小猫爱吃鱼")

    def drink(self):
        print("小猫爱喝水")

tom = Cat()
tom.eat()
tom.drink()
```

### 1.2.对象内置方法

```python
class Cat:

    def __init__(self,name):
        print("初始化方法")
        self.name=name

    def eat(self):
        print(self.name+"爱吃鱼")

    def drink(self):
        print(self.name+"爱喝水")

    def __del__(self):
        print("销毁方法")

    def __str__(self):
        return "重写tostring"

tom = Cat("Tom")
tom.eat()
tom.drink()
print(tom)
```

### 1.3.私有属性和方法

```python
class Cat:

    def __init__(self,name):
        print("初始化方法")
        self.name=name
        self.__age =18

    def eat(self):
        print(self.name+"爱吃鱼")

    def drink(self):
        print(self.name+"爱喝水")

    def say_age(self):
        print("年纪是:"+str(self.__age))
        # 调用私有方法
        self.__private_method()

    def __private_method(self):
        print("私有方法")

    def __del__(self):
        print("销毁方法")

    def __str__(self):
        return "重写tostring"

tom = Cat("Tom")
tom.eat()
tom.drink()
tom.say_age()
print(tom)
# 这种访问方式,也是可以访问到私有的属性和方法的
print(tom._Cat__age)
```

### 1.4.继承和重写

```python
class Animal:

    def __init__(self):
        self.name1 =100
        self.__num2 = 200

    def eat(self):
        print("动物吃")

    def run(self):
        print("动物跑")

    # 子类不允许调用私有方法
    def __test(self):
        print("父类可以访问到私有属性和私有方法")


class Dog(Animal):

    def run(self):
        print("子类打印,开始调用父类方法")
        super().run()
        print("调用完父类方法")


# animal = Animal()
# animal.eat()
# animal.run()

dog = Dog()
dog.eat()
dog.run()

```

### 1.5.多继承

尽量避免使用多继承,如果继承了两个累,两个类有相同的方法和属性,容易混淆

```python
class Animal:

    def __init__(self):
        self.name1 = 100
        self.__num2 = 200

    def eat(self):
        print("动物吃")

    def run(self):
        print("动物跑")

    # 子类不允许调用私有方法
    def __test(self):
        print("父类可以访问到私有属性和私有方法")


class Zoo:

    def eat(self):
        print("动物园吃饭")


class Dog(Animal, Zoo):

    def run(self):
        print("子类打印,开始调用父类方法")
        super().run()
        print("调用完父类方法")

dog = Dog()
dog.eat()
```

### 1.6.多态

```python
class Dog(object):

    def __init__(self,name):
        self.name = name

    def game(self):
        print("蹦蹦跳跳",self.name)


class Xiaotianquan(Dog):

    def game(self):
        print("哮天犬",self.name)

class Person(object):

    def __init__(self,name):
        self.name = name

    def game_with_dog(self,dog):
        print("人和狗玩耍",self.name,dog.name)

        dog.game()

# dog = Dog("旺财")
dog = Xiaotianquan("旺财")

xiaoming = Person("xiaoming")
xiaoming.game_with_dog(dog)
```



### 1.7.类属性和类方法和静态方法

类属性 相当于静态变量

```python
class Dog(object):
    # 类属性
    age = 12

    def __init__(self,name):
        self.name = name
```

类方法

```python
class Dog(object):

    # 类属性
    age = 12

    # 类方法
    @classmethod
    def show_age(cls):
        print("静态方法",cls.age)

dog = Dog()
Dog.show_age()
```

静态方法,在不用方法类属性和静态属性的时候,可以定义成静态方法

```python
class Dog(object):

    # 类属性
    age = 12

    # 类方法
    @classmethod
    def show_age(cls):
        print("类方法",cls.age)

    @staticmethod
    def static_method():
        print("静态方法")


dog = Dog()

# 调用类方法
Dog.show_age()
# 调用静态方法
Dog.static_method()
```

## 2.异常

### 2.1.异常的完整语法

```python
try:
    num = int(input("输入一个整数:"))
    10 / num
except ZeroDivisionError:
    print("请不要输入数字0")
except Exception as result:
    print("未知错误 %s" % result)
else:
    print("没有异常才会执行的代码")
finally:
    print("无论是否有异常,都会异常的代码")
```

### 2.2.主动抛异常

```python
def check(name):
    if(name == "anthony"):
        return "是安东尼"
    else:
        # 主动抛异常
        raise Exception("不是安东尼")

# 捕获异常
try:
    print(check("anthony2"))
except Exception as result:
    print(result)
```

## 3.模块

### 3.1.导入模块

不推荐使用`,`

```python
import pkg1
import pkg2
```

### 3.2.简单的使用

my_module.py

```python
title = "模块2"

def say_hello():
    print("i am module : %s " % title)

class Cat:
    pass
```

index.py

```python
import my_module

# use module method
my_module.say_hello()

dog = my_module.Cat()
print(dog)
```

### 3.3.导入的时候也可以起别名

别名要使用大驼峰命名

```python
import my_module as MyModule
```

### 3.4.from...import

导入一部分工具

使用的时候,就不需要写那个模块名了,直接使用

```python
from my_module import say_hello
from my_module import Cat
say_hello()
cat = Cat()
```

### 3.5.作为模块的正常写法

```python
def main():
    pass

# 有了这个之后,被别的模块调用的时候
if __name__ = "__main__"
	main
```

### 3.6.包

包 包含多个模块

创建一个新的文件夹,在文件夹里面创建`__init__.py`

```python
# . 是相对路径名
from . import send_message
from . import receive_message
```

在文件夹里面创建两个模块

receive_message.py

```python
def receive():
    print("接受信息")
```

send_message.py

```python
def send(text):
    print("发送 %s" % text)
```

调用模块

```python
import hm_message

hm_message.send_message.send('hello')
hm_message.receive_message.receive()
```

### 3.7.发布模块

1.创建setup.py

```python
from distutils.core import setup

setup(name="hm_message",
      version="1.0",
      description="push",
      py_modules=["hm_message.send_message",
                  "hm_message.receive_message"])
```

2.命令

```python
python setup.py build
python setup.py sdist
```

### 3.8.安装模块

```python
tar xxx.tar.gz
python setup.py install
```

### 3.9.卸载模块



# 3.打飞机实战
```bash
# 安装pygame
$ sudo pip3 install pygame

# 验证安装
$ python3 -m pygame.examples.aliens
```

## 1.pygame 快速入门

```python
import pygame

# 导入并初始化所有 `pygame` 模块，使用其他模块之前，必须先调用 `init` 方法
pygame.init()

# 游戏代码...

# 卸载所有 `pygame` 模块，在游戏结束之前调用！
pygame.quit()
```

### 1.理解游戏中的坐标系

* **坐标系**
    * **原点** 在 **左上角** `(0, 0)`
    * **x 轴** 水平方向向 **右**，逐渐增加
    * **y 轴** 垂直方向向 **下**，逐渐增加

![002_游戏窗口和坐标系-w300](img/15025046487919/002_%E6%B8%B8%E6%88%8F%E7%AA%97%E5%8F%A3%E5%92%8C%E5%9D%90%E6%A0%87%E7%B3%BB.png)

* 在游戏中，**所有可见的元素** 都是以 **矩形区域** 来描述位置的
    * 要描述一个矩形区域有四个要素：`(x, y) (width, height)`

* `pygame` 专门提供了一个类 `pygame.Rect` 用于描述 **矩形区域**

```python
Rect(x, y, width, height) -> Rect
```

* `pygame.Rect` 是一个比较特殊的类，内部只是封装了一些数字计算
* 不执行 `pygame.init()` 方法同样能够直接使用

**需求**

1. 定义 `hero_rect` 矩形描述 **英雄的位置和大小**
2. 输出英雄的 **坐标原点**（`x` 和 `y`）
3. 输出英雄的 **尺寸**（**宽度** 和 **高度**）

```python
hero_rect = pygame.Rect(100, 500, 120, 126)

print("坐标原点 %d %d" % (hero_rect.x, hero_rect.y))
print("英雄大小 %d %d" % (hero_rect.width, hero_rect.height))
# size 属性会返回矩形区域的 (宽, 高) 元组
print("英雄大小 %d %d" % hero_rect.size)
```

### 2.创建游戏主窗口

| 方法                      | 说明               |
| ------------------------- | ------------------ |
| pygame.display.set_mode() | 初始化游戏显示窗口 |
| pygame.display.update()   | 刷新屏幕内容显示   |

```python
set_mode(resolution=(0,0), flags=0, depth=0) -> Surface
```

* **作用** —— 创建游戏显示窗口
* **参数**
    * `resolution` 指定屏幕的 `宽` 和 `高`，默认创建的窗口大小和屏幕大小一致
    * `flags` 参数指定屏幕的附加选项，例如是否全屏等等，默认不需要传递
    * `depth` 参数表示颜色的位数，默认自动匹配

```python
# 创建游戏主窗口
screen = pygame.display.set_mode((480, 700))
```



### 3.理解 **图像** 并实现图像绘制

* 要在屏幕上 **看到某一个图像的内容**，需要按照三个步骤：
    1. 使用 `pygame.image.load()` **加载图像的数据**
    2. 使用 **游戏屏幕** 对象，调用 `blit` 方法 将图像绘制到指定位置
    3. 调用 `pygame.display.update()` 方法更新整个屏幕的显示

> 提示：要想在屏幕上看到绘制的结果，就一定要调用 `pygame.display.update()` 方法

```python
# 绘制背景图像
# 1> 加载图像
bg = pygame.image.load("./images/background.png")

# 2> 绘制在屏幕
screen.blit(bg, (0, 0))

# 3> 更新显示
pygame.display.update()
```

```python
# 1> 加载英雄图像
hero = pygame.image.load("./images/me1.png")

# 2> 绘制在屏幕
screen.blit(hero, (200, 500))

# 3> 更新显示
pygame.display.update()
```

### 4.理解 `update()` 方法的作用

```python
# 绘制背景图像
# 1> 加载图像
bg = pygame.image.load("./images/background.png")

# 2> 绘制在屏幕
screen.blit(bg, (0, 0))

# 绘制英雄图像
# 1> 加载图像
hero = pygame.image.load("./images/me1.png")

# 2> 绘制在屏幕
screen.blit(hero, (200, 500))

# 3> 更新显示 - update 方法会把之前所有绘制的结果，一次性更新到屏幕窗口上
pygame.display.update()
```

### 5. 游戏时钟

*  `pygame.time.Clock`提供帧率,就是一秒钟运行多少次

```python
# 3. 创建游戏时钟对象
clock = pygame.time.Clock()
i = 0

# 游戏循环
while True:

    # 设置屏幕刷新帧率,一秒钟运行60次
    clock.tick(60)

    print(i)
    i += 1
```

### 6. 在游戏循环中 监听 事件 

* `pygame` 中通过 `pygame.event.get()` 可以获得 **用户当前所做动作** 的 **事件列表**

```python
# 游戏循环
while True:

    # 设置屏幕刷新帧率
    clock.tick(60)

    # 事件监听,获取事件列表,比如一直按左方向键,都可以捕获到
    for event in pygame.event.get():

        # 判断用户是否点击了关闭按钮
        if event.type == pygame.QUIT:
            print("退出游戏...")

            pygame.quit()

            # 直接退出系统
            exit()
```

### 7.精灵和精灵组

* `pygame.sprite.Sprite` —— 存储 **图像数据 image** 和 **位置 rect** 的 **对象**
* `pygame.sprite.Group`—— 存放精灵的

![006_pygame.Sprite](img/15025046487919/006_pygame.Sprite.png)

* **精灵** 需要 有 **两个重要的属性**
    * `image` 要显示的图像
    * `rect` 图像要显示在屏幕的位置
* 默认的 `update()` 方法什么事情也没做
    * 子类可以重写此方法，在每次刷新屏幕时，更新精灵位置

* **注意**：`pygame.sprite.Sprite` 并没有提供 `image` 和 `rect` 两个属性
    * 需要程序员从 `pygame.sprite.Sprite` 派生子类
    * 并在 **子类** 的 **初始化方法** 中，设置 `image` 和 `rect` 属性

### 8.派生精灵子类

1. 新建 `plane_sprites.py` 文件
2. 定义 `GameSprite` 继承自 `pygame.sprite.Sprite`

**注意**

* 如果一个类的 **父类** 不是 `object`
* 在重写 **初始化方法** 时，**一定要** 先 `super()` 一下父类的 `__init__` 方法
* **保证父类中实现的 `__init__` 代码能够被正常执行**

![007_GameSprite-w398](img/15025046487919/007_GameSprite.png)

**属性**

* `image` 精灵图像，使用 `image_name` 加载
* `rect` 精灵大小，默认使用图像大小
* `speed` 精灵移动速度，默认为 `1`

**方法**

* `update` 每次更新屏幕时在游戏循环内调用
    * 让精灵的 `self.rect.y += self.speed`

**提示**

* `image` 的 `get_rect()` 方法，可以返回 **pygame.Rect(0, 0, 图像宽, 图像高)** 的对象

```python
import pygame


class GameSprite(pygame.sprite.Sprite):
    """游戏精灵基类"""
    
    def __init__(self, image_name, speed=1):
        
        # 调用父类的初始化方法
        super().__init__()
        
        # 加载图像
        self.image = pygame.image.load(image_name)
        # 设置尺寸
        self.rect = self.image.get_rect()
        # 记录速度
        self.speed = speed

    def update(self, *args):
        
        # 默认在垂直方向移动
        self.rect.y += self.speed
        
```

### 9.使用 游戏精灵 和 精灵组 创建敌机

**需求**

* 使用刚刚派生的 **游戏精灵** 和 **精灵组** 创建 敌机 并且实现敌机动画

**步骤**

1. 使用 `from` 导入 `plane_sprites` 模块 
    * `from` 导入的模块可以 **直接使用**
    * `import` 导入的模块需要通过 **模块名.** 来使用
2. 在 **游戏初始化** 创建 **精灵对象** 和 **精灵组对象**
3. 在 **游戏循环中** 让 **精灵组** 分别调用 `update()` 和 `draw(screen)` 方法

**职责**

* 精灵
    * 封装 **图像 image**、**位置 rect** 和 **速度 speed**
    * 提供 `update()` 方法，根据游戏需求，**更新位置 rect**
* 精灵组
    * 包含 **多个** **精灵对象**
    * `update` 方法，让精灵组中的所有精灵调用 `update` 方法更新位置
    * `draw(screen)` 方法，在 `screen` 上绘制精灵组中的所有精灵

#### 实现步骤

* 1) 导入 `plane_sprites` 模块

```python
from plane_sprites import *
```

* 2) 修改初始化部分代码

```python
# 创建敌机精灵和精灵组
enemy1 = GameSprite("./images/enemy1.png")
enemy2 = GameSprite("./images/enemy1.png", 2)
enemy2.rect.x = 200
enemy_group = pygame.sprite.Group(enemy1, enemy2)
```

* 3) 修改游戏循环部分代码

```python
# 让敌机组调用 update 和 draw 方法
enemy_group.update()
enemy_group.draw(screen)

# 更新屏幕显示
pygame.display.update()
```



## 4.游戏框架搭建

### 1.明确主程序职责

* 回顾 **快速入门案例**，一个游戏主程序的 **职责** 可以分为两个部分：
    * 游戏初始化
    * 游戏循环
* 根据明确的职责，设计 `PlaneGame` 类如下：

![009_游戏主程序-w600](img/15025159832322/009_%E6%B8%B8%E6%88%8F%E4%B8%BB%E7%A8%8B%E5%BA%8F.png)

* **游戏初始化** ——  `__init__()` 会调用以下方法： 

| 方法                     | 职责                 |
| ------------------------ | -------------------- |
| `__create_sprites(self)` | 创建所有精灵和精灵组 |

* **游戏循环** —— `start_game()` 会调用以下方法：

| 方法                     | 职责                                   |
| ------------------------ | -------------------------------------- |
| `__event_handler(self)`  | 事件监听                               |
| `__check_collide(self)`  | 碰撞检测 —— 子弹销毁敌机、敌机撞毁英雄 |
| `__update_sprites(self)` | 精灵组更新和绘制                       |
| `__game_over()`          | 游戏结束                               |



### 2. 实现飞机大战主游戏类

#### 1.明确文件职责

![011_程序文件职责-w479](img/15025159832322/011_%E7%A8%8B%E5%BA%8F%E6%96%87%E4%BB%B6%E8%81%8C%E8%B4%A3.png)

* `plane_main` 
    1. 封装 **主游戏类**
    2. 创建 **游戏对象**
    3. **启动游戏**
* `plane_sprites`
    * 封装游戏中 **所有** 需要使用的 **精灵子类**
    * 提供游戏的 **相关工具**

#### 2.代码实现

* 新建 `plane_main.py` 文件，并且设置为可执行
* 编写 **基础代码**

```python
import pygame
from plane_sprites import *


class PlaneGame(object):
    """飞机大战主游戏"""

    def __init__(self):
        print("游戏初始化")

    def start_game(self):
        print("开始游戏...")


if __name__ == '__main__':
    # 创建游戏对象
    game = PlaneGame()

    # 开始游戏
    game.start_game()

```

### 2.3 游戏初始化部分

* 完成 `__init__()` 代码如下：

```python
def __init__(self):
    print("游戏初始化")
    
    # 1. 创建游戏的窗口
    self.screen = pygame.display.set_mode((480, 700))
    # 2. 创建游戏的时钟
    self.clock = pygame.time.Clock()
    # 3. 调用私有方法，精灵和精灵组的创建
    self.__create_sprites()

def __create_sprites(self):
    pass
```

#### 使用 常量 代替固定的数值

> * 常量 —— 不变化的量
> * 变量 —— 可以变化的量

**应用场景**

* 在开发时，可能会需要使用 **固定的数值**，例如 **屏幕的高度** 是 `700`
* 这个时候，建议 **不要** 直接使用固定数值，而应该使用 **常量**
* 在开发时，为了保证代码的可维护性，尽量不要使用 **魔法数字** 

**常量的定义**

* 定义 **常量** 和 定义 **变量** 的语法完全一样，都是使用 **赋值语句**
* **常量** 的 **命名** 应该 **所有字母都使用大写**，**单词与单词之间使用下划线连接**

**常量的好处**

* 阅读代码时，通过 **常量名** **见名之意**，不需要猜测数字的含义
* 如果需要 **调整值**，只需要 **修改常量定义** 就可以实现 **统一修改**

> 提示：Python 中并没有真正意义的常量，只是通过命名的约定 —— 所有字母都是大写的就是常量，开发时不要轻易的修改！

**代码调整**

* 在 `plane_sprites.py` 中增加常量定义

```python
import pygame

# 游戏屏幕大小
SCREEN_RECT = pygame.Rect(0, 0, 480, 700)
```

* 修改 `plane_main.py` 中的窗口大小

```python
self.screen = pygame.display.set_mode(SCREEN_RECT.size)
```

### 2.4 游戏循环部分

* 完成 `start_game()` 基础代码如下：

```python
def start_game(self):
    """开始游戏"""
    
    print("开始游戏...")
       
    while True:
    
        # 1. 设置刷新帧率
        self.clock.tick(60)
        
        # 2. 事件监听
        self.__event_handler()
        
        # 3. 碰撞检测
        self.__check_collide()
        
        # 4. 更新精灵组
        self.__update_sprites()
        
        # 5. 更新屏幕显示
        pygame.display.update()

def __event_handler(self):
    """事件监听"""
    
    for event in pygame.event.get():
    
        if event.type == pygame.QUIT:
            PlaneGame.__game_over()

def __check_collide(self):
    """碰撞检测"""
    pass

def __update_sprites(self):
    """更新精灵组"""
    pass
    
@staticmethod
def __game_over():
   """游戏结束"""

   print("游戏结束")
   pygame.quit()
   exit()
```

### 03. 准备游戏精灵组

### 3.1 确定精灵组

![010_精灵组确定-w600](img/15025159832322/010_%E7%B2%BE%E7%81%B5%E7%BB%84%E7%A1%AE%E5%AE%9A.png)

### 3.2 代码实现

* 创建精灵组方法

```python
def __create_sprites(self):
    """创建精灵组"""
    
    # 背景组
    self.back_group = pygame.sprite.Group()
    # 敌机组
    self.enemy_group = pygame.sprite.Group()
    # 英雄组
    self.hero_group = pygame.sprite.Group()

```

* 更新精灵组方法

```python
def __update_sprites(self):
    """更新精灵组"""
    
    for group in [self.back_group, self.enemy_group, self.hero_group]:
    
        group.update()
        group.draw(self.screen)
```



### 4.游戏背景

### 01. 背景交替滚动的思路确定

运行 **备课代码**，**观察** 背景图像的显示效果：

* 游戏启动后，**背景图像** 会 **连续不断地** **向下方** 移动
* 在 **视觉上** 产生英雄的飞机不断向上方飞行的 **错觉** —— 在很多跑酷类游戏中常用的套路
    * **游戏的背景** 不断变化
    * **游戏的主角** 位置保持不变

### 1.1 实现思路分析

 ![013_背景图片交替滚动-w640](img/15025262948537/013_%E8%83%8C%E6%99%AF%E5%9B%BE%E7%89%87%E4%BA%A4%E6%9B%BF%E6%BB%9A%E5%8A%A8.png)

**解决办法**

1. 创建两张背景图像精灵
    * 第 `1` 张 **完全和屏幕重合**
    * 第 `2` 张在 **屏幕的正上方**
2. 两张图像 **一起向下方运动**
    * `self.rect.y += self.speed`
3. 当 **任意背景精灵** 的 `rect.y >= 屏幕的高度` 说明已经 **移动到屏幕下方**
4. 将 **移动到屏幕下方的这张图像** 设置到 **屏幕的正上方**
    * `rect.y = -rect.height`  

### 1.2 设计背景类

![012_派生Background子类-w398](img/15025262948537/012_%E6%B4%BE%E7%94%9FBackground%E5%AD%90%E7%B1%BB.png)

* **初始化方法**
    * 直接指定 **背景图片**
    * `is_alt` 判断是否是另一张图像
        * `False` 表示 **第一张图像**，需要与屏幕重合
        * `True` 表示 **另一张图像**，在屏幕的正上方
* **update()** 方法
    * 判断 **是否移动出屏幕**，如果是，将图像设置到 **屏幕的正上方**，从而实现 **交替滚动**

> **继承** 如果父类提供的方法，不能满足子类的需求：
>
> * 派生一个子类
> * 在子类中针对特有的需求，重写父类方法，并且进行扩展

## 02. 显示游戏背景

### 2.1 背景精灵的基本实现

* 在 `plane_sprites` 新建 `Background` 继承自 `GameSprite`

```python
class Background(GameSprite):
    """游戏背景精灵"""

    def update(self):

        # 1. 调用父类的方法实现
        super().update()

        # 2. 判断是否移出屏幕，如果移出屏幕，将图像设置到屏幕的上方
        if self.rect.y >= SCREEN_RECT.height:
            self.rect.y = -self.rect.height

```

### 2.2 在 `plane_main.py` 中显示背景精灵

1. 在 `__create_sprites` 方法中创建 **精灵** 和 **精灵组**
2. 在 `__update_sprites` 方法中，让 **精灵组** 调用 `update()` 和 `draw()` 方法

> `__create_sprites` 方法

```python
def __create_sprites(self):

    # 创建背景精灵和精灵组
    bg1 = Background("./images/background.png")
    bg2 = Background("./images/background.png")
    bg2.rect.y = -bg2.rect.height
    
    self.back_group = pygame.sprite.Group(bg1, bg2)
```

> `__update_sprites` 方法

```python
def __update_sprites(self):

    self.back_group.update()
    self.back_group.draw(self.screen)
```

### 2.3 利用初始化方法，简化背景精灵创建

> 思考 —— 上一小结完成的代码存在什么样的问题？能否简化？

* 在主程序中，创建的**两个背景精灵**，**传入了相同的图像文件路径**
* 创建 **第二个 背景精灵** 时，**在主程序中**，设置背景精灵的图像位置

> 思考 —— 精灵 **初始位置** 的设置，应该 **由主程序负责**？还是 **由精灵自己负责**？

**答案** —— **由精灵自己负责**

* 根据面向对象设计原则，应该将对象的职责，封装到类的代码内部
* 尽量简化程序调用一方的代码调用

![012_派生Background子类-w398](img/15025262948537/012_%E6%B4%BE%E7%94%9FBackground%E5%AD%90%E7%B1%BB.png)

* **初始化方法**
    * 直接指定 **背景图片**
    * `is_alt` 判断是否是另一张图像
        * `False` 表示 **第一张图像**，需要与屏幕重合
        * `True` 表示 **另一张图像**，在屏幕的正上方

在 `plane_sprites.py` 中实现 `Background` 的 **初始化方法**

```python
def __init__(self, is_alt=False):

    image_name = "./images/background.png"
    super().__init__(image_name)
       
    # 判断是否交替图片，如果是，将图片设置到屏幕顶部
    if is_alt:
        self.rect.y = -self.rect.height     
```

* 修改 `plane_main` 的 `__create_sprites` 方法

```python
# 创建背景精灵和精灵组
bg1 = Background()
bg2 = Background(True)

self.back_group = pygame.sprite.Group(bg1, bg2)
```



## 5.敌机出场

## 目标

* 使用 **定时器** 添加敌机
* 设计 `Enemy` 类

## 01. 使用定时器添加敌机

运行 **备课代码**，**观察** 敌机的 **出现规律**：

1. 游戏启动后，**每隔 1 秒** 会 **出现一架敌机**
2. 每架敌机 **向屏幕下方飞行**，飞行 **速度各不相同**
3. 每架敌机出现的 **水平位置** 也不尽相同
4. 当敌机 **从屏幕下方飞出**，不会再飞回到屏幕中

### 1.1 定时器

* 在 `pygame` 中可以使用 `pygame.time.set_timer()` 来添加 **定时器**
* 所谓 **定时器**，就是 **每隔一段时间**，去 **执行一些动作**

```python
set_timer(eventid, milliseconds) -> None
```

* `set_timer` 可以创建一个 **事件**
* 可以在 **游戏循环** 的 **事件监听** 方法中捕获到该事件
* 第 1 个参数 **事件代号** 需要基于常量 `pygame.USEREVENT` 来指定
    * `USEREVENT` 是一个整数，再增加的事件可以使用 `USEREVENT + 1` 指定，依次类推...
* 第 2 个参数是 **事件触发** 间隔的 **毫秒值**

**定时器事件的监听**

* 通过 `pygame.event.get()` 可以获取当前时刻所有的 **事件列表**
* **遍历列表** 并且判断 `event.type` 是否等于 `eventid`，如果相等，表示 **定时器事件** 发生

### 1.2 定义并监听创建敌机的定时器事件

`pygame` 的 **定时器** 使用套路非常固定：

1. 定义 **定时器常量** —— `eventid`
2. 在 **初始化方法** 中，调用 `set_timer` 方法 **设置定时器事件**
3. 在 **游戏循环** 中，**监听定时器事件**

#### 1) 定义事件

* 在 `plane_sprites.py` 的顶部定义 **事件常量**

```python
# 敌机的定时器事件常量
CREATE_ENEMY_EVENT = pygame.USEREVENT
```

* 在 `PlaneGame` 的 **初始化方法** 中 **创建用户事件**

```python
# 4. 设置定时器事件 - 每秒创建一架敌机
pygame.time.set_timer(CREATE_ENEMY_EVENT, 1000)
```

#### 2) 监听定时器事件

* 在 `__event_handler` 方法中增加以下代码：

```python
def __event_handler(self):
    
    for event in pygame.event.get():
    
        # 判断是否退出游戏
        if event.type == pygame.QUIT:
            PlaneGame.__game_over()
        elif event.type == CREATE_ENEMY_EVENT:
            print("敌机出场...")
```

## 02. 设计 `Enemy` 类

1. 游戏启动后，**每隔 1 秒** 会 **出现一架敌机**
2. 每架敌机 **向屏幕下方飞行**，飞行 **速度各不相同**
3. 每架敌机出现的 **水平位置** 也不尽相同
4. 当敌机 **从屏幕下方飞出**，不会再飞回到屏幕中

![014_派生Enemy子类-w657](img/15025309517247/014_%E6%B4%BE%E7%94%9FEnemy%E5%AD%90%E7%B1%BB.png)

* **初始化方法**
    * 指定 **敌机图片**
    * **随机** 敌机的 **初始位置** 和 **初始速度**
* 重写 **update()** 方法
    * 判断 **是否飞出屏幕**，如果是，从 **精灵组** 删除

### 2.1 敌机类的准备

* 在 `plane_sprites` 新建 `Enemy` 继承自 `GameSprite`
* 重写 **初始化方法**，直接指定 **图片名称**
* 暂时 **不实现** **随机速度** 和 **随机位置** 的指定
* 重写 `update` 方法，判断是否飞出屏幕

```python
class Enemy(GameSprite):
    """敌机精灵"""
    
    def __init__(self):
        
        # 1. 调用父类方法，创建敌机精灵，并且指定敌机的图像
        super().__init__("./images/enemy1.png")

        # 2. 设置敌机的随机初始速度

        # 3. 设置敌机的随机初始位置
    
    def update(self):
        
        # 1. 调用父类方法，让敌机在垂直方向运动
        super().update()
        
        # 2. 判断是否飞出屏幕，如果是，需要将敌机从精灵组删除
        if self.rect.y >= SCREEN_RECT.height:
            print("敌机飞出屏幕...")    
```

### 2.2 创建敌机

**演练步骤**

1. 在 `__create_sprites`，添加 **敌机精灵组**
    * 敌机是 **定时被创建的**，因此在初始化方法中，不需要创建敌机
2. 在 `__event_handler`，创建敌机，并且 **添加到精灵组**
    * 调用 **精灵组** 的 `add` 方法可以 **向精灵组添加精灵**
3. 在 `__update_sprites`，让 **敌机精灵组** 调用 `update` 和 `draw` 方法

![006_pygame.SpriteII](img/15025309517247/006_pygame.SpriteII.png)

**演练代码**

* 修改 `plane_main` 的 `__create_sprites` 方法

```python
# 敌机组
self.enemy_group = pygame.sprite.Group()
```

* 修改 `plane_main` 的 `__update_sprites` 方法

```python
self.enemy_group.update()
self.enemy_group.draw(self.screen)
```

* 定时出现敌机

```python
elif event.type == CREATE_ENEMY_EVENT:
    self.enemy_group.add(Enemy())
```

### 2.3 随机敌机位置和速度

#### 1) 导入模块

* 在导入模块时，**建议** 按照以下顺序导入

```python
1. 官方标准模块导入
2. 第三方模块导入
3. 应用程序模块导入
```

* 修改 `plane_sprites.py` 增加 `random` 的导入

```python
import random
```

#### 2) 随机位置

![015_飞机初始位置-w360](img/15025309517247/015_%E9%A3%9E%E6%9C%BA%E5%88%9D%E5%A7%8B%E4%BD%8D%E7%BD%AE.png)

使用 `pygame.Rect` 提供的 `bottom` 属性，在指定敌机初始位置时，会比较方便

*  `bottom = y + height`
*  `y = bottom - height`

#### 3) 代码实现

* 修改 **初始化方法**，随机敌机出现 **速度** 和 **位置**

```python
def __init__(self):

    # 1. 调用父类方法，创建敌机精灵，并且指定敌机的图像
    super().__init__("./images/enemy1.png")

    # 2. 设置敌机的随机初始速度 1 ~ 3
    self.speed = random.randint(1, 3)

    # 3. 设置敌机的随机初始位置
    self.rect.bottom = 0
    
    max_x = SCREEN_RECT.width - self.rect.width
    self.rect.x = random.randint(0, max_x)
```

### 2.4 移出屏幕销毁敌机

* 敌机移出屏幕之后，如果 **没有撞到英雄**，敌机的历史使命已经终结
* 需要从 **敌机组** 删除，否则会造成 **内存浪费**

#### 检测敌机被销毁

* `__del__` 内置方法会在对象被销毁前调用，在开发中，可以用于 **判断对象是否被销毁**

```python
def __del__(self):
    print("敌机挂了 %s" % self.rect)
```

#### 代码实现

![006_pygame.SpriteII](img/15025309517247/006_pygame.SpriteII.png)

* 判断敌机是否飞出屏幕，如果是，调用 `kill()` 方法从所有组中删除

```python
def update(self):
    super().update()
    
    # 判断敌机是否移出屏幕
    if self.rect.y >= SCREEN_RECT.height:
        # 将精灵从所有组中删除
        self.kill()
```



## 6.英雄登场

## 目标

* 设计 **英雄** 和 **子弹** 类
* 使用 `pygame.key.get_pressed()` 移动英雄
* 发射子弹

## 01. 设计 **英雄** 和 **子弹** 类

### 英雄需求

1. 游戏启动后，**英雄** 出现在屏幕的 **水平中间** 位置，距离 **屏幕底部** `120` **像素**
2. **英雄** 每隔 `0.5` 秒发射一次子弹，每次 **连发三枚子弹**
3. **英雄** 默认不会移动，需要通过 **左/右** 方向键，控制 **英雄** 在水平方向移动

![017_英雄位置-w480](img/15025349250200/017_%E8%8B%B1%E9%9B%84%E4%BD%8D%E7%BD%AE.png)

### 子弹需求

1. **子弹** 从 **英雄** 的正上方发射 **沿直线** 向 **上方** 飞行
2. **飞出屏幕后**，需要从 **精灵组** 中删除

![016_派生英雄和子弹子类](img/15025349250200/016_%E6%B4%BE%E7%94%9F%E8%8B%B1%E9%9B%84%E5%92%8C%E5%AD%90%E5%BC%B9%E5%AD%90%E7%B1%BB.png)

### Hero —— 英雄

* **初始化方法**
    * 指定 **英雄图片**
    * **初始速度 = 0** —— 英雄默认静止不动
    * 定义 `bullets` **子弹精灵组** 保存子弹精灵
* 重写 **update()** 方法
    * 英雄需要 **水平移动**
    * 并且需要保证不能 **移出屏幕**
* 增加 `bullets` 属性，记录所有 **子弹精灵**
* 增加 `fire` 方法，用于发射子弹

### Bullet —— 子弹

* **初始化方法**
    * 指定 **子弹图片**
    * **初始速度 = -2** —— 子弹需要向上方飞行
* 重写 **update()** 方法
    * 判断 **是否飞出屏幕**，如果是，从 **精灵组** 删除

## 02. 创建英雄

### 2.1 准备英雄类

* 在 `plane_sprites` 新建 `Hero` 类
* 重写 **初始化方法**，直接指定 **图片名称**，并且将初始速度设置为 `0`
* 设置 **英雄的初始位置**

![003_pygame.Rect-w382](img/15025349250200/003_pygame.Rect.png)

* `centerx = x + 0.5 * width`
* `centery = y + 0.5 * height`
* `bottom = y + height`

![017_英雄位置-w480](img/15025349250200/017_%E8%8B%B1%E9%9B%84%E4%BD%8D%E7%BD%AE.png)

```python
class Hero(GameSprite):
    """英雄精灵"""

    def __init__(self):

        super().__init__("./images/me1.png", 0)

        # 设置初始位置
        self.rect.centerx = SCREEN_RECT.centerx
        self.rect.bottom = SCREEN_RECT.bottom - 120
```

### 2.2 绘制英雄

1. 在 `__create_sprites`，添加 **英雄精灵** 和 **英雄精灵组**
    * 后续要针对 **英雄** 做 **碰撞检测** 以及 **发射子弹**
    * 所以 **英雄** 需要 **单独定义成属性**
2. 在 `__update_sprites`，让 **英雄精灵组** 调用 `update` 和 `draw` 方法

#### 代码实现

* 修改 `__create_sprites` 方法如下：

```python
# 英雄组
self.hero = Hero()
self.hero_group = pygame.sprite.Group(self.hero)
```

* 修改 `__update_sprites` 方法如下：

```python
self.hero_group.update()
self.hero_group.draw(self.screen)
```

## 03. 移动英雄位置

> 在 `pygame` 中针对 **键盘按键的捕获**，有 **两种** 方式

* **第一种方式** 判断 `event.type == pygame.KEYDOWN`
* **第二种方式** 
    1. 首先使用 `pygame.key.get_pressed()` 返回 **所有按键元组**
    2. 通过 **键盘常量**，判断元组中 **某一个键是否被按下** —— 如果被按下，对应数值为 `1`

**提问** 这两种方式之间有什么区别呢？

* 第一种方式

```python
elif event.type == pygame.KEYDOWN and event.key == pygame.K_RIGHT:
    print("向右移动...")
```

* 第二种方式

```python
# 返回所有按键的元组，如果某个键被按下，对应的值会是1
keys_pressed = pygame.key.get_pressed()
# 判断是否按下了方向键
if keys_pressed[pygame.K_RIGHT]:
    print("向右移动...")
```

**结论**

* **第一种方式** `event.type` 用户 **必须要抬起按键** 才算一次 **按键事件**，**操作灵活性会大打折扣**
* **第二种方式** 用户可以按住方向键不放，就能够实现持续向某一个方向移动了，**操作灵活性更好**

### 3.1 移动英雄位置

**演练步骤**

1. 在 `Hero` 类中重写 `update` 方法
    * 用 **速度** `speed` 和 **英雄** `rect.x` 进行叠加
    * **不需要调用父类方法** —— 父类方法只是实现了单纯的垂直运动
2. 在 `__event_handler` 方法中根据 **左右方向键** 设置英雄的 **速度**
    * **向右** => `speed = 2`
    * **向左** => `speed = -2`
    * **其他** => `speed = 0`

**代码演练**

* 在 `Hero` 类，重写 `update()` 方法，**根据速度水平移动** 英雄的飞机

```python
def update(self):

    # 飞机水平移动
    self.rect.x += self.speed
```

* 调整键盘按键代码

```python
# 获取用户按键
keys_pressed = pygame.key.get_pressed()

if keys_pressed[pygame.K_RIGHT]:
    self.hero.speed = 2
elif keys_pressed[pygame.K_LEFT]:
    self.hero.speed = -2
else:
    self.hero.speed = 0
```

### 3.2 控制英雄运动边界

* 在 `Hero` 类的 `update()` 方法判断 **英雄** 是否超出 **屏幕边界**

![003_pygame.Rect-w382](img/15025349250200/003_pygame.Rect.png)

* `right = x + width` 利用 `right` 属性可以非常容易的针对右侧设置精灵位置

![017_英雄位置II-w408](img/15025349250200/017_%E8%8B%B1%E9%9B%84%E4%BD%8D%E7%BD%AEII.png)

```python
def update(self):

    # 飞机水平移动
    self.rect.x += self.speed
    
    # 判断屏幕边界
    if self.rect.left < 0:
        self.rect.left = 0
    if self.rect.right > SCREEN_RECT.right:
        self.rect.right = SCREEN_RECT.right
```

## 04. 发射子弹

### 需求回顾 —— 英雄需求

1. 游戏启动后，**英雄** 出现在屏幕的 **水平中间** 位置，距离 **屏幕底部** `120` **像素**
2. **英雄** 每隔 `0.5` 秒发射一次子弹，每次 **连发三枚子弹**
3. **英雄** 默认不会移动，需要通过 **左/右** 方向键，控制 **英雄** 在水平方向移动

### 4.1 添加发射子弹事件

`pygame` 的 **定时器** 使用套路非常固定：

1. 定义 **定时器常量** —— `eventid`
2. 在 **初始化方法** 中，调用 `set_timer` 方法 **设置定时器事件**
3. 在 **游戏循环** 中，**监听定时器事件**

**代码实现**

* 在 `Hero` 中定义 `fire` 方法

```python
def fire(self):
    print("发射子弹...")
```

* 在 `plane_main.py` 的顶部定义 **发射子弹** 事件常量

```python
# 英雄发射子弹事件
HERO_FIRE_EVENT = pygame.USEREVENT + 1
```

* 在 `__init__` 方法末尾中添加 **发射子弹** 事件

```python
# 每隔 0.5 秒发射一次子弹
pygame.time.set_timer(HERO_FIRE_EVENT, 500)
```

* 在 `__event_handler` 方法中让英雄发射子弹

```python
elif event.type == HERO_FIRE_EVENT:
    self.hero.fire()
```

### 4.2 定义子弹类

#### 需求回顾 —— 子弹需求

1. **子弹** 从 **英雄** 的正上方发射 **沿直线** 向 **上方** 飞行
2. **飞出屏幕后**，需要从 **精灵组** 中删除

#### Bullet —— 子弹

* **初始化方法**
    * 指定 **子弹图片**
    * **初始速度 = -2** —— 子弹需要向上方飞行
* 重写 **update()** 方法
    * 判断 **是否飞出屏幕**，如果是，从 **精灵组** 删除

#### 定义子弹类

* 在 `plane_sprites` 新建 `Bullet` 继承自 `GameSprite`
* 重写 **初始化方法**，直接指定 **图片名称**，并且设置 **初始速度**
* 重写 `update()` 方法，判断子弹 **飞出屏幕从精灵组删除**

```python
class Bullet(GameSprite):
    """子弹精灵"""
    
    def __init__(self):
        
        super().__init__("./images/bullet1.png", -2)
 
    def update(self):
    
        super().update()
        
        # 判断是否超出屏幕，如果是，从精灵组删除
        if self.rect.bottom < 0:
            self.kill()
```

### 4.3 发射子弹

**演练步骤**

1. 在 `Hero` 的 **初始化方法** 中创建 **子弹精灵组** 属性
2. 修改 `plane_main.py` 的 `__update_sprites` 方法，让 **子弹精灵组** 调用 `update` 和 `draw` 方法
3. 实现 `fire()` 方法
    * 创建子弹精灵
    * 设置初始位置 —— 在 **英雄的正上方**
    * 将 **子弹** 添加到精灵组

**代码实现**

* 初始化方法

```python
# 创建子弹的精灵组
self.bullets = pygame.sprite.Group()
```

* 修改 `fire()` 方法

```python
def fire(self):

    # 1. 创建子弹精灵
    bullet = Bullet()
    
    # 2. 设置精灵的位置
    bullet.rect.bottom = self.rect.y - 20
    bullet.rect.centerx = self.rect.centerx
    
    # 3. 将精灵添加到精灵组
    self.bullets.add(bullet)
```

#### 一次发射三枚子弹

![017_英雄位置III-w559](img/15025349250200/017_%E8%8B%B1%E9%9B%84%E4%BD%8D%E7%BD%AEIII.png)

* 修改 `fire()` 方法，一次发射三枚子弹

```python
def fire(self):

    for i in (1, 2, 3):
        # 1. 创建子弹精灵
        bullet = Bullet()
        
        # 2. 设置精灵的位置
        bullet.rect.bottom = self.rect.y - i * 20
        bullet.rect.centerx = self.rect.centerx
        
        # 3. 将精灵添加到精灵组
        self.bullets.add(bullet)
```

## 7.碰撞检测

## 目标

* 了解碰撞检测方法
* 碰撞实现

## 01. 了解碰撞检测方法

* `pygame` 提供了 **两个非常方便** 的方法可以实现碰撞检测：

### pygame.sprite.groupcollide()

* **两个精灵组** 中 **所有的精灵** 的碰撞检测

```python
groupcollide(group1, group2, dokill1, dokill2, collided = None) -> Sprite_dict
```

* 如果将 `dokill` 设置为 `True`，则 **发生碰撞的精灵将被自动移除**
* `collided` 参数是用于 **计算碰撞的回调函数**
    * 如果没有指定，则每个精灵必须有一个 `rect` 属性

### pygame.sprite.spritecollide()

* 判断 **某个精灵** 和 **指定精灵组** 中的精灵的碰撞

```python
spritecollide(sprite, group, dokill, collided = None) -> Sprite_list
```

* 如果将 `dokill` 设置为 `True`，则 **指定精灵组** 中 **发生碰撞的精灵将被自动移除**
* `collided` 参数是用于 **计算碰撞的回调函数**
    * 如果没有指定，则每个精灵必须有一个 `rect` 属性
* 返回 **精灵组** 中跟 **精灵** 发生碰撞的 **精灵列表**

## 02. 碰撞实现

```python
def __check_collide(self):

    # 1. 子弹摧毁敌机
    pygame.sprite.groupcollide(self.hero.bullets, self.enemy_group, True, True)
    
    # 2. 敌机撞毁英雄
    enemies = pygame.sprite.spritecollide(self.hero, self.enemy_group, True)
    
    # 判断列表时候有内容
    if len(enemies) > 0:
    
        # 让英雄牺牲
        self.hero.kill()
        
        # 结束游戏
        PlaneGame.__game_over()
```



# 4.网络编程

## 1.udp

发送端

```python
import socket

def main():
    # 创建一个udp套接字
    udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

    while True:

        send_data = str = input("输入要发送的数据")

        if send_data == "exit":
            break

        # udp_socket.sendto(b"这是消息",("192.169.0.1",8000))
        udp_socket.sendto(send_data.encode("utf-8"),("127.0.0.1",7788))

    # 关闭套接字
    udp_socket.close()

if __name__ == '__main__':
    main()
```

接受者

```python
import socket

def main():
    # 创建一个udp套接字
    udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

    # 绑定本地相关信息
    local_addr = ("",7788)
    udp_socket.bind(local_addr)

    while True:
        # 等待接收对方发送的数据
        recv_data = udp_socket.recvfrom(1024)
        print(recv_data[0].decode("gbk"))

    # 关闭套接字
    udp_socket.close()

if __name__ == '__main__':
    main()
```



## 2.tcp



# 5.Mysql

安装包

```python
pip3 install pymysql
```

```python
from pymysql import connect

con = connect(host="43.240.30.52",
        port=3306,
        user="yanganli_github",
        password="yanganli_github",
        database="yanganli_github",
        charset="utf8")

cursor = con.cursor()

# 查询出条数
print(cursor.execute("select * from post"))

for one in cursor.fetchall():
    print(one)

cursor.close()
con.close()
```

# 6.Request库



# 7.Django

生成数据库 迁移文件

```python
python manage.py makemigrations
```

执行迁移生成表

```python
python manage.py migrate
```










