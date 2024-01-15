# 整体结构
## 1 设计模式中的MVC模式
模型（Model）： 负责管理应用程序的数据

视图（View）： 负责呈现用户界面和显示数据

控制器（Controller）： 负责处理用户输入、更新模型和通知视图的变化

后端使用 MVC 来管理数据模型、处理业务逻辑，而前端使用 MVC 或其变种（如MVVM）来管理用户界面

## 2 Django MTV模型
模型（Model）：负责处理与数据相关的操作。在Django中，模型类定义了数据的字段和行为，可以通过 ORM（对象关系映射）与数据库进行交互，而无需直接使用 SQL。

模板（Template）：


。

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

## 6 为什么正式部署时不要开启DEBUG = True配置
安全性： 开启调试模式会暴露应用程序的内部信息，包括详细的错误信息、文件路径等
性能： 调试模式会导致应用程序运行时额外的开销
不稳定的代码： 在调试模式下，Django 会尝试捕获异常，关闭调试模式有助于及早发现和解决这些问题。

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
QuerySet 用于构建和执行数据库查询, 如 filter()、exclude() 等

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