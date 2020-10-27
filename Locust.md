# 写一个Locust文件

Locust文件是一个普通的python文件,唯一的要求就是至少有一个类继承的`User`类

## User

一个User类标识一个用户,Locust会给每个用户模拟一个User实例,公共的属性通常在User类里被定义

### wait_time 属性

用户的wwait_time方法通常作用于执行的任务简直等待多长时间

这里有三个内建的等待时间的函数

* constant 一段固定的时间
* between 最小和最大数之间的随机时间
* constant_pacing 确保任务运行一次最多不超过x秒的适应时间

举个例子,让每个用户在每个任务执行之间等待0.5s到10s

```python
from locust import User, task, between

class MyUser(User):
    @task
    def my_task(self):
        print("executing my_task")

    wait_time = between(0.5, 10)
```

也可以直接在你的类里声明wait_time方法,举个例子,下面的User类,会睡眠1s,2s...

```python
class MyUser(User):
    last_wait_time = 0

    def wait_time(self):
        self.last_wait_time += 1
        return self.last_wait_time

    ...
```

### weight 属性

如果文件中存在多个用户类，并且在命令行中没有指定用户类，则Locust将生成每个用户类的相同数量。您还可以通过将用户类作为命令行参数传递来指定从同一个locustfile中使用哪些用户类

```shell
locust -f locust_file.py WebUser MobileUser
```

如果希望模拟某种类型的更多用户，可以在这些类上设置权重属性,比方说,web用户是移动用户的三倍多

```python
class WebUser(User):
    weight = 3
    ...

class MobileUser(User):
    weight = 1
    ...
```

### host 属性

host属性是host的URL前缀,通常,当locust启动的时候,在locust的web界面或者在命令上中使用`--hosts`选项中使用

如果一个类声明了声明的host属性,它将在没有使用`--host`命令行或web请求仲使用

### tasks属性

用户类可以使用`@task`装饰器,将任务声明为方法,但是也可以使用下面详细描述的tasks属性来指定任务。

### environment 属性

对用户正在其中运行的环境的引用,使用它影响这环境,或者是运行着在它的容器中,比如去停止运行从任务方法中

```python
self.environment.runner.quit()
```

如果运行的是独立的locust实例,它将停止全部,如果是运行在工作节点,它将停止节点

### on_start 和 on_stop 方法

用户 或者是任务集合可以声明``on_start`方法或者`on_stop`方法,用户将调用它自己的`on_start`方法在它将要开始运行的时候,`on_stop`方法,将在停止运行的时候调用,比如TaskSet,`on_start`方法被调用在模拟的用户开始执行任务,`on_stop`方法在停止模拟的用户执行任务的时候调用(或者被`interrupt()`方法调用,或者是用被用杀掉)

## Tasks

当启动负载测试,一个用户实例将被创建,