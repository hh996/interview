## 1 Python的函数参数传递
在python中，strings, tuples, 和numbers是不可更改的对象，而 list, dict, set 等则是可以修改的对象
```python
a = 1
print("func_in", id(a))   # func_in 41322472
a = 2
print("re-point",id(a), id(2))  # re-point 41322448 41322448
```
引用传递给函数的时候,函数自动复制一份引用,这个函数里的引用和外边的引用没有半毛关系了，修改后会指向一个新的对象

## 2 Python的元类
类也是对象(函数也是对象)
```python
class ObjectCreator(object):
    pass
my_object = ObjectCreator()
print(my_object)    # <__main__.ObjectCreator object at 0x8974f2c>
```
元类是创建这些对象的东西,可以将其称为“类工厂”。它们是类的类,type是一个元类，用于在幕后创建所有的类
```python
MyClass = type('MyClass', (), {})
```

## 3 实例方法，静态方法(@staticmethod),类方法(@classmethod)
```python
def foo(x):
    print("executing foo(%s)"%x)

class A(object):
    # 实例方法
    def foo(self,x):
        print("executing foo(%s,%s)"%(self,x))

    @classmethod
    def class_foo(cls,x):
        print("executing class_foo(%s,%s)"%(cls,x))

    @staticmethod
    def static_foo(x):
        print("executing static_foo(%s)"%x)
a=A()
```
实例方法调用的时候是a.foo(x)(其实是foo(a, x)，需要把实例自己传给函数)
类方法传递的是类而不是实例,A.class_foo(x)
静态方法其实和普通的方法一样，可以a.static_foo(x)或者A.static_foo(x)来调用

## 4 类变量和实例变量
类变量:所有实例共享同一个类变量。当一个实例修改了类变量的值，其他实例也会受到影响。  
实例变量:self.xxx, 每个实例都有独立的实例变量，修改一个实例的实例变量不会影响其他实例
```python
class Test(object):  
    # 类变量
    num_of_instance = 0  
    def __init__(self, name): 
        # 实例变量    
        self.name = name  
        Test.num_of_instance += 1
```

## 5 Python自省
面向对象的语言所写的程序在运行时,所能知道对象的类型
```python
a = [1,2,3]
b = {'a':1,'b':2,'c':3}
c = True
print(type(a),type(b),type(c)) # <type 'list'> <type 'dict'> <type 'bool'>
print(isinstance(a,list))  # True
```

## 6 迭代器和生成器
迭代:一项一项地读取
```python
mylist = [1, 2, 3]
for i in mylist:
    print(i)  # 1 2 3
```
生成器是迭代器，是只可以迭代一次的生成器
```python
generator = (x*x for x in range(3))
for i in generator:
    print(i)  # 0 1 4
```

## 7 *args and **kwargs
*args: 可以传任意数量的参数, 将函数中的多个参数打包成一个元组（tuple）
**kwargs: 用于接收不定数量的关键字参数（Keyword Arguments）,将函数中的多个关键字参数打包成一个字典（dictionary）
```python
def example_function(*args, **kwargs):
    for arg in args:
        print(arg)
    for key, value in kwargs.items():
        print(f"{key}: {value}")

example_function(1, 2, 3, name='Alice', age=30)
# 输出:
# 1
# 2
# 3
# name: Alice
# age: 30

```

## 8 面向切面编程AOP和装饰器
装饰器:允许在装饰的函数之前和之后执行代码
```python
def use_logging(func):

    def wrapper():
        logging.warn("%s is running" % func.__name__)
        return func()   # 把 foo 当做参数传递进来时，执行func()就相当于执行foo()
    return wrapper

def foo():
    print('i am foo')

foo = use_logging(foo)  # foo 实际上 = wrapper()
foo()  # 实际上是 wrapper()

# 或者
@use_logging  # 有了@, 就可以省去 foo = use_logging(foo)
def foo():
    print("i am foo")
foo()
```
在这个例子中，函数进入和退出时 ，
被称为一个横切面，这种编程方式被称为面向切面(AOP)的编程
AOP(面向切面编程，Aspect-Oriented Programming): 将横切关注点（Cross-Cutting Concerns）从主要业务逻辑中分离出来, 例如:日志记录

