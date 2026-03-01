
# 1.初始Django(startproject)
1.首先通过django-admin startproject来创建一个项目, 并且指定全局的config dir(虽然里面的文件都是.py)的名称与该proj的名称

2.进入proj后会有manage.py ,这个是Django内置的command工具,可以通过python3 manage.py 来实现与django-admin一致的功能;

# 2.创建功能模块(startapp)
3.startapp创建proj下每个app(这里的app并非整个应用,Django将实际的应用称为proj,每个具体,相对独立的功能模块最好自行设计一个app);  app内会创建多个app内配置.py文件;

# 3.Dive into Django 架构

### 3.1 views/urls
app目录下包含一个views.py文件, 该views文件是所有URLs实际映射到的函数集合,所有的函数都需要在此有声明;
对于Django,views并非是前端的UI展示资源,这种资源在Django中called template; 而views在Django中作为callable对象,比如捕获urls对应的http请求的function def func(request): 这个func就是view.

对于全局config, 其中的urls.py中存在一个urlpatterns列表,这是项目全局列表,所有实际Http请求的URLs全部被它捕获,通过django.urls.path()函数来映射到对应的_ResponseType变量;
其中path()函数至少接受两个params, 第一是str, 也是URL的route地址(基于ip后面的); 第二个参数是当前urls.py文件环境下能够识别的对象类型,要求是可返回对象类型,比如function_name;

Note:这里有一种官方给出的practice: 对于所有app,app目录下自行创建一个urls.py,创建一个urlpatterns列表并使用path()建立mapping; 然后对所有的apps,在全局config的urls.py中对应的urlpatterns列表中添加:path('app_name/',include("app_name.urls"))来实现一键导入自动匹配.
(当apps数量很多每个app内也存在许多route URLs需求时这无疑很好!)

django.urls.include()的作用是将其他urlpatterns列表挂载到当前urlpatterns列表中,原本的_ResponseType参数的内容;

