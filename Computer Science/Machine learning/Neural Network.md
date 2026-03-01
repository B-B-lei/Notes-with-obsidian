

1.Activate function:

![Exported image](Exported%20image%2020251007221849-0.png)
 
三种activation： sigmoid function/ linear function/ ReLU function  
Notice: Don't use Linear Regression as activation funcs in all hidden layers  
假如：hidden layers units 全用linear, 前面所有的内容全部变为线性多项式；  
最后结果： a[n-1]=X matmul W + …..+ …..  
无论最后一层output用什么activation,结果都是： a[n]=activation(W*a[n-1]+b),而a[n-1]关于x是线性关系，退化成只能实现最后output的activaiton功能；
 
Andrew Ng 推荐使用的activation: ReLU，为何选择ReLU:

![Exported image](Exported%20image%2020251007221855-1.png)  

该示例为某一层的3个units经过ReLU activation后简单的：U0+U1+U2+0的线性组合结果，其中x是该layer input, z=wx+b是每个unit未ReLU前，a是unitsReLU后。
 
如果该layer所有units的activation都是linear 模型，那么最后的结果也只是合并同系数项后的简单关于x的线性图形；而ReLU赋予了每个unit shutdown 对于某个range的影响 的功能：如Unit0中x\>1后该Unit输出内容不再影响最终拟合结果；  
而显然每个unit可以根据自身内部w参数代表的斜率影响自身需要起作用range内的结果斜率；b可以通过上下平移并利用ReLU来影响自身究竟在那一段range内激活起作用；
 
Recap:  
通过不断地训练迭代每个unit中w,b的值可以有效使得当前layer具备拟合某种离散折线关系的能力，具备了简单的linear不具备的复杂功能，同时也避免了sigmoid函数的计算复杂与梯度下降收敛过慢问题(这里有关sigmoid缺点，还不太清楚；)  
以及，最后这只是1 layer的对于平直内容的拟合更改能力；当经过多层Neural Network反复激活后可以拟合十分平滑的曲线！
   

Softmax 函数：  
类似sigmoid()函数，同样处理分类任务，不同之处在于softmax用于处理多分类情况：

![Exported image](Exported%20image%2020251007221901-2.png)

If in Neural Network, 最后一层output units数量==categories，并且activation函数使用softmax(),函数内容如上图，结果作用能够将vector_Z(Z=Wi dot a[i-1] + bi)进行归一化，使得sum(a)==1。
    ![Exported image](Exported%20image%2020251007221904-3.png)  

上图内容为类比logistic regression的softmax cost function定义；注意到实际计算的时候(监督学习下，已知当前sample中y情况)，只取实际y对应的那一项，归纳统计后如下式：
 ![Exported image](Exported%20image%2020251007221908-4.png)