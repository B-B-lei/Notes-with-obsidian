1.f-string  
对于python输出数据，使用print()的时候可以使用插值方式,使用方式为：print(f"……"),如此f"" 双引号内都为字符串；当涉及到引用的时候，可以在""内使用{variable}引用的方式输出变量内容；￼  
除此之外f-string涉及到许多格式化内容,一般都是{variable:…..},具体链接如下,另外python官方文档对于f-string的格式规定如下图(小语言语法)  
[🚀](https://www.cnblogs.com/zhanggaoxing/p/18897086#%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95) Python f-string 全攻略：从入门到大师，让你的编码效率翻倍！ - 张高兴 - 博客园
   
![Exported image](Exported%20image%2020251007221605-0.png)        

2.Python命名空间：  
命名空间是在不同时刻创建的，且拥有不同的生命周期。内置名称的命名空间是在 Python 解释器启动时创建的，永远不会被删除。模块的全局命名空间在读取模块定义时创建；通常，模块的命名空间也会持续到解释器退出。从脚本文件读取或交互式读取的，由解释器顶层调用执行的语句是 __main__模块调用的一部分，也拥有自己的全局命名空间。内置名称实际上也在模块里，即 builtins 。

\> 来自 \<[https://docs.python.org/zh-cn/3/tutorial/classes.html](https://docs.python.org/zh-cn/3/tutorial/classes.html)\>   
这里的：内置命名空间类比C++中的std内容，比如python内置的函数print,len等都是在这个内置命名空间中的  
模块命名空间当import进入程序时被导入程序，此时模块中非函数内定义的内容就处于该module的全局命名空间；  
Python每个module根据被导入方式(被其它.py import)或执行方式(python -m 直接运行.py脚本)会创建不同的__name__变量，前者=脚本文件名称；后者=__main__； 鉴于这个特点编写Python项目的时候可以将module的core function与test codes放在同一个module.py文件中；测试时执行 if __name__ == __main__:内的test codes即可
   

2.python中逻辑运算符：

and , or , not 并且bool expression也存在短路功能(前面的condition满足后不再判断运行后面的部分


3.nonlocal:
Python语法涉及许多的nested function(函数嵌套)，python函数的可识别变量环境作用域按照：local frame, parent frame, global frame的顺序优先级依次判断，也就是说python的内层函数允许访问外部env定义的变量；但是python默认只允许以读的方式访问外部作用域的变量，一旦尝试在nested function内部修改变量值会导致python创建一个新的local variable并基于该变量操作；
nonlocal 关键字用于声明该变量属于非local变量，并且非global变量，并且允许local 修改该变量的值；


4.mutable与inmutable:
（结合对于nonlocal的理解），
python的assignment(赋值语句，=)本质上可以参考C++的引用/别名 语法，是将一个名称(=左侧)绑定到变量值(=右侧)上;Python数据类型可分为： mutable(可变类型)与inmutable(不可变类型)，当涉及到nested func frame内部对于parent frame中variabel进行assignment时（假如想要修改值），在python中认为是修改binding关系，如果操作针对是mutable变量中的某个sub_data内容，允许操作；对于inmutable或者整个的mutable对象数据类型的 assignment操作，python默认会在local frame中创建一个并且进行assignment,并不会真实影响parent中的变量，此时需要nonlocal关键字来声明该variable 是parent frame中已有的，不要创建新的local variable；

对于mutable数据类型，对于函数中直接传入参数 /  外部变量调用   的情况下，对其内部元素进行=赋值以及数据类型的内置方法（如list.append）是原地操作，会改变变量的实际值；inmutable是创建当前frame中的临时变量进行处理（nonlocal 则是类似传入引用，绑定了外部值可以修改真值）。 


4.iter与yield
(1). iter:
an iterable value is a value can be passsed to iter() function  (for example list,dictionary,...)
an iterator is returned from iter() function and is also a mutable value
iter is  api of using __ iter __ function


Python大量的利用迭代器的概念，比如for item in expression:中，本质上python语法就会检查expression是否是一个可迭代对象，如果是通过__iter__和__next__内置方法来获取expression中的元素并进行迭代； 

何为迭代器？所谓迭代器就是用于迭代的节点，从离散的节点角度考虑整体的过程，类似马尔科夫链状态转移的思想；

有何作用？
yield关键字，节省内存，动态运行function, 不保存之前的迭代器返回值内容，仅仅记录迭代器生成cur_value的逻辑与需要的变量值，并step-by-step的生成数据；
在funciton中定义 yield value来取代return value语句后，首次引用函数会返回一个iterable object 也就是说可以被迭代器函数识别使用（也就是说可以在for item in this-thing，因为for in 本质就是调用__iter__和 __ text __ 内置迭代器函数)；
后续每次函数执行遇到yield语句后会返回一个迭代器，可通过访问迭代器值的方式访问，可通过：
for item in func(params):
     .....
的语法进行函数调用，好处在于：当函数内部运行次数庞大，需要大量的开销，而yield会将函数返回值以迭代器的类型返回，自动丢弃之间的记录仅仅记录每个当前迭代器运行结果和函数算法内容，通过运行效率来换取内存，资源开销。

超出迭代器的范围python抛出：Stopiteration异常


当自定义需要iter的类时，一般可根据需要自行定义__iter__(self):函数,返回结果通过yield关键字修饰，如此由于函数被调用时返回的内容为生成器对象，不需要再显式定义__next__函数(就是执行__iter__函数体的内容)。

