\> [!caution] This page contained a drawing which was not converted.   

逻辑回归模型本质上用于实现classification/categories  
的分类任务（分类与回归不同之处在于分类结果finite,后者结果infinite）;
   

Logistic regression实现的原理利用了regression的思路，不断寻找一个能够将N-DSpace 分割为两个独立sub-space的 超平面，尽可能构建模型去拟合该超平面；  
实际并非建模的参数组成超平面(不像linear regression那么直观);  
Logistic regression经过linear regression model之后将结果作为sigmoid()函数的输入；  
该函数的作用是将负无穷到0之间结果mapping到 \<0.5, 0到正无穷mapping到\>0.5,最后实现预测结果为正的概率的转化。
 
另外对于loss function（有别于cost function,后者量化整个样本空间中当前model对于理想结果的偏差；前者量化某个single sample对于理想结果的偏差），logistic regression由于sigmoid的存在，再使用squad deviation作为cost function，结果不好，因为本身sigmoid产生的图形就是一个S型曲线（假如低维），而经过平方后的图形形状极为复杂存在许多极小值点(说是Non-convex function)，梯度下降无法很好的工作,inefficient。￼故，loss function重新设计:

![Exported image](Exported%20image%2020251007221747-0.png)  

针对label为0，1设计不同的loss版本（本质上就是log内部互补了一下），最后实现的效果为：当预测结果正确或接近正确值时(即为f输出值接近实际y_label的0/1)，此时loss func的值非常的小接近0；  
反之假如prediction的value与 y_label不同，(差距很大可以说预测错了)，此时 penalty力度非常的大(-log(x)x接近0的时候结果都能接近无穷了)，很好的起到了量化 实际prediction与 ideal label之间差距的效果；

Note! :

![Exported image](Exported%20image%2020251007221750-1.png)  

Logistic regression与linear regression进行gradient descent的时候，求cost_function的偏导数部分，以及最后迭代公式形式完全一致，  
区别在于上述的fw,b(xi)不同； logistic的f本质上是sigmoid函数，后者是wx+b。￼形式一致的原因与求导链式法则相关(已证,注意原本的loss函数中log()相关内容经过偏导数链式求导法则，最后的结果也就是上述f(x)中的f是sigmoid()形式，原本的log已经消掉)
   
![Exported image](Exported%20image%2020251007221752-2.png)

由于分界线为：probability=0.5(sigmoid(wx+b)=0.5)故，wx+b=0即为所求分界线(或超平面)  
推广到vectorization features situation, 当 X dimension equals N, means 我们需要在N维空间中找到超平面(或超曲面）使得N-D space划分为两个sub-space; ￼任务具体到Math_function就是寻找一个F(x1,x2,…,xn)函数，该函数作为sigmoid()的输入； 而F(X)==0的投影到N-D space就是我们预期的超平面；(sigmoid(F(X)))==0.5;
   

cost function :

![Exported image](Exported%20image%2020251007221755-3.png)   
==该====loss function====本质上将====sigmoid====函数的数值进行====log====处理后，能够有效的惩罚预测出错的情况；而针对不同====y====实际值的情况取值的====loss====表达式为了最后获得：==  
==预测出错结果====cost J====数值为正且尽可能大，====y=1/0====时的== ==expression====并不同，故其中结合====y====的值====({0,1})====归纳成一个式子即为上述== ==loss function==

![Exported image](Exported%20image%2020251007221758-4.png)