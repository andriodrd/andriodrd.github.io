# Python工作笔记

## 1、装饰器

### 1.1	装饰器介绍

> - 接地气定义
>
>   > - Python的装饰器(decorator)可以说是Python的一个神器，它可以在不改变一个函数代码和调用方式的情况下给函数添加新的功能。
>
> - 本质定义
>
>   > - Python的装饰器本质上是一个嵌套函数，它接受被装饰的函数(func)作为参数，并返回一个包装过的函数（不带()）。这样我们可以在不改变被装饰函数的代码的情况下给被装饰函数或程序添加新的功能。
>
> - 应用场景
>
>   > - Python的装饰器广泛应用于缓存、权限校验(如django中的@login_required和@permission_required装饰器)、性能测试(比如统计一段程序的运行时间)和插入日志等应用场景。
>
> - 好处
>
>   > - 有了装饰器，我们就可以抽离出大量与函数功能本身无关的代码，增加一个函数的重用性。

### 1.2	装饰器分类

> - 分类 - 参考网址https://0x9.me/tWbh2
>
>   > - 不带参数的普通装饰器和带多值参数的高级装饰器
>
> - 不带参数的普通装饰器
>
>   > - 暂无，待补充
>
> - 带多值参数的高级装饰器
>
>   > ```python
>   > # 登录装饰器
>   > def login_required(func):  # Python 中的函数可以像普通变量一样当做参数传递给另外一个函数
>   >     '''登录装饰器页面'''
>   >     def wraps(request, *args, **kwargs): # 定义的参数用*和**指定参数是列表还是字典
>   >         # 判断用户是否登录
>   >         if request.session.has_key('islogin'):
>   >             return func(request, *args, **kwargs)  # 把 func 当做参数传递进来时，执行close()就相当于执行了func()
>   >         else:
>   >             # 用户未登录，跳转至登录页
>   >             return redirect('/login')
>   >     return wraps  # Python的函数可以作为返回值
>   > 
>   > ```

