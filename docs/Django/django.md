

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

### 1.6	定义视图

> ```
> // 1.应用包下views.py文件定义视图函数跟接收浏览器请求，进行处理，返回页面相关
> ```
>
> > ```python
> > // 举例如下
> > from django.shortcuts import render
> > from 应用包.models import *
> > # /index
> > def index(request):
> >     '''首页，展示所有图书'''
> >     # 查询所有图书
> >     booklist = BookInfo.objects.all()
> >     # 将图书列表传递到模板中，然后渲染模板
> >     context = {'booklist': booklist}
> >     return render(request, 'booktest/index.html', context)
> > ```
> >
> > 

### 1.7	配置Url

> ```python
>// 1.项目包/urls.py文件，配置url举例如下：
> from django.contrib import admin
> from django.conf.urls import url,include
> urlpatterns = [
>  url(r'^admin/', admin.site.urls),
>  # 引入booktest的url配置
>     url(r'^', include('booktest.urls'))
>    ]
>    
> // 2.在booktest应用下创建urls.py文件，代码如下：
> from django.conf.urls import url
> from booktest import views
> urlpatterns = [
>  url(r'^index/$', views.index),
>  url(r'^detail/(\d+)/$', views.detail),
>    ]
>    ```

### 1.8	创建模板

> 
>
> ```python
> // 1.打开项目包/settings.py文件，配置模板查找目录TEMPLATES的DIRS
> import os
> TEMPLATES = [
>     {
>         'BACKEND': 'django.template.backends.django.DjangoTemplates',
>         'DIRS': [os.path.join(BASE_DIR, 'templates')],
>         'APP_DIRS': True,
>         'OPTIONS': {
>             'context_processors': [
>                 'django.template.context_processors.debug',
>                 'django.template.context_processors.request',
>                 'django.contrib.auth.context_processors.auth',
>                 'django.contrib.messages.context_processors.messages',
>             ],
>         },
>     },
> ]
> 
> // 2.创建templates/booktest/回显的页面.html文件。
> {#在模板中输出变量语法如下，变量可能是从视图中传递过来的，也可能是在模板中定义的{{ 变量名 }}{ 代码段 }#}
> <!DOCTYPE html>
> <html lang="en">
> <head>
>     <meta charset="UTF-8">
>     <title>图书列表</title>
> </head>
> <body>
> <ul>
>     {#遍历图书列表#}
>     {% for book in booklist %}
>         <li>
>         {#输入图书名称并设置超链接，连接地址是一个数字#}
>         <a href="/detail/{{ book.id }}/">{{ book.btitle }}</a>
>         </li>
>     {% endfor %}
> </ul>
> </body>
> </html>
> ```

### 1.9	启动服务器

> ```python
> // 1.启动调试服务器
> python manage.py runserver ip:port
> ```



## 2、模型

### 2.1	字段属性

> - Django根据属性的类型确定以下信息：
>
>   > - 当前选择的数据库支持字段的类型
>   > - 渲染管理表单时使用的默认html控件
>   > - 在管理站点最低限度的验证
>   >   django会为表创建自动增长的主键列，每个模型只能有一个主键列，如果使用选项设置某属性为主键列后django不会再创建自动增长的主键列。
>
> - 默认创建的主键列属性为id，可以使用pk代替，pk全拼为primary key。
>
>   > - 注意：pk是主键的别名，若主键名为id2，那么pk是id2的别名。
>
> - 属性命名限制：
>
>   > - 不能是python的保留关键字。
>   >
>   > - 不允许使用连续的下划线，这是由django的查询方式决定的。
>   >
>   > - 定义属性时需要指定字段类型，通过字段类型的参数指定选项，语法如下：
>   >
>   >   ```
>   >   属性=models.字段类型(选项)
>   >   ```
>

### 2.2	字段类型

> - 使用时需要引入django.db.models包，字段类型如下：
>
>   > - AutoField：自动增长的IntegerField，通常不用指定，不指定时Django会自动创建属性名为id的自动增长属性。
>   > - BooleanField：布尔字段，值为True或False。
>   > - NullBooleanField：支持Null、True、False三种值。
>   > - CharField(max_length=字符长度)：字符串。
>   >   - 参数max_length表示最大字符个数。
>   >
>   > - TextField：大文本字段，一般超过4000个字符时使用。
>   > - IntegerField：整数。
>   > - DecimalField(max_digits=None, decimal_places=None)：十进制浮点数。
>   >   - 参数max_digits表示总位数。
>   >   - 参数decimal_places表示小数位数。
>   >
>   > - FloatField：浮点数。
>   > - DateField[auto_now=False, auto_now_add=False])：日期。
>   >   - 参数auto_now表示每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为false。
>   >   - 参数auto_now_add表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为false。
>   >   - 参数auto_now_add和auto_now是相互排斥的，组合将会发生错误。
>   >
>   > - TimeField：时间，参数同DateField。
>   > - DateTimeField：日期时间，参数同DateField。
>   > - FileField：上传文件字段。
>   > - ImageField：继承于FileField，对上传的内容进行校验，确保是有效的图片。

