\> [!caution] This page contained a drawing which was not converted.   
 ![Exported image](Exported%20image%2020251007221710-0.png)

该图呈现内容为线性回归函数f(x)=wx+b中，针对w,b这两个模型参数的损失函数图像；函数为二元函数，x,y axis分别为w,b, z axis为J(w,b)，也就是损失函数；所谓损失函数是衡量当前模型参数所构建出的模型与实际效果偏差值的一种量化手段；我们的模型训练目标就是将J(w,b) minimum化，找到使得J最小的w,b点，即为图中的极小值点；

![Exported image](Exported%20image%2020251007221714-1.png) ![Exported image](Exported%20image%2020251007221717-2.png)

梯度下降原理：￼涉及到多元函数 方向导数与梯度相关知识：  
方向导数为数值，表示多元函数某个点沿着某个方向增长时导数值大小(增长速率快慢)；  
当该多元函数在某个点上可微分时，由微分式与方向导数定义式联立分解可证：￼方向导数==梯度 点乘 方向向量  
证明方式如左图
 
由于方向导数在某点梯度的模为常量，方向向量的模为1，故大小取决于两者夹角；二当方向向量与梯度同向取得最大值cos夹角值为1，沿梯度方向增量最大；同理沿梯度反方向cos值为-1下降最快；  
此为梯度下降原理。

一：What is gradient desent's use?

二：Why gradient descent is correct?

三 : How to implement gradient descent?
 
我们以f(x)=wx+b这种线性函数为例；  
为了让model内参数w,b接近真实值，我们通过定义J(w,b)=差方和 的方式来量化与理想值差距； 此时我们的目标转为：寻找函数J的极小值点对应w,b;对于二元函数J(w,b)，当函数处于w0,b0位置时，假设想要其继续减小收敛到极值点，最快的方式是沿着gradJ梯度的反方向变化，即:(w0,b0)-\>  
本质上可以看作两个向量做减法,从而移动点到更正确位置；而这种梯度下降可以更快使w,b 达到收敛点位置；
 
这里注意一个问题：codes implement的时候要保证simultaneous principle：￼temp_w=w-  
temp_b=b-  
w=temp_w; b=temp_b;  
因为本质上向量加减法，如果分开计算(比如w=w-…; b=b-…)违背梯度下降法则。

四 : About multiple linear regression gradient descent:
 
当sample内容为N-dimension类型时，对应linear regression模型的不同参数之间range范围差距过大时，会导致梯度下降算法性能表现不好：  
**1. 参数范围相似（圆形等高线）**

- **梯度方向**：梯度方向总是指向圆心，也就是损失函数的最小值点。
- **训练效果**：梯度下降可以**非常高效地**沿着直达最小值的路径前进，每一步都朝着正确的方向。即使使用相对较大的学习率，算法也能稳定地收敛，因为它不会“越过”最小值点。

**2. 参数范围差距大（狭长椭圆形等高线）**

- **梯度方向**：梯度方向不再指向椭圆的中心。相反，它会**几乎垂直于等高线**。在狭长的椭圆形中，这个方向会指向椭圆的“侧壁”，而不是沿着长轴指向中心。
- **训练效果**：
    - **低效的之字形（Zig-zagging）移动**：梯度下降会沿着狭长方向来回振荡，因为它不断地在陡峭的“侧壁”上修正方向，而沿着平缓的长轴前进的步伐却很小。
    - **难以收敛**：为了防止在陡峭方向上振荡，你可能需要设置一个非常小的学习率。但这又会导致沿着平缓方向的收敛速度变得极其缓慢，使得训练过程非常耗时。
    - **不稳定性**：如果学习率设置得太大，算法可能会在陡峭的轴上直接跳过最小值，甚至发散。
 
此时需要我们进行re-scale操作。

![Exported image](Exported%20image%2020251007221722-3.png)     

注意：我们进行features re-scalling 的根本目的是speed up gradient descent

结合contour图也可看出gradient方向是cost function 变化最快的方向(途中红色箭头方向)

![Exported image](Exported%20image%2020251007221728-4.png)

另外关于学习率alpha也是很重要的；当alpha过小，虽能保证gradient descent正常，但是很慢；当alpha过大，则可能产生