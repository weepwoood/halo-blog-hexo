---
title: Django
tags:
  - Django
  - Python
categories:
  - [Python, Django]
date: 2020-04-06 19:30:08
---



基于Django官方文档


---

## 使用Pycharm创建Django

![使用Pycharm创建Django]( http://img.whl123456.top/image/使用Pycharm创建Django.png)

Django项目结构

```
DjangoDemo				 # 根目录只是项目的容器
 ├── db.sqlite3			 # 数据库文件 
 ├── DjangoDemo          # 纯 Python 包
 │   ├── asgi.py         # 作为项目运行在 ASGI 兼容的Web服务器上的入口
 │   ├── wsgi.py         # 作为项目运行在 WSGI 兼容的Web服务器上的入口
 │   ├── settings.py     # Django 项目的配置文件
 │   ├── urls.py         # Django 项目的 URL 声明，就像你网站的“目录”
 │   └── __init__.py
 ├── manage.py           # 一个用各种方式管理 Django 项目的命令行工具
 ├── polls
 └── templates
```

Django应用结构

```
polls
 ├── admin.py
 ├── apps.py
 ├── migrations
 │   └── __init__.py
 ├── models.py
 ├── tests.py
 ├── views.py
 └── __init__.py
```

命令行启动项目

```shell
python manage.py runserver
```

## 创建应用视图

创建视图，打开 `polls/views.py`，把下面这些 Python 代码输入进去：

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

如果想看见效果，我们需要将一个 URL 映射到它——这就是我们需要 URLconf 的原因了。

创建 URLconf，在polls中新建`urls.py`文件

```
polls
 ├── admin.py
 ├── apps.py
 ├── migrations
 │   └── __init__.py
 ├── models.py
 ├── tests.py
 ├── urls.py
 ├── views.py
 └── __init__.py
```

在`urls.py`中，输入如下代码：

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

在根 URLconf 文件中指定我们创建的 `polls.urls` 模块。在 `DjangoDemo/urls.py` 文件的 `urlpatterns` 列表里插入一个 `include()`， 如下：

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

函数 [`include()`](https://docs.djangoproject.com/zh-hans/3.0/ref/urls/#django.urls.include) 允许引用其它 URLconfs。每当 Django 遇到 [`include()`](https://docs.djangoproject.com/zh-hans/3.0/ref/urls/#django.urls.include) 时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。

你现在把 `index` 视图添加进了 URLconf。通过以下命令验证是否正常工作：

```shell
python manage.py runserver
```

访问http://127.0.0.1:8000/polls/而不是http://127.0.0.1:8000

`path()`函数

`path(route, view, [kwargs], [name])`

+ `route`：是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 `urlpatterns` 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 `https://www.example.com/myapp/` 时，它会尝试匹配 `myapp/` 。处理请求 `https://www.example.com/myapp/?page=3` 时，也只会尝试匹配 `myapp/`。

+ `view`：当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 [`HttpRequest`](https://docs.djangoproject.com/zh-hans/3.0/ref/request-response/#django.http.HttpRequest) 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。

+  `kwargs`：任意个关键字参数可以作为一个字典传递给目标视图函数。

+ `name`：为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。

## 基础配置

在 `DjangoDemo/settings.py` 中

设置域名访问权限

```
myblog/settings.py
ALLOWED_HOSTS = []      # 修改前
ALLOWED_HOSTS = ['*']   # 修改后，表示任何域名都能访问。如果指定域名的话，在''里放入指定的域名即可
```

---

设置TEMPLATES里的'DIRS'，添加模板目录templates的路径，后面我们做网站模板的时候用得着。

```
# 修改前
'DIRS': []
# 修改后
'DIRS': [os.path.join(BASE_DIR, 'templates')]
# 注：使用pycharm创建的话会自动添加
```

---

找到DATABASES设置网站数据库类型。这里我们使用默认的sqlite3。如果需要使用Mysql请查看文章：[Django如何使用Mysql数据库](https://www.django.cn/forum/forum-6.html)，其它数据库请查看官方文档。[官方文档](https://docs.djangoproject.com/en/2.1/ref/settings/#databases)，后期上线部署的话，也可以进行数据库与数据库之间的数据转换。具体可查看：[如何把SQLite数据库转换为Mysql数据库](https://www.django.cn/article/show-17.html)

在INSTALLED_APPS添加APP应用名称。

```
INSTALLED_APPS = [                  # Django 的自带应用
    'django.contrib.admin',         # 管理员站点
    'django.contrib.auth',          # 认证授权系统。
    'django.contrib.contenttypes',  # 内容类型框架
    'django.contrib.sessions',      # 会话框架
    'django.contrib.messages',      # 消息框架。
    'django.contrib.staticfiles',   # 管理静态文件的框架
    'polls.apps.PollsConfig',       # 注册APP应用
]
# 使用pycharm创建的话，这里自动添加
```

---

修改项目语言和时区

```
myblog/settings.py
# 修改前为英文
# LANGUAGE_CODE = 'en-us'
# 修改后
LANGUAGE_CODE = 'zh-hans'  # 语言修改为中文
# 时区，修改前
# TIME_ZONE = 'UTC'
# 修改后
TIME_ZONE = 'Asia/Shanghai'
```

---

在项目根目录里创建static和media，两个目录。static用来存放模板CSS、JS、图片等静态资源，media用来存放上传的文件，后面我们在讲解数据库创建的时候有说明。

settings里找到STATIC_URL，然后在后面一行加上如下代码。

```
# 设置静态文件目录和名称
STATIC_URL = '/static/'

# 加入下面代码

# 这个是设置静态文件夹目录的路径
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
# 设置文件上传路径，图片上传、文件上传都会存放在此目录里
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

基本配置完成，更多关于配置文件的介绍，请查看文章：[全局配置settings详解](https://www.django.cn/course/show-10.html)

## 配置数据库

在 `DjangoDemo/settings.py` 中

```python
# 默认情况
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

+ `ENGINE`：可选值有 `'django.db.backends.sqlite3'`，`'django.db.backends.postgresql'`，`'django.db.backends.mysql'`，或 `'django.db.backends.oracle'`。其它 [可用后端](https://docs.djangoproject.com/zh-hans/3.0/ref/databases/#third-party-notes)。

+ `NAME`：数据库的名称。如果使用的是 SQLite，数据库将是你电脑上的一个文件，在这种情况下， [`NAME`](https://docs.djangoproject.com/zh-hans/3.0/ref/settings/#std:setting-NAME) 应该是此文件的绝对路径，包括文件名。默认值 `os.path.join(BASE_DIR, 'db.sqlite3')` 将会把数据库文件储存在项目的根目录。

```python
############修改成mysql如下：
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'test',    #你的数据库名称
        'USER': 'root',   #你的数据库用户名
        'PASSWORD': '445813', #你的数据库密码
        'HOST': '', #你的数据库主机，留空默认为localhost
        'PORT': '3306', #你的数据库端口
    }}

#由于mysql默认引擎为MySQLdb，在__init__.py文件中添加下面代码
#在python3中须替换为pymysql,可在主配置文件（和项目同名的文件下，不是app配置文件）中增加如下代码
#import pymysql
#pymysql.install_as_MySQLdb()
#如果找不到pymysql板块，则通过pip install pymysql进行安装。
```

数据库设置好之后，我们就依次输入下面的命令进行数据库迁移：

```shell
python manage.py makemigrations
python manage.py migrate
```

这个 [`migrate`](https://docs.djangoproject.com/zh-hans/3.0/ref/django-admin/#django-admin-migrate) 命令检查 [`INSTALLED_APPS`](https://docs.djangoproject.com/zh-hans/3.0/ref/settings/#std:setting-INSTALLED_APPS) 设置，为其中的每个应用创建需要的数据表，至于具体会创建什么，这取决于你的 `settings.py` 设置文件和每个应用的数据库迁移文件。

## 创建模型

在这个投票应用中，需要创建两个模型：问题 `Question` 和选项 `Choice`。

`Question` 模型包括问题描述和发布时间。`Choice` 模型有两个字段，选项描述和当前得票数。每个选项属于一个问题。

在`models.py`文件中写入以下内容：

```python
from django.db import models

# Create your models here.


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

每个模型被表示为 [`django.db.models.Model`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/instances/#django.db.models.Model) 类的子类。每个模型有许多类变量，它们都表示模型里的一个数据库字段。

每个字段都是 [`Field`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field) 类的实例 - 比如，字符字段被表示为 [`CharField`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.CharField) ，日期时间字段被表示为 [`DateTimeField`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.DateTimeField) 。这将告诉 Django 每个字段要处理的数据类型。

每个 [`Field`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field) 类实例变量的名字（例如 `question_text` 或 `pub_date` ）也是字段名，所以最好使用对机器友好的格式。你将会在 Python 代码里使用它们，而数据库会将它们作为列名。

你可以使用可选的选项来为 [`Field`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field) 定义一个人类可读的名字。这个功能在很多 Django 内部组成部分中都被使用了，而且作为文档的一部分。如果某个字段没有提供此名称，Django 将会使用对机器友好的名称，也就是变量名。在上面的例子中，我们只为 `Question.pub_date` 定义了对人类友好的名字。对于模型内的其它字段，它们的机器友好名也会被作为人类友好名使用。

定义某些 [`Field`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field) 类实例需要参数。例如 [`CharField`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.CharField) 需要一个 [`max_length`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.CharField.max_length) 参数。这个参数的用处不止于用来定义数据库结构，也用于验证数据，我们稍后将会看到这方面的内容。

[`Field`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field) 也能够接收多个可选参数；在上面的例子中：我们将 `votes` 的 [`default`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.Field.default) 也就是默认值，设为0。

注意在最后，我们使用 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/fields/#django.db.models.ForeignKey) 定义了一个关系。这将告诉 Django，每个 `Choice` 对象都关联到一个 `Question` 对象。Django 支持所有常用的数据库关系：多对一、多对多和一对一。

## 激活模型

把 `polls` 应用安装到我们的项目里，见基础配置

运行下面的命令:

```shell
python manage.py makemigrations polls
```

Terminal输出：

```
Migrations for 'polls':
  polls\migrations\0001_initial.py
    - Create model Question
    - Create model Choice
```

通过运行 `makemigrations` 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次迁移。

迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式，它们其实也只是一些你磁盘上的文件。如果你想的话，你可以阅读一下你模型的迁移数据，它被储存在 `polls/migrations/0001_initial.py` 里。别担心，你不需要每次都阅读迁移文件，但是它们被设计成人类可读的形式，这是为了便于你手动调整Django的修改方式。

## Django 管理页面

### 创建一个管理员账号

```shell
python manage.py createsuperuser
```