## 9 鸭子类型
不关心对象是什么类型, 只关心行为
```python
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class Duck:
    def speak(self):
        return "Quack!"

def animal_sound(animal):
    return animal.speak()

# 使用鸭子类型
dog = Dog()
cat = Cat()
duck = Duck()

print(animal_sound(dog))  # 输出 "Woof!"
print(animal_sound(cat))  # 输出 "Meow!"
print(animal_sound(duck))  # 输出 "Quack!"
```

## 10 重载和重写
重载:在同一个类中，可以定义多个同名方法，但它们的参数类型或个数不同

重写:在子类中重新定义父类中已经存在的方法,满足子类特定的需求

为什么Python不支持重载:https://www.zhihu.com/question/20053359

## 11 单例模式
确保一个类只有一个实例，并提供一个全局访问点
### 1 import
```python
class Singleton:
    pass
singleton_instance = Singleton()

from Singleton import singleton_instance
```
### 2 __new__
```python
class A:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super(A,cls).__new__(cls)
        return cls._instance
a1 = A()
a2 = A()
print(a1 is a2)
# True
```

## 12 GIL线程全局锁
线程全局锁(Global Interpreter Lock), Python为了保证线程安全**一个核只能在同一时间运行一个线程**。   
GIL对于CPU密集型任务的性能影响较大，而对于I/O密集型任务的性能影响较小。在处理I/O时，线程可以释放GIL，允许其他线程执行。  
考虑使用多进程而非多线程，使用其他Python解释器，如Jython（Java平台）

## 13 协程(Coroutine)
协程:程序主动控制切换函数执行，没有线程切换的开销，所以执行效率极高对于IO密集型任务非常适用(整个过程看似像多线程，然而协程只有一个线程执行)。
```python
import asyncio
async def hello():
    print("hello")
    await asyncio.sleep(5)
    print("world")
coroutine = asyncio.get_event_loop()
coroutine.run_until_complete((hello()))
```

## 14 yield
用于定义生成器函数, 使其逐个产生值
```python
def my_generator():
    yield 1
    yield 2
    yield 3

# 使用生成器函数
gen = my_generator()

# 通过迭代器获取生成器的值
for value in gen:
    print(value)
```

## 15 闭包
闭包(Closure)是一种函数对象(函数是一等公民，这意味着函数可以作为变量、参数传递给其他函数，也可以在函数内定义函数,闭包就是由这种函数嵌套的特性衍生而来), 闭包必须满足以下几点:
1. 必须有一个内嵌函数
2. 内嵌函数必须引用外部函数中的变量
3. 外部函数的返回值必须是内嵌函数  
和装饰器里AOP的思想有点像
闭包是由另一个函数返回的函数对象。我们使用它们来消除代码冗余。

## 16 lambda函数
lambda 函数是一种匿名函数，也称为 lambda 表达式
用法 lambda arguments: expression
```python
add = lambda x, y: x + y
print(add(3, 5))  # 输出 8

numbers = [1, 2, 3, 4, 5]
squared_numbers = list(map(lambda x: x**2, numbers))  # map 将一个函数应用于可迭代对象（如列表）的每个元素, 返回一个迭代器
print(squared_numbers)  # 输出 [1, 4, 9, 16, 25]

```

## 17 Python里的copy
b = a: 赋值引用，其实就是对象的引用（别名）

b = a.copy(): 拷贝父对象，不会拷贝对象的内部的子对象

b = copy.deepcopy(a): 深度拷贝, a 和 b 完全拷贝了父对象及其子对象，两者是完全独立的。
```python
import copy

a = [1, [1, 2, 3]]
b = copy.copy(a)
c = copy.deepcopy(a)
a[1][0] = 2
c[1][0] = 3
print(a)  # [1, [2, 2, 3]]
print(b)  # [1, [2, 2, 3]]
print(c)  # [1, [3, 2, 3]]
```

## 18 Python的is
is是对比地址,==是对比值

## 19 Python 多线程多进程
```python
import threading
import multiprocessing
import time


def print_nums():
    for i in range(5):
        time.sleep(1)
        print(i)


def print_strs():
    for s in "abcde":
        time.sleep(1)
        print(s)


# 线程
thread1 = threading.Thread(target=print_nums)
thread2 = threading.Thread(target=print_strs)
thread1.start()
thread2.start()


thread1.join()  # 程序将等待线程执行完成，然后继续执行接下来的代码;目的是保证主线程在子线程执行完成后再继续执行，避免出现线程还在执行时主线程已经结束的情况
thread2.join()
print("Both threads have finished.")

if __name__ == '__main__':
    process1 = multiprocessing.Process(target=print_nums)
    process2 = multiprocessing.Process(target=print_strs)

    multiprocessing.freeze_support()  # 添加这一行和 if __name__..., 因为windows启动多进程有点问题

    process1.start()
    process2.start()

    process1.join()
    process2.join()

    print("Both processes have finished.")

```

## 20 垃圾回收
引用计数  
循环垃圾回收（Cycle Collector）:为了解决循环引用问题，Python 使用了循环垃圾回收器。这个回收器会查找并清理那些被循环引用占用的内存。循环垃圾回收器会周期性地检测程序中的循环引用，并释放无法访问的对象

## 21 列表和元组有什么区别
元组是不可变的（Immutable），一旦创建，元组的内容就不能被修改，添加或删除元素，或者改变大小。

## 22 Python 切片
切片是 Python 中用于从序列（例如列表、元组、字符串等）中获取子序列的一种机制。切片的语法形式是 start:stop:step，其中：
start 表示起始索引，切片包含该索引对应的元素。  
stop 表示终止索引，切片不包含该索引对应的元素。  
step 表示步长，表示从起始索引到终止索引每隔多少个元素取一个  
```python
# 切片列表
my_list = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# 获取索引为2到5（不包含）的元素
slice_1 = my_list[2:5]
print(slice_1)  # 输出：[2, 3, 4]

# 获取索引为2到末尾的元素
slice_2 = my_list[2:]
print(slice_2)  # 输出：[2, 3, 4, 5, 6, 7, 8, 9]

# 获取开头到索引为5（不包含）的元素
slice_3 = my_list[:5]
print(slice_3)  # 输出：[0, 1, 2, 3, 4]

# 获取所有元素，步长为2
slice_4 = my_list[::2]
print(slice_4)  # 输出：[0, 2, 4, 6, 8]

# 逆序列表
slice_5 = my_list[::-1]
print(slice_5)  # 输出：[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

```

## 23 chr() 和 ord()
chr() 函数返回表示 Unicode 代码点为整数的字符的字符串, 例如，chr(122) 返回字符串 z
ord() 返回一个表示字符的 Unicode 代码格式的整数,  ord("z") 122

##  24 __new__和__init__区别
__new__ 主要用于创建对象，返回一个实例。  
__init__ 主要用于初始化对象，不返回值。  
__new__ 在对象创建之前调用，负责对象的创建和返回。  
__init__ 在对象创建后调用，负责对象的初始化。  

## 25 字典和 json 转换
字典转 json
```python
import json
dict1 = {'zhangfei':1, "liubei":2, "guanyu": 4, "zhaoyun":3}
myjson = json.dumps(dict1)
myjson
# '{"zhangfei": 1, "liubei": 2, "guanyu": 4, "zhaoyun": 3}'
```
json 转字典
```python
mydict = json.loads(myjson)
mydict
# {'zhangfei': 1, 'liubei': 2, 'guanyu': 4, 'zhaoyun': 3}
```

