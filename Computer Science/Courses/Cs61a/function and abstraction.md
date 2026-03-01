1.Python环境的嵌套与自动识别：  
当前local env-\> 上级parent env -\> global env ; 其中local env指当前代码所在局部空间（比如某个具体函数空间内，则可识别函数内定义临时内容与函数传入参数）； 上级env指函数被调用时，函数所在env;
 
2.高级函数， 指的是操作函数的函数； Python中将函数作为一等公民，意味着函数既可以作为其它函数的传参，也可作为函数返回值（这一点很重要，函数可以返回一个函数而不是传统数据值）
 
基于高级函数的功能，可以通过函数嵌套定义来实现一些额外功能，如：  
def complexity(f):  
def g(x):  
return f(x)  
return g  
上述代码同时结合了1，2的内容：本身com函数接收一个函数类型参数(属于高级函数)，返回一个函数，并且返回函数类型是接收一个单值参数的函数；  
当我们使用高级函数的时候，如：  
g=complexity(math.sqrt) ，将函数名作为参数传给高级函数后，返回一个新的函数，后续可使用：  
g(5) 来得到sqrt(5)的结果；  
上述功能等价于： complexity(math.sqrt)(5)
 
3.curry(柯里化)  
基于上述2中介绍，我们可引出另一个Python技巧：柯里化  
所谓curry指的是，将原本的接收多参数的函数通过高级函数嵌套定义的功能实现单值流水线传递，如：func(a)(b)(c)(d)；  
可以理解为func高级函数提供了更细致的函数更改功能，每次传递一个值都是返回一个由参数确定的，功能更加准确完备的函数；直到最后一个参数（一般是实际数值）传入后才确实地执行最后的函数功能。
 
4.lambda表达式  
本质上是匿名函数，语法内容为： lambda x : f(x)  
解释为：该函数预期接收一个x参数，return value为f(x); 其中f只需要使用lambda时env下可以识别即可。lambda除了简便轻量级函数的使用外，（比如突然想处理一下数据但不想def一个函数太庞大了；比如作为函数的传参，临时起意处理一下数值避免重定义函数）；  
尤其特别的一点，lambda可作为函数返回值，此时由于lambda返回的是一个匿名函数，则此时相当于该返回lambda 的函数等同于一个高级函数并返回一个期待接收参数的新函数(lambda中定义的实际函数体)；  
Notice: lambda返回的内容为一个函数！！！
    
5.装饰器decorator  
与高级函数结合使用，内容如下：  
def My_high_func(f):  
def wrapper(x):  
…  
…  
…  
f(x)  
return…  
return wrapper  
可以看到该函数作为包装函数，当调用：  
wrapper=My_high_func(f)时，返回的wrapper函数为封装了原本f功能的更复杂多功能的函数；  
而decorator的作用与语法：  
@My_high_func  
def f(x):  
….  
等价于 f=My_high_func(f) ，实际上@就是一个利用高级函数返回包装函数的语法糖，后续@指定高级函数后，直接def函数会自动返回经high_func返回的封装后包装函数。  
 
Reacp:  
重要的部分在于：Python语言将function赋予一等公民权限，可作为function's params or return value;￼use function's def nested and function as a return value flexibly：

![Exported image](Exported%20image%2020251007221529-0.png)        

2.Python的传递参数类型： *args 与 **kwargs ：  
Python函数传参顺序也是按照left to right 顺序传递的；  
当某个函数在使用时不能确定其内部具体参数数量与类型的时候(一般涉及到使用高级函数，但高级函数并不清楚调用的函数传参形式如何)，可使用 *args类型参数自动传参。  
所谓自动传参，本质上是Python自左到右将实际调用函数时的多于参数组织成一个元组(*args)，并且根据该元组元素left to right内容与实际函数的参数内容left to right 一一比对自动匹配，故数量与类型仍需要一致否则仍产生传值错误。  
Sample:

![Exported image](Exported%20image%2020251007221532-1.png)  

该高级函数的核心功能是将传入的第一个参数对应函数执行 第二个参数次，并要求返回一个完成该重复执行次数的函数；那么对于内部nested def的函数，我们不知道它究竟接收怎样的参数(包括实际执行的第一个传参函数，也不知道所需参数，此时可使用 *args的方式来 def函数)
 
**kwargs与 *args同理，只不过前者将多余参数打包为一个字典的形式：dict{name:value};故想要正确使用含**kwargs传参的函数，调用时要注意多余参数传入格式为： func(A=a,B=b)的 (key=value)格式
   

![Exported image](Exported%20image%2020251007221535-2.png)



Charpter 1.7:  
The Recursive Leap of faith(递归的信仰之跃)  
要点：  
1.与其考虑最复杂的整体情况，不如从最简单的base case入手考虑，判断此时应该是什么情况，return什么结果；
 
2.不要详细展开每个递归函数的深层递归内容，把它当作一个抽象的功能函数，把注意力集中在它究竟实现什么功能，返回什么结果，从一层的角度去考虑它；
 
3.对于实际的递归函数体，不可避免地涉及到内部调用递归函数时，assume它们一定返回正确的结果；
 
4.对于当层函数，判断利用子层递归函数后，额外的功能是否正确；这既保证了高层最后返回结果正确，也保证了深层次底层子级以及每一级递归函数的返回结果正确。
 ![Exported image](Exported%20image%2020251007221538-3.png)  

注意：  
当递归结构一看看不出来不够清晰时，可尝试通过数学归纳法的方式将表达式写出；一旦能够写出归纳表达式，则可轻易通过递归解决，额外工作就是判断easy case的递归边界条件。
   

HW3最后一道递归题：  
用lambda匿名函数的方式实现递归算阶乘，同时禁止将lambda绑定到指定函数名称；源代码如下：  
注意这里的源代码与lambda calculus严格一致: lambda param.func_body(values_ToChange_param),这种思想也与其一致。
 
    return (lambda g: lambda n: g(g, n)) (  
    lambda f, n: 1 if n == 0 else mul(n, f(f, sub(n, 1)))  
原理：传统递归函数实现的方式是在function_body中实现self-calling，而实现的方式是通过func内部引用自身名称的函数；lambda匿名函数不具备绑定函数名与函数体的功能，但是可通过将递归函数设计为高级函数，将实际函数作为一个函数类型参数传入函数内部，实际功能需求参数额外传入，如此可实现匿名函数内部另类的函数识别与self-calling

![Exported image](Exported%20image%2020251007221544-4.png)