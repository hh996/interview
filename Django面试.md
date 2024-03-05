# 整体结构
## 1 设计模式中的MVC模式
模型（Model）： 负责管理应用程序的数据

视图（View）： 负责呈现用户界面和显示数据

控制器（Controller）： 负责处理用户输入、更新模型和通知视图的变化

后端使用 MVC 来管理数据模型、处理业务逻辑，而前端使用 MVC 或其变种（如MVVM）来管理用户界面

## 2 Django MTV模型
模型（Model）：负责处理与数据相关的操作。在Django中，模型类定义了数据的字段和行为，可以通过 ORM（对象关系映射）与数据库进行交互，而无需直接使用 SQL。

模板（Template）：

视图（View）：视图是模型和模板之间的协调者，负责处理用户的请求，从模型中检索数据，并将数据传递给模板，最终生成响应。

工作流程如下：用户发起请求→视图处理请求→模板生成响应→响应返回给用户

## 3 Django中的模块
django.urls：提供 URL 配置和路由的功能，用于将请求映射到相应的视图。  
django.views：包含一些通用视图和基础视图的功能，用于处理常见的 HTTP 请求。  
django.test： 提供用于编写单元测试和功能测试的工具和类。  
django.settings： 包含 Django 项目的设置，如数据库配置、调试设置等。  
django.management： 提供命令行管理工具，用于执行与项目和应用程序管理相关的任务。

## 4 Django自带的Admin
 Django Admin 提供了一个直接可用的模型管理界面，可以快速查看和修改数据，利用 Django Admin 的自定义能力，添加自定义视图、定制样式和功能
 
## 5 WSGI的作用
WSGI（Web Server Gateway Interface） Web 服务器与 Python Web 应用程序之间通信接口的标准
```python
HELLO_WORLD = b"Hello world!\n"

def application(environ, start_response):
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain')]
    start_response(status, response_headers)    # 调用了 start_response 函数，将状态码和响应头传递给 Web 服务器
    return [HELLO_WORLD]
```
WSGI是Python在处理HTTP请求时，规定的一种处理方式。如一个HTTP Request过来了，那么就有一个相应的处理函数来进行处理和返回结果。WSGI就是规定这个处理函数的参数长啥样的，它的返回结果是长啥样的？至于该处理函数的名子和处理逻辑是啥样的，那无所谓。简单而言，WSGI就是规定了处理函数的输入和输出格式。


## 6 为什么正式部署时不要开启DEBUG = True配置
安全性： 开启调试模式会暴露应用程序的内部信息，包括详细的错误信息、文件路径等
性能： 调试模式会导致应用程序运行时额外的开销
不稳定的代码： 在调试模式下，Django 会尝试捕获异常，关闭调试模式有助于及早发现和解决这些问题。

## 7 CSRF
CSRF（Cross-Site Request Forgery，跨站请求伪造）
Django中的CSRF保护机制通常使用一个特殊的令牌（CSRF令牌）来验证请求的合法性。这个令牌会嵌入到表单中，或者作为Cookie发送给客户端
CSRF保护是默认开启的，你可以在模板中使用 {% csrf_token %} 标签来包含CSRF令牌。在视图中，Django也提供了 @csrf_protect 装饰器，用于启用CSRF保护

## 7 Q
Q查询：对数据的多个字段联合查询（常和且或非"&|~"进行联合使用）
F查询：提供了一种方式，允许在数据库层面上执行比较和计算、字段值的更新
```python
from django.db.models import Q
from myapp.models import MyModel

# 使用Q对象构建查询条件
query = Q(name__icontains='John') | Q(age__gte=25)
# 在查询中使用Q对象
results = MyModel.objects.filter(query)

# 假设有一个模型（Model）定义如下：
from django.db import models
class Book(models.Model):
    title = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=5, decimal_places=2)
    quantity = models.IntegerField()

# 使用 "F 查询"，您可以在查询中引用模型字段，而不是具体的值。例如，如果您想查询价格小于库存量的图书：
from django.db.models import F
books = Book.objects.filter(price__lt=F('quantity'))
```