## 26 快速排序
```python
def quickSort(nums, left, right):
    if left < right:
        index = partion(nums, left, right)
        quickSort(nums, left, index-1)
        quickSort(nums, index+1, right)
    return nums


def partion(nums, low, high):
    pivot = nums[low]
    while low < high:
        while low < high and nums[high] >= pivot:
            high -= 1
        nums[low] = nums[high]
        while low < high and nums[low] <= pivot:
            low += 1
        nums[high] = nums[low]
    nums[low] = pivot
    return low

arr = [i for i in range(9,-1,-1)]

print(quickSort(arr, 0, 9))

```

## 27 python java c++ 区别
Python： Python 是解释型语言，代码在运行时通过解释器逐行执行。
Java： Java 是编译型语言，代码先被编译成字节码，然后在 Java 虚拟机（JVM）上执行。
C++： C++ 是编译型语言，代码在编译时被转换成机器码，直接在计算机上运行

## 28 Python异步任务
使用asyncio库
```python
import asyncio

async def print_fuck():
    print("fuck")

async def print_me():
    # await 是Python中用于异步编程的关键字，通常与async关键字一起使用。在异步编程中，
    # await 用于等待一个异步操作完成。它告诉解释器在执行到这一行代码时，如果遇到一个异步操作，
    # 就挂起当前任务，让出控制权给事件循环，等待异步操作完成后再继续执行下面的代码。
    await print_fuck()
    print("me")

asyncio.run(print_me())
```

## 29 True is False == False 结果
(True is False) and (False == False) #  False

## 30 Python消息队列
在Python中，消息队列是一种常见的用于在不同组件之间传递消息的通信模式。Python有多个库可以用于实现消息队列，其中最常用的包括：

1. **Queue 模块：**
   Python标准库中的`queue`模块提供了`Queue`类，可以用于实现简单的先进先出（FIFO）队列。这对于在多线程应用程序中共享数据是有用的。以下是一个简单的例子：

    ```python
    from queue import Queue
   
    # 创建一个队列
    my_queue = Queue()
   
    # 在队列中放入数据
    my_queue.put("Message 1")
    my_queue.put("Message 2")
   
    # 从队列中取出数据
    message = my_queue.get()
    print(message)
    ```

2. **multiprocessing 模块：**
   `multiprocessing`模块提供了一个`Queue`类，可以用于在多进程之间共享数据。这对于在并行处理中传递消息很有用。

    ```python
    from multiprocessing import Process, Queue
   
    def worker(queue):
        message = queue.get()
        print(f"Worker received: {message}")
   
    if __name__ == "__main__":
        # 创建一个进程间共享的队列
        shared_queue = Queue()
   
        # 启动进程
        process = Process(target=worker, args=(shared_queue,))
        process.start()
   
        # 向队列中放入数据
        shared_queue.put("Hello from main process")
   
        # 等待进程结束
        process.join()
    ```

3. **Celery：**
   `Celery`是一个分布式任务队列，用于处理异步任务。它可以用于将任务分发给多个工作进程或者远程工作者。
    ```python
    from celery import Celery
   
    app = Celery('tasks', broker='pyamqp://guest@localhost//')
   
    @app.task
    def add(x, y):
        return x + y
    ```

   在这个例子中，`Celery`通过`broker`参数指定了消息队列的位置。`add`函数是一个异步任务，可以被分发到不同的工作进程或者远程工作者中执行。

这些都是不同场景下使用的消息队列的例子，你可以根据你的需求选择适合的库。

## 31 https如何加密
HTTPS通过在HTTP和TCP之间引入SSL/TLS层，使用加密和证书验证等机制来保护数据的机密性和完整性。这使得通过HTTPS传输的数据更难被窃听、篡改或伪造。

## 32 Celery用法
`Celery` 是一个用于处理异步任务的分布式任务队列框架，它允许你将任务发送到一个异步工作者（worker）集群，并通过消息中间件进行通信。以下是 `Celery` 的基本用法：

1. **安装 Celery：**
   使用以下命令安装 Celery：

   ```bash
   pip install celery
   ```