### 2.3	字段选项

> - 通过选项实现对字段的约束，选项如下：
>
>   > - null：如果为True，表示数据库字段允许为空，默认值是False。
>   > - blank：如果为True，则后台管理该字段允许为空白，默认值是False。
>   >   - 对比：null是数据库范畴的概念，blank是表单验证范畴的**。
>   > - db_column：字段的名称，如果未指定，则使用属性的名称存入到数据库中。
>   > - db_index：若值为True, 则在数据库表中会为此字段创建索引，默认值是False。
>   > - default：设置字段属性类型的默认值
>   > - primary_key：若为True，则该字段会成为模型的主键字段，默认值是False，一般作为AutoField的选项使用。
>   > - unique：如果为True, 这个字段在数据库表中必须有唯一值，默认值是False。

### 2.4	字段查询

> - 通过模型类.objects属性可以调整如下函数，实现对模型类对应的数据表的查询。
>
>   > | 函数名     | 功能                                   | 返回值                 | 说明                                                         |
>   > | ---------- | -------------------------------------- | ---------------------- | ------------------------------------------------------------ |
>   > | get()      | 返回表中满足条件的一条且只能有一条数据 | 返回值是一个模型类对象 | 参数中写查询条件。<br />1. 如果查到多条数据，则抛出异常miltipleobjectsreturned<br />2.查询不到数据，则抛出doesnotexist |
>   > | all()      | 返回模型对应表格中的所有数据           | 返回值是queryset类型   | 查询集                                                       |
>   > | filter()   | 返回不满足条件的数据                   | 返回值是queryset类型   | 参数写查询条件                                               |
>   > | exclude()  | 返回不满足条件的数据                   | 返回值是queryset类型   | 参数写查询条件                                               |
>   > | order_by() | 对查询结果进行排序                     | 返回值是queryset类型   | 参数中写根据那些字段进行排序                                 |
>
> - 条件查询：语法  属性名称__比较运算符=值
>
>   > - 查询等：exact
>   > - 模糊查询：contains
>   > - 以指定值开头或结尾：startswith、endswith
>   > - 空查询：isnull
>   > - 范围查询：in [] 接的是列表
>   > - 比较查询：gt、gte、lt、lte：大于、大于等于、小于、小于等于
>   > - 日期查询：year、month、day、week_day、hour、minute、second：对日期时间类型的属性进行运算
>
> - F对象：F(属性名)-用于对属性的比较
>
>   ```python
>   from django.db.models import F
>   ...
>   list = BookInfo.objects.filter(bread__gte=F('bcomment'))
>   ```
>   
> - Q对象：Q(属性名__运算符=值)，使用&、|、~表示。
>
>   ```python
>   list = BookInfo.objects.filter(Q(bread__gt=20) | Q(pk__lt=3))
>   
>   //Q对象前可以使用~操作符，表示非not。
>   
>   //例：查询编号不等于3的图书。
>   
>   list = BookInfo.objects.filter(~Q(pk=3))
>   ```
>   
> - 聚合函数：aggregate(Avg，Count，Max，Min，Sum)过滤器调用聚合函数
>
>   ```python
>   //注意aggregate的返回值是一个字典类型，格式如下：
>   
>   // {'聚合类小写__属性名':值}
>   // 如:{'sum__bread':3}
>       
>   //例：查询图书的总阅读量。
>   
>   from django.db.models import Sum
>   ...
>   list = BookInfo.objects.aggregate(Sum('bread'))
>   
>   //使用count时一般不使用aggregate()过滤器。
>   
>   //例：查询图书总数。
>   
>   list = BookInfo.objects.count()
>   //注意count函数的返回值是一个数字。
>   ```
>   
> - 关联查询：实现类似于join查询。
>
>   > 1. 通过对象执行关联查询
>   >
>   >    > - 由一到多的访问语法-访问多
>   >    >
>   >    >   ```python
>   >    >   // 1.一对应的模型类对象.多对应的模型类名小写_set
>   >    >   b = BookInfo.objects.get(id=1)
>   >    >   b.heroinfo_set.all()
>   >    >   ```
>   >    >
>   >    > -   由多到一的访问语法-访问一
>   >    >
>   >    >     ```python
>   >    >     // 1.多对应的模型类对象.多对应的模型类中的关系类属性名
>   >    >     h = HeroInfo.objects.get(id=1)
>   >    >     h.hbook
>   >    >     ```
>   >    >
>   >    > - 访问一对一的模型类关联对象的id语法:访问一
>   >    >
>   >    >   ```python
>   >    >   // 1.多对应的模型类对象.关联类属性_id
>   >    >   h = HeroInfo.objects.get(id=1)
>   >    >   h.book_id
>   >    >   ```
>   >
>   > 2. 通过模型类执行关联查询
>   >
>   >    > - 由多模型类条件查询一模型类数据:访问一
>   >    >
>   >    >   ```python
>   >    >   // 1.语法：关联模型类名小写__属性名__条件运算符=值如果没有"__运算符"部分，表示等于，结果和sql中的inner join相同。
>   >    >   
>   >    >   // 2.例：查询图书，要求图书中英雄的描述包含'八'。
>   >    >   
>   >    >   BookInfo.objects.filter(heroinfo__hcontent__contains='八')
>   >    >   ```
>   >    >
>   >    > - 由一模型类条件查询多模型类数据:访问多
>   >    >
>   >    >   ```python
>   >    >   // 1.语法：一模型类关联属性名__一模型类属性名__条件运算符=值
>   >    >   
>   >    >   // 2.例：查询书名为“天龙八部”的所有英雄。
>   >    >   
>   >    >   list = HeroInfo.objects.filter(hbook__btitle='天龙八部')
>   >    >   ```
>   >    >
>   >    >   
>
>   
>
> - 总结
>
>   > 1. get：返回一条且只有一条数据，返回值是一个对象，参数可以写查询条件。
>   > 2. all：返回模型对应表的所有数据，返回值是queryset
>   > 3. filter：返回满足条件的数据，返回值是queryset，参数可以写查询条件
>   > 4. exclude：返回不满足条件的数据，返回值是queryset，参数可以写查询条件
>   > 5. order_by：对查询结果进行排序，返回值是queryset，参数可以写查询条件
>   > 6. 导入的包 from django.db.models import F,Q,Sum,Count,Avg,Max,Min
>   > 7. F对象：用于类属性之间的比较。
>   > 8. Q对象：用于条件之间的逻辑关系。
>   > 9. aggregate：进行聚合操作，返回值是一个字典，进行聚合的时候需要先导入聚合类。
>   > 10. count：返回结果集中数据的数目，返回值是一个数字。
>   >
>   > > 备注：
>   > >
>   > > - 对一个queryset实例对象，可以继续调用上面的所有函数。
>   > >
>   > > - queryset询集特性：
>   > >
>   > >   1）惰性查询：只有在实际使用查询集中的数据的时候才会发生对数据库的真正查询。
>   > >
>   > >   2）缓存：当使用的是同一个查询集时，第一次使用的时候会发生实际数据库的查询，然后把结果缓存起来，之后再使用这个查询集时，使用的是缓存中的。
>   > >
>   > >   3）可以用角标或者切片的方式来限制查询值。
>   > >
>   > >   - 可以对查询集进行取下标或切片操作，等同于sql中的limit和offset子句。
>   > >
>   > >     > 注意：不支持负数索引。对查询集进行切片后返回一个新的查询集，不会立即执行查询。如果获取一个对象，直接使用[0]，等同于[0:1].get()，但是如果没有数据，[0]引发IndexError异常，[0:1].get()如果没有数据引发DoesNotExist异常。

