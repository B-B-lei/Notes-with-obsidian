



貌似很新很牛逼的python包管理工具? 

## 1. overview
uv init 创建原始的uv管理的project;
会创建一个pyproject.toml文件,该文件是对uv最重要的file uv.lock的"说明蓝图", 实际uv安装具体的
libs都是根据uv.lock安装的;


## 2.uv sync
若git下来的,已有branch/project 中存在uv.lock的内容, 直接uv sync,它会自动build .venv, 并且安装对应的依赖包到venv中;


## 3.uv add
uv add lib_name ,  uv会将record添加到pyproject.toml中,接着联网将依赖包记录的hash写道uv.lock中,最后将该lib的内容解压下载到venv/lib下记录;

Note: pyproject.toml中只记录了env的依赖环境>=...  不是固定的,而uv.lock中指定了具体的lib version(uv最后根据实际选择下载的真是version),并且 uv sync也是使用的该uv.lock中信息来install的.
uv add本质上是: 添加pyproject.toml , 联网搜索找到目标lib后计算hash写到uv.lock, 实际install 到.venv/lib 三位一体的复合command.

## 4.uv run
使用uv一定要 uv run python...   因为uv run会默认先activate env,才继续run后面的python 指令,否则默认是不activate env的;
uv run后,uv 会默认就近寻找.venv,找不到它会先执行uv sync(会根据uv.lock install libs之前build venv).