## 8 Cache
Django提供了内置的缓存框架，你可以配置为使用多种后端存储
配置缓存后端： 在Django的设置文件中，你可以配置缓存后端。Django支持多种后端，包括内存缓存、数据库缓存、文件系统缓存等
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
    }
}

```

## 9 url 里的 name
name 可以用于在 templates，models，views ..... 中得到对应的网址，相当于给网址取了个名字，只要这个名字不变，网址变了也能通过名字获取到。
https://www.cnblogs.com/rexcheny/p/9635879.html

## 10 Django 如何配置 log
在settings.py中配置日志设置
```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': '/path/to/django/debug.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```
在代码中记录日志
```python
import logging

logger = logging.getLogger(__name__)

def my_view(request):
    try:
        # 你的代码逻辑
        result = 1 / 0  # 引发一个异常
    except Exception as e:
        # 记录异常日志
        logger.exception("An error occurred: %s", str(e))
    return HttpResponse("View executed successfully.")
```

## 11 ASGI
ASGI是异步网关协议接口，一个介于网络协议服务和Python应用之间的标准接口，能够处理多种通用的协议类型，包括HTTP，HTTP2和WebSocket。  
ASGI对于WSGI原有的模式的支持和WebSocket的扩展，即ASGI是WSGI的扩展。

## 12 设计模式
设计模式是在软件设计中常见问题的最佳解决方案。根据目的和用途，设计模式可以分为创建型模式、结构型模式和行为型模式三类。

创建型模式：关注对象创建的最佳方式。主要有***单例模式、工厂模式***、抽象工厂模式、建造者模式和原型模式。  
结构型模式：关注如何组合类和对象以形成更大的结构。主要有适配器模式、***装饰器模式***、代理模式、外观模式、桥接模式、组合模式和享元模式。  
行为型模式：关注对象之间的通信和算法选择。主要有策略模式、模板方法模式、***观察者模式***、迭代器模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式和解释器模式。  

在 Django 中，主要用到了以下几种设计模式：

工厂模式：Django 的模型层使用了工厂模式来创建和操作数据库表。工厂模式用于创建对象的最佳方式，特别是当对象的创建方式复杂或不确定时。  
单例模式：在 Django 中，某些应用或中间件可能使用单例模式来确保全局只有一个实例。例如，Django 的缓存系统使用了单例模式来确保全局只有一个缓存系统。  
装饰器模式：Django 的装饰器用于修改或增强函数或类的行为。它们可以用来处理认证、授权、日志记录等。  
观察者模式：Django 的信号系统使用了观察者模式。当某个事件发生时（如模型对象的创建、更新或删除），可以自动触发一系列的函数或方法。  

单例模式 (Singleton Pattern):  
保证一个类只有一个实例，并提供一个全局访问点。    
例子：数据库连接、日志记录器等。  

工厂模式 (Factory Pattern):  
定义一个接口用于创建对象，但由子类决定实例化哪个类。    
例子：GUI库中的创建不同类型窗口的工厂。  

装饰者模式 (Decorator Pattern):  
动态地给一个对象添加一些额外的职责，就增加功能而言，装饰者模式比生成子类更为灵活。  
例子：输入输出流的装饰，如BufferedInputStream

观察者模式 (Observer Pattern):  
定义了对象间一对多的依赖关系，使得当一个对象改变状态时，所有依赖它的对象都会得到通知并自动更新。  
例子：GUI中的事件处理、发布-订阅系统。



# Model层
## 1 Django Migrations的作用
 Migration迁移文件包含了对数据库进行相应更改的指令，运行 migrate 命令，Django 会对数据库变更
 
## 2 ORM的概念
对象（Object）关系（Relationship）映射（Mapping）
将对象与关系数据库中的表进行映射，从而实现通过对象操作数据库的目的

## 3 ORM下的N+1问题
因为懒加载，在一个表里有N条数据，最终需要查N+1次SQL
```python
#  获取所有的文章数据，注意此时不会执行sql语句
# 懒加载 在检索主模型记录时，并不会立即加载关联模型的数据。而是当访问关联字段时，系统会执行额外的查询以获取关联模型的数据。
posts = Post.objects.all()  
result = []
for post in posts:   # 此时会执行select * from post的查询
    result.append({
        'title': post.title,
        'owner': post.owner.name,  # 此时会执行  select * from user where user_id = <post.user_id>
    })