2. **创建 Celery 应用：**
   在你的项目中创建一个 Celery 应用，通常在一个单独的模块中。例如，创建一个 `tasks.py` 文件：

   ```python
   # tasks.py
   from celery import Celery
   
   app = Celery('tasks', broker='pyamqp://guest@localhost//')
   
   @app.task
   def add(x, y):
       return x + y
   ```

   在这个例子中，我们创建了一个 Celery 应用实例，并定义了一个异步任务 `add`。

3. **启动 Celery Worker：**
   启动 Celery worker 进程来处理任务。在命令行中运行：

   ```bash
   celery -A tasks worker --loglevel=info
   ```

   这里 `-A tasks` 参数指定了 Celery 应用所在的模块。

4. **调用 Celery 任务：**
   在你的应用中，你可以通过 Celery 应用实例的 `delay` 方法或者 `apply_async` 方法来调用任务。

   ```python
   # 在应用中调用任务
   from tasks import add
   
   result = add.delay(4, 4)
   ```

   或者使用 `apply_async`：

   ```python
   result = add.apply_async(args=(4, 4))
   ```

5. **获取任务结果：**
   你可以通过 `get` 方法获取任务的执行结果：

   ```python
   result = add.delay(4, 4)
   result.get()
   ```

   这会阻塞当前进程，直到任务完成并返回结果。

6. **配置 Celery：**
   Celery 提供了丰富的配置选项，你可以配置消息中间件、结果存储、序列化方式等。配置通常放在一个名为 `celeryconfig.py` 的文件中。

   ```python
   # celeryconfig.py
   broker_url = 'pyamqp://guest@localhost//'
   result_backend = 'rpc://'
   ```

   在启动 worker 时，你可以通过 `-c` 参数指定配置文件：

   ```bash
   celery -A tasks worker --loglevel=info -c celeryconfig
   ```

这是一个基本的 Celery 使用示例。`Celery` 还支持更高级的特性，如任务重试、任务定时调度、分布式任务执行、任务结果存储等。你可以根据项目的需求，进一步配置和使用 `Celery`。

## 33 默认参数是可变对象（如列表）
默认参在函数定义时只被创建一次，并在每次调用函数时重用
```python
def append_to_list(value, my_list=[]):
    my_list.append(value)
    return my_list

result1 = append_to_list(1)
result2 = append_to_list(2)

print(result1)  # 输出 [1, 2]
print(result2)  # 输出 [1, 2]
```
在这个例子中，函数append_to_list有一个默认参数my_list，它是一个可变的列表。当第一次调用函数时，result1包含了预期的值 [1]。然而，当第二次调用函数时，result2包含了预期之外的值 [1, 2]
为了避免这种问题，使用不可变对象作为默认参数的值

## 34 __call__
`__call__`是Python中的一个特殊方法，它允许一个对象实例像函数一样被调用。如果一个类实现了`__call__`方法，那么这个类的实例可以被当作函数来调用。

下面是一个简单的例子：

```python
class CallableClass:
    def __init__(self):
        pass

    def __call__(self, *args, **kwargs):
        print("Instance is called with arguments:", args, kwargs)

# 创建一个实例
obj = CallableClass()

# 调用实例，触发 __call__ 方法
obj(1, 2, keyword='value')
```

在这个例子中，`CallableClass`类实现了`__call__`方法。当创建了一个`CallableClass`的实例`obj`后，可以像调用函数一样使用`obj(1, 2, keyword='value')`来调用这个实例。这将触发`__call__`方法，并输出相应的信息。

`__call__`方法的灵活性允许你在对象实例被调用时执行特定的操作，这在某些情况下非常有用。例如，可以在`__call__`中实现一些处理逻辑，让对象实例的行为更像一个函数。

```python
class Counter:
    def __init__(self):
        self.count = 0

    def __call__(self):
        self.count += 1
        return self.count

# 创建一个计数器实例
counter = Counter()

# 调用实例，实现计数功能
print(counter())  # 输出 1
print(counter())  # 输出 2
print(counter())  # 输出 3
```

