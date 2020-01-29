# 1.基础

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