```
可以采用预加载的方式，将查询改为join的方式
```python
posts_with_user = Post.objects.all().select_related('user')
# SELECT t1.title, t2.name from post as t1 INNER JOIN user as t2 ON t2.id = t1.owner_id;
```

## 4 Django中Model的作用
定义数据结构、提供 ORM 功能、支持数据库迁移和数据校验, 能够以面向对象的方式来操作数据库

## 5 Model的Meta属性类的可配置项
元数据（Model's Meta）是指模型类中的一个内部类，元数据提供了一种方式来配置和描述模型的一些非数据属性。这个类通常被命名为 Meta
### 1 db_table 指定数据库中存储该模型数据的表名
```python
class MyModel(models.Model):
    # 指定表名为 'my_custom_table'
    class Meta:
        db_table = 'my_custom_table'
```
### 2 ordering 指定查询结果的默认排序方式
```python
class Person(models.Model):
    name = models.CharField(max_length=30)
    age = models.IntegerField()

    # 按照年龄降序排序
    class Meta:
        ordering = ['-age']
```
### 3 unique_together 指定一组字段的组合必须唯一
```python
class MyModel(models.Model):
    field1 = models.CharField(max_length=30)
    field2 = models.CharField(max_length=30)

    # 指定 field1 和 field2 的组合必须唯一
    class Meta:
        unique_together = ('field1', 'field2')
```
### 4 index_together  指定一组字段的组合创建索引
```python
class MyModel(models.Model):
    field1 = models.CharField(max_length=30)
    field2 = models.CharField(max_length=30)

    # 为 field1 和 field2 的组合创建索引
    class Meta:
        index_together = ['field1', 'field2']
```
### 5 verbose_name 和 verbose_name_plural： 分别指定模型的单数和复数名称
```python
class Person(models.Model):
    name = models.CharField(max_length=30)

    # 在管理界面显示为 '个人' 和 '个人们'
    class Meta:
        verbose_name = '个人'
        verbose_name_plural = '个人们'
```
### 6 abstract： 如果设置为 True，表示该模型是抽象的，不会在数据库中生成对应的表。用于其他模型继承的基类。
```python
class BaseModel(models.Model):
    # 作为基类的抽象模型
    class Meta:
        abstract = True
```

## 6 QuerySet的作用，QuerySet优化措施
QuerySet 用于构建和执行数据库查询, 如 filter()、exclude()、all() 等
优化措施：使用 values() 或 only() 方法，仅获取需要的字段
```python
# 仅获取姓名和年龄字段
selected_fields = Person.objects.values('name', 'age')
```
使用索引、使用缓存

## 7 Pagination
Pagination（分页）是一种用于处理大量数据并将其分割成小块进行显示的机制
```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.shortcuts import render
from .models import YourModel

def your_view(request):
    # 获取所有数据
    all_data = YourModel.objects.all()

    # 每页显示的数据量
    items_per_page = 10

    # 创建 Paginator 实例
    paginator = Paginator(all_data, items_per_page)

    # 获取当前页数，默认为 1
    page = request.GET.get('page', 1)

    try:
        # 获取指定页数的数据
        current_page_data = paginator.page(page)
    except PageNotAnInteger:
        # 如果 page 参数不是一个整数，则默认取第一页
        current_page_data = paginator.page(1)
    except EmptyPage:
        # 如果 page 参数超出范围，则取最后一页
        current_page_data = paginator.page(paginator.num_pages)

    return render(request, 'your_template.html', {'current_page_data': current_page_data})
```

## 8 Model中Field
用于定义数据库表中的字段，它决定了该字段在数据库中的类型、约束和行为

## 9 Manager
Manager 是模型的查询接口，通过它可以进行数据库查询和操作
```python
class BookManager(models.Manager):
    def contain_keyword_count(self, keyword):
        return self.filter(name__icontains=keyword).count()