### 2.5	模型类关系

> - 关系数据类型
>
>   > 1. 关系型数据库的关系包括三种类型
>   >
>   >    > - ForeignKey：一对多，将字段定义在多的一端中。
>   >    > - ManyToManyField：多对多，将字段定义在任意一端中。
>   >    > - OneToOneField：一对一，将字段定义在任意一端中。
>   >    > - 可以维护递归的关联关系，使用'self'指定，详见"自关联"。
>   >
>   > 2. 一对多
>   >
>   >    > ```python
>   >    > // 1.英雄与图书表的关系为一对多，所以属性定义在英雄模型类中
>   >    > hbook = models.ForeignKey('BookInfo')
>   >    > ```
>   >
>   > 3. 多对多
>   >
>   >    > ```python
>   >    > // 1.通过ManyToManyField建立TypeInfo类和NewsInfo类之间多对多的关系
>   >    > ntype = models.ManyToManyField('TypeInfo') 
>   >    > ```
>   >
>   > 4. 一对一
>   >
>   >    > ```python
>   >    > // 1.通过OneToOneField建立TypeInfo类和NewsInfo类之间一对一的关系
>   >    > ntype = models.OneToOneField('TypeInfo') 
>   >    > ```
>   >
>   > 5. 自关联
>   >
>   >    > ```python
>   >    > // 1.关系属性使用self指向本类，一般要求null和blank允许为空
>   >    > aParent=models.ForeignKey('self',null=True,blank=True)
>   >    > ```
>   >
>   > 6. 