
Django整个架构都是Django内部的模块调用开发者编写的app模块进行功能实现;
Django实际采用的方法是根据mysite(类似global conifg的.py文件,把所有需要使用的app都进行了APP_INSTALL的注册).

而urlpatterns也是一个全局的框架用数据结构,所有实际项目的URL路由到最终的执行函数路径都是基于该Django的global config的内容去实现完成的;

当项目很大存在许多app时,全部一个一个写入全局urls.py中太混乱, Django推荐方式是:
将每个app各自的需要进行route的urls操作写道app自身的urls.py文件下,也用urlpatterns的方式实际绑定, 注意这里的列表元素调用path()函数指定第一个参数时, 只需要基于: app_name/之后的相对路径名称指定即可;  
当每个app下都如此创建自己的local urlpatterns后, 回到mysite下的全局urls.py中, 在全局urlpatterns中使用path绑定对应的app urls集体; 方式也是调用path,第一个参数是app_name/ ,第二个参数并非指定具体的callable func_name了,而是通过"app_name.urls"的方式来指定,这里Django能够智能识别是因为之前的settings.py中需要填充INSTALLED_APP字段;

对于path()的第三个参数,name,用来为Django内部做别名快速访问使用的, 它指代该urlpatterns列表中对应urlpattern条目(由path()返回的)中的第一个参数路由地址项; 后续在python内部使用url地址作为变量时,可以直接使用该name来代指; 这是一种软编码的方式;