class Book(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()
    objects = BookManager()

# 不使用Manager
keyword = "python"
count = Book.objects.filter(name__icontains=keyword)
# 通过Manager来操作
keyword = "python"
count = Book.objects.contain_keyword_count(keyword)
```

## 10 Raw SQL的效率跟ORM的效率
Raw SQL：灵活，性能优化；可读性差，数据库移植性差  
ORM：可读性高，数据库移植性好，安全性高；性能损耗，灵活性较低

## 11 Django内置提供的权限逻辑
Django 的权限系统使用 django.contrib.auth.models.Permission 模型来表示权限；用户可以被分配到用户组，而用户组可以拥有一组权限
@login_required，用于在视图中进行权限检查

## 12 如何为模型建立索引
db_index=True
```python
from django.db import models
class YourModel(models.Model):
    # 为列建立索引
    your_column = models.CharField(max_length=100, db_index=True)
    class Meta:
        indexes = [
            models.Index(fields=['your_column']),
            # 你可以添加更多的索引...
        ]
```

## 13 model 如何实现继承
在Django中，你可以使用模型继承来实现继承关系。Django提供了三种模型继承方式：单表继承（OneToOneField）、抽象基类继承（abstract base class inheritance）和多表继承（multi-table inheritance）。

以下是这三种继承方式的简要说明：

1. **单表继承（OneToOneField）：**
   
   在单表继承中，每个子类都与父类共享相同的数据库表，通过`OneToOneField`建立与父类的关联。

   ```python
   from django.db import models

   class Person(models.Model):
       name = models.CharField(max_length=100)

   class Employee(models.Model):
       person = models.OneToOneField(Person, on_delete=models.CASCADE)
       employee_id = models.CharField(max_length=10)
   ```

2. **抽象基类继承：**
   
   抽象基类继承是通过设置`abstract = True`来创建一个不对应数据库表的抽象基类。抽象基类本身不对应数据库中的表，但它的子类将包含在数据库中生成的表中

   ```python
   from django.db import models

   class Animal(models.Model):
       name = models.CharField(max_length=100)

       class Meta:
           abstract = True

   class Cat(Animal):
       purr_sound = models.CharField(max_length=50)
   ```

3. **多表继承：**
   
   多表继承会在数据库中创建一个父表和多个子表，每个子表都包含了父表的字段。

   ```python
   from django.db import models

   class Person(models.Model):
       name = models.CharField(max_length=100)

   class Employee(Person):
       employee_id = models.CharField(max_length=10)
   ```




# View层
## 1 Function-Based Views（函数视图）和 Class-Based Views（类视图）
Function-Based Views（函数视图）：使用函数来定义视图, 简洁灵活
```python
from django.http import HttpResponse

def my_view(request):
    return HttpResponse("Hello, World!")
```

## 2 Class-Based Views（类视图）
允许将不同 HTTP 方法（如 GET、POST 等）的逻辑放在同一个类中
```python
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        return HttpResponse("Hello, World!")
```

## 3 Class-based View添加login required装饰器
使用 @method_decorator 装饰器将 login_required 装饰器应用到类视图的方法上
```python
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.views import View
from django.http import HttpResponse

@method_decorator(login_required, name='dispatch')
class MyProtectedView(View):
    def get(self, request):
        return HttpResponse("This is a protected view. You are logged in.")
```

## 4 Middleware在Django系统中的作用
Middleware 在 Django 中是一个处理 HTTP 请求和响应的钩子系统,允许开发者在请求到达视图之前或响应发送到客户端之前执行自定义的代码
```python
import time

class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # 记录请求开始时间
        start_time = time.time()

        # 继续处理请求
        response = self.get_response(request)

        # 计算请求处理时间
        end_time = time.time()
        processing_time = end_time - start_time

        # 打印请求处理时间
        print(f"Request processed in {processing_time:.4f} seconds")

        return response
```
每当请求到达时，TimingMiddleware 将记录请求开始时间，并在响应发送之前计算和打印请求处理时间

## 5 settings中默认配置的MIDDLEWARES
django.middleware.security.SecurityMiddleware
django.contrib.sessions.middleware.SessionMiddleware
django.contrib.auth.middleware.AuthenticationMiddleware

## 6 Django系统如何判断用户是否为登录用户
Django 使用 request 对象来判断用户是否为登录用户
```python
from django.shortcuts import render
from django.http import HttpResponse

def my_view(request):
    if request.user.is_authenticated:
        # 用户已登录
        return HttpResponse(f"Hello, {request.user.username}!")
    else:
        # 用户未登录
        return HttpResponse("Please log in.")
```
## 7 对于无Cookie的浏览器如何实现用户登录
使用 django-rest-framework-simplejwt 或其他 JWT（JSON Web Token）库来实现 Token 认证

## 8 Django中的request和HttpResponse
request 和 HttpResponse，分别用于表示客户端发起的请求和服务器返回的响应
request 对象代表了客户端发起的 HTTP 请求
HttpResponse 对象用于构建服务器返回给客户端的 HTTP 响应

## 9 图片
使用 ImageField 字段来存储图片，在视图中处理表单提交

## 10 Django的缓存
数据库查询缓存（Queryset Caching）
```python
from django.core.cache import cache

def get_data():
    key = 'my_data_key'
    data = cache.get(key)
    if data is None:
        # 数据库查询...
        data = queryset  # 假设这是数据库查询结果
        cache.set(key, data, timeout=3600)  # 设置缓存时间为 1 小时
    return data
```

## 11 django dispatch
Django中dispatch()函数的用法通常与HTTP请求方法装饰器结合使用，这些装饰器映射了对应的请求方法，如@require_GET、@require_POST等。然而，实际上在Django的类视图（Class-Based Views）中，dispatch()方法并不需要显式地与这些装饰器一起使用，因为它是视图基类（通常是View）中的一个方法，已经被设计好用于根据请求方法分派到相应的处理函数。
dispatch()方法在视图类中的工作方式是：当一个请求到达时，Django会根据URL配置调用相应的视图类。视图类的as_view()方法实际上会返回一个函数，这个函数在被调用时会实例化视图类，并调用其dispatch()方法。dispatch()方法查看请求的类型（GET、POST等），然后调用类中的相应方法（如get()、post()等）来处理请求。
下面是一个简单的示例，展示了如何在Django中使用dispatch()方法：
```python
from django.http import HttpResponse  
from django.views import View  
  
class MyView(View):  
    def get(self, request):  
        # 处理GET请求  
        return HttpResponse('Hello, World!')  
  
    def post(self, request):  
        # 处理POST请求  
        return HttpResponse('Hello, POST!')  
  
    def dispatch(self, request, *args, **kwargs):  
        # 在这里可以添加一些前置操作，比如权限检查  
        # 如果检查不通过，可以返回HttpResponseForbidden等响应  
        # 否则，调用父类的dispatch方法继续处理请求  
        return super().dispatch(request, *args, **kwargs)
```
在这个例子中，MyView类继承自Django的View类。它定义了get()和post()方法来处理GET和POST请求。dispatch()方法被重写以添加一些自定义逻辑，比如权限检查。最后，通过调用super().dispatch()来确保请求被正确地分派到get()或post()方法。


# Form 层
## 1 Form的作用
处理 HTML 表单， 将用户输入数据验证、转换和呈现到 HTML 表单的便捷方式
## 2 Form中的Field跟Model中的Field
字段类型相似
## 3 在Form层实现对某个字段的校验
在 Django 的 Form 中，可以通过定义一个特定的方法来实现对某个字段的校验。这个方法的命名规则为 clean_<fieldname>()，其中 <fieldname> 是要校验的字段名。这个方法会在整个表单验证过程中被调用，用于对特定字段进行自定义校验。
```python
from django import forms

class MyForm(forms.Form):
    name = forms.CharField(max_length=100, label='Your Name', required=True)
    age = forms.IntegerField(label='Your Age', required=True)

    def clean_age(self):
        age = self.cleaned_data.get('age')

        if age is not None and age < 18:
            raise forms.ValidationError("Age must be 18 or older.")

        return age

```

# Template层
接触的比较少
