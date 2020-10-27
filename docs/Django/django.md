

# Django入门

## 1、Djiango入门命令

### 1.1	创建项目

> ```python
> // 1.创建项目
> django-admin startproject 项目名
> ```

> > ![image-20201027224217724](assets/image-20201027224217724.png)
> >
> > ```
> > - 目录说明
> >   - manage.py：一个命令行工具，可以使你用多种方式对Django项目进行交互
> >   - 内层的目录：项目的真正的Python包
> >   - *_init* _.py：一个空文件，它告诉Python这个目录应该被看做一个Python包
> >   - settings.py：项目的配置
> >   - urls.py：项目的URL声明
> >   - wsgi.py：项目与WSGI兼容的Web服务器入口
> > ```

### 1.2	创建应用

> ```python
> // 1.创建应用
> python manage.py startapp booktest
> ```
>
> > ![image-20201027224710307](assets/image-20201027224710307.png)
> >
> > ```
> > - 目录说明
> > 	- admin.py：管理后台网站
> >   - *_init* _.py：一个空文件，它告诉Python这个目录应该被看做一个Python包
> >   - migrations：迁移文件包
> >   - models.py：与数据库进行交互的
> >   - tests.py：单元测试的
> >   - views.pt: 接收浏览器请求，进行处理，返回页面相关
> >   - urls.py: 应用的URL声明
> > ```

### 1.3	数据库配置

> ```
> // 1.配置成Mysql数据库,在settings.py文件中，通过DATABASES项进行数据库设置
> ```
>
> > ```python
> > # python3需要安装pymysql,且需要在'项目包'/__init__.py中加如下内容：import pymysql，pymysql.install_as_MySQLdb()
> > DATABASES = {
> >     'default': {
> >         'ENGINE': 'django.db.backends.mysql',
> >         'NAME': '数据库名',
> >         'USER': '用户',
> >         'PASSWORD': '密码',
> >         'HOST': '地址',
> >         'PORT': '端口',
> >     }
> > }
> > ```

### 1.4	定义模型类

> ```
> // 1.有一个数据表，就有一个模型类与之对应
> ```
>
> > ```python
> > from django.db import models
> > # models.py文件跟数据库操作相关
> > # 1.在models.py中定义模型类
> > # 2.迁移
> > # 3.通过类和对象完成数据增删改查操作
> > # 定义模型类
> > class BookInfo(models.Model):
> >     pass
> > ```

### 1.5	生成数据表

> ```python
> // 1.激活模型：编辑settings.py文件，将booktest应用加入到installed_apps中
> INSTALLED_APPS = [
>   'django.contrib.admin',
>   'django.contrib.auth',
>   'django.contrib.contenttypes',
>   'django.contrib.sessions',
>   'django.contrib.messages',
>   'django.contrib.staticfiles',
>   'booktest.apps.BooktestConfig',
> ]
> // 2.生成迁移文件：根据模型类生成sql语句
> python manage.py makemigrations
> // 3.执行迁移：执行sql语句生成数据表
> python manage.py migrate
> ```
>

### 1.6	服务器

> ```
> // 1.启动调试服务器
> python manage.py runserver ip:port
> ```

