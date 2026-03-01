
假设我们已有LLM-core lib, 以lib的形式可以直接调用它;


## Django:

1.通信: DRF(Django REST Framework)

2.database识别与分析: 连接到database, 提取出其中schema;

3.与前端通信,提取出前端的


### 对于database的读取方面设计
1.MVP架构先实现一个:动态读取local已有database的功能;
2.后续升级: user可以自行选择database的读入/
3.更加升级: 支持用户将.csv文件传入,使用对应的database类型,并生成对应的database;


django可以一键识别导出已知db文件的models:
python manage.py inspectdb > your_app_name/models.py
Note: 需要将db文件的path写入settings.py的DATABASES 'NAME'表项记录中

对于sqlite3,可以一键读取其Schema信息,因为sqlite3将该信息作为一个table存储在db中;
而其他type db都没有这种实现,所以需要手动实现data的拼接,暂时还未实现;


## 开发记录:

#### 25-12-6.
1.build Django 项目body
   创建onesql app用来作为specific database的适配

2.models已经基于database构建完毕.

3.services中实现了对于指定db的schema分析功能.

#### 25-12-7.

