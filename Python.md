# 基础

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
| **%d** | 格式化整数 |
| %x | 格式化十六进制数（小写） |
| %X | 格式化十六进制数（大写） |
| %o | 格式化八进制数 |
| **%f** | 格式化浮点数字，可以指定小数点精度 |
| **%%** |输出%号|

