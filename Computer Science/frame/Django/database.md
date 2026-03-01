1.connection
在settings.py中添加DATABASE的记录,可以连接到已有database/新database;

2.Default and ORM
Django默认使用models.py中class(Model)的ORM方法是基于'default'的配置in DATABASE;

3.multi dbs & routers.py
所以当project需要连接多个db(比如多个apps不同的db),此时需要设计routers.py的内容进行ORM操作的db routers分发(一般ORM都是在views.py中被调用的);

routers.py的定义位置不固定,既可以app/下也可以global(与settings.py同dir下); 需要在:DATABASE后添加DATABASE_ROUTERS的记录,其中以 python package.modules.Class的方式定义routers.py的位置(最后.Class);

最后routers.py中定义实际Router类,其中固定四个functions ,并且输入参数也固定, 包括了所有ORM相关的database CRUD, 关系操作与shape操作(migrate); 
在每个func的body中,利用if else指明每个参数(比如CRUD是model._meta.app_label,这个参数是django内部偷偷设置的,就是models所属app的name) 情况下return 的db name;

