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
在这个例子中，函数进入和退出时 ，被称为一个横切面，这种编程方式被称为面向切面(AOP)的编程
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
class Singleton:
    _instance = None
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super(Singleton, cls).__new__()
        return cls._instance
```

## 12 GIL线程全局锁
线程全局锁(Global Interpreter Lock), Python为了保证线程安全**一个核只能在同一时间运行一个线程**。GIL对于CPU密集型任务的性能影响较大，而对于I/O密集型任务的性能影响较小。在处理I/O时，线程可以释放GIL，允许其他线程执行。
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
b = a: 赋值引用，a 和 b 都指向同一个对象

b = a.copy(): 浅拷贝, a 和 b 是一个独立的对象，但他们的子对象还是指向统一对象（是引用）,生成了一个新的变量，而变量所指向的对象和原来的是一样的

b = copy.deepcopy(a): 深度拷贝, a 和 b 完全拷贝了父对象及其子对象，两者是完全独立的。
```python
# 赋值引用和浅拷贝的区别
original_list = [1, 2, [3, 4]]
a = copy.copy(original_list)
original_list[2][1] = 0
print(original_list)    # [1, 2, [3, 0]]
print(a)    # [1, 2, [3, 0]]


origin = "sss"
b = origin
c = copy.copy(origin)
origin = "asd"
print(origin)   # asd
print(b)    # sss
print(c)    # sss
```

## 18 Python的is
is是对比地址,==是对比值