在这个例子中，每次调用`counter()`时，`__call__`方法会更新计数器并返回当前计数值。这样，对象实例就可以被像函数一样调用，并且具有状态。

## 35 总共50g文件利用4g内存统计出现次数最多的10个文件
处理一个50GB的文件，而且内存限制只有4GB，需要采取一些策略以有效地完成任务。以下是一种可能的思路：

1. **分块处理：** 将文件分割成小块，每次处理一部分。这样可以避免一次性加载整个文件到内存中。

2. **哈希映射：** 使用哈希映射来保存文件名和其出现次数的对应关系。在每个文件块中，统计文件名出现的次数，并将结果存储在哈希映射中。

3. **合并结果：** 在处理完所有文件块后，将所有哈希映射的结果进行合并。可以采用优先队列（堆）来找到出现次数最多的文件。

4. **优先队列（堆）：** 使用优先队列来保持当前出现次数最多的文件。每次从合并的结果中取出一个文件名和其出现次数，将其插入优先队列中。如果队列的大小超过10，就弹出队列顶部的元素，以保持队列中只有前10个最频繁的文件。

5. **最终结果：** 一旦完成了所有文件块的处理和合并，优先队列中的元素就是出现次数最多的前10个文件。

这种方法通过分块处理文件和使用哈希映射进行统计，能够在有限的内存条件下完成大文件的处理。然而，由于分块处理可能导致某些文件出现在多个块中，需要在合并结果时进行适当的处理以确保准确性。

## 36 抽象类
在Python中，抽象类是一种特殊的类，它不能被实例化，而是用来定义其他类的共同接口。**抽象类通常包含至少一个抽象方法**，这是一种没有具体实现的方法，需要在子类中被重写。

Python中实现抽象类的模块是`abc`（Abstract Base Classes）。以下是创建和使用抽象类的基本示例：

```python
from abc import ABC, abstractmethod

# 定义一个抽象类
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

# 尝试实例化抽象类会引发TypeError
# shape = Shape()  # 这行代码会报错

# 创建子类并实现抽象方法
class Square(Shape):
    def __init__(self, side):
        self.side = side
    def area(self):
        return self.side * self.side

    def perimeter(self):
        return 4 * self.side

# 实例化子类
square = Square(5)

# 调用子类的方法
print("Area:", square.area())  # 输出: 25
print("Perimeter:", square.perimeter())  # 输出: 20
```

在上面的例子中，`Shape`是一个抽象类，定义了两个抽象方法`area`和`perimeter`。然后，`Square`是`Shape`的子类，并实现了这两个抽象方法。**如果子类没有实现抽象类中的所有抽象方法，它仍然被认为是抽象类**，无法实例化。

## 37 读写文件

```python
# 打开文件并读取内容
with open('example.txt', 'r') as file:
    content = file.read()

# 输出文件内容
print(content)

# 打开文件并写入内容
with open('example.txt', 'w') as file:
    file.write('Hello, this is a sample text.\n')
    file.write('This is another line in the file.\n')
    
# 打开二进制文件并写入内容
with open('example.bin', 'wb') as file:
    data = b'\x48\x65\x6c\x6c\x6f\x2c\x20\x57\x6f\x72\x6c\x64'
    file.write(data)

# 打开文件并追加内容
with open('example.txt', 'a') as file:
    file.write('This line is appended to the file.\n')

with open('example.txt', 'w+') as file:
    # 写入内容
    file.write('Hello, this is a sample text.\n')

    # 将文件指针移动到文件开头
    file.seek(0)

    # 读取文件内容
    content = file.read()

    print(content)


```

## 38 POST和PUT区别
PUT： 通常用于更新资源或创建新资源。当客户端知道资源的URI，并希望在该URI下存储给定的实体时，可以使用PUT请求。PUT请求是幂等的，这意味着多次执行相同的PUT请求的效果应该与执行一次相同。
POST： 主要用于提交实体数据，通常用于创建新资源

