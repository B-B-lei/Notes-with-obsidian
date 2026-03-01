## In the beginning
1.本章节回归树部分中提到的 w* 代表的每个node节点的预测值y_hat,是根据Loss函数与该node中yi集合的min解析解得到的;
2.每个tree最后的预测结果是leaves节点的w* 结果,所有的x在该tree中预测结果都是对应leaf node中的w*,实际tree的参数只有leaves nodes中的w* 与中间节点的分裂规则, 中间节点的 w* 并不是真实 存在的,只是用来判断最优分裂规则的中间变量;
3.$f_m(x_i)$代表tree对于xi数据划分到的leave node给出的w* value,可以理解为整个集成trees函数空间中的一个函数(某个tree结构导致的函数),我们可以用$f_m$来定义一棵trees中具体的tree.

## 1.Decision tree

1.长话短说,这个tree是最基本的分类任务tree,首先定义一个能够衡量分类结果的Loss function,
对于一般的分类tree,用cross-entropy就可以了(weighted); 因为能有效衡量你的tree模型预测
的分布与实际data分布之间的差异;
2.注意上述只是设定了对最终的预测leaves nodes的好坏衡量标准,是 leaves的! 
那么我们实际如何得到这些leaves?如何选择每次分裂的好坏标准呢? 此处许多教材都模糊不清,大抵是他们本身就理解不够吧,太过浮躁的学习氛围导致大批人如此学习态度(扯远了);
我们回到tree-build的过程, 实际我们tree-build也是基于cross-entropy的概念,具体一点,每次node的划分是基于information-Gain, 也就是entropy(划分前,weighted_划分后)的差异, 越大说明提纯度越高,我们选择Gain最大的策略进行本轮此划分,
最关键的地方! 这种tree-build的理念是基于贪心算法的思想的!  我们每次divide都奔着能够得到leaves节点收敛的目的去的,每次划分都是基于局部最优(max Gain)的理念, 这种方式往往得不到实际的全局最优,但是往往是一个不错的结果

Note: 不要苦恼非全局最优,这种分类问题都是NP难,以后或许也会解决;

## 2.regression tree

核心思想与decision tree完全一致,包括: 设立最终的Loss, 以及贪心进行每次node divide;
不过回归树的本质是对连续性变量的回归值预测, 核心在"提纯"并非划分, 所以对于如何设计合理的Loss以保证Loss能够准确反映最终每个leaf node的纯度是很重要的, 方差(MSE as loss)就是一种很好的设计, 因为每个node只能给出一种predict_val(没错就一种,这很显然是信息压缩,),为了保证每个node给出的预测值很好的压缩了node内所有信息,需要对如何给出预测值进行分析;
假设预测值为$w^*$,(我们姑且将其看作tree的参数), 往往本次划分的目标函数形式为:
$$minLoss(y_{nodes},w*)$$
这里nodes表示该node内所有样本的集合,他们都有实际的y值,但model给出的预测值只有一个w*,当Loss函数固定形式的时候,我们往往可以求得w*的最优解(解析解,让Loss的关于w*的导数=0),
实际我们build_tree也是将该w*作为node的预测结果;

build-tree的过程就是每次divide遍历所有candidate(col,col_val) col为某个feature, 来判断哪个Loss最小(min Loss),迭代这个过程直到收敛;
最后的回归预测结果也是leaves node的内部解析解w*.

## 3.boosting tree(GBRT,which is :gradient boosting regression tree)

这部分属于集成学习的范畴,核心思想是build 多棵trees,共同进行predict以达到single-tree所达不到的性能;

boosting表示提升的意思,boosting tree的核心理念是将树与树之间建立同步的联系,(trees之间不是独立的);每个tree的 aim 也就是min的Loss并非简单的 Loss(y, f(x)), 这里的f代表当前tree给出预测的结果, 而是将aim 转化为了: $$min Loss(y, F_{m-1}(x)+f_m(x))$$ 
 ;  这里的 $F_{m-1}$代表前m-1棵树各自的predict结果$\sum_{i,i=1}^{m-1}f_{i}(x)$的sum结果;
更形象的说,第m棵树学习的目标是最小化 Loss返回的实际data y 与前m-1棵树的集成预测结果的残差,本质上等价于:
 $$min Loss(y-F_{m-1}(x)+, f(x))$$
这里的y-$F_{m-1}$可以形象的理解为残差;

一般Boosting tree的single estimator都是一棵回归树,每个tree的build 原理基于regression tree,求出解析解; 我们之前忽略的地方在于,当Loss是SSE(均方误差)时,解析解就是均值,很好求,但并非所有Loss都是SSE,当Loss比较复杂时解析解w* 求解十分困难; 求解困难主要在于Loss的形式限制,而GBRT的解决方案在于将Loss转化为更加好求w* 的一个拟合函数, 而它的方案就是G的由来:
求解上一次Loss(F)的负梯度, 新的tree-build求解目标变为求解:
$$min\quad SSE(-Gradient_{m-1},w*)$$
其中SSE是我们熟悉的均方误差,w* 解析解就是均值,好求, 这本质是一种拟合逼近;
原本的 min Loss(y-F , f ) 转为  min SSE(Gradient_Loss(y,F) _ F , f), 

比如Loss为logistic ,w* 解析解没得求,但关于sigmoid的 梯度很好求;
核心在与理解f是F的增量,用 学习率* 梯度 去拟合这个增量,并且外部限制是让这个增量朝着Loss最小的方向变化, 这便是GBRT;   

总结: min Loss过程中,导致 regression tree build时 w* 解析解不好求, 问题转化为求解 Loss(y,F)关于F的梯度 的SSE; 
实际这里推导出梯度的理念并不完善,看后文.


## 4.xgboost

既是实现的lib工具,也是一种基于GBRT算法的优化版本,从原本的梯度延伸到了二阶导(Hessin矩阵);
这里不得不讲梯度下降的原理: 将Loss基于w参数进行Typer展开, 一阶展开的情况下会出现最后一项: 
Grad_Loss_$w_0$ * w, 前面的loss(w)是常数项min过程可以忽略不考虑; 那么如何min Grad_Loss_$w_0$ * w呢? 考虑w是向量,做点积,结果含cos方向影响,显然w沿着 -Grad方向最小! 这就是梯度下降的直观解释!
Note:这里我写的w都是w的变化量的意思,是基于$w_0$进行的变化,
同时引出另一个问题, w的方向确定了,w的数值呢? 很显然所有的ML算法更新都基于一个所谓learning rate, 为什么? 因为我们不知道w多长, 搞一个经验猜测的步长来拟合;一阶泰勒展开只能精确到这里! 

而假设考虑Typer展开到二阶, 会出现:
Grad_Loss_$w_0$ * w+  (1/2! * H_Loss_$w_0$ * $w^2$,这里的H表示二阶导数;仔细分析该函数,对于Loss,我们一般选择关于w可以进行求导的如凸函数, 那么二阶导H一定是>=0的, 此时很直观的二次函数存在最优解析解! 韦达定理可解出, 故得出结论: 
当我们通过二阶泰勒展开时,不仅可以确定w的方向,也能确定w的长度!  
我们将boosting tree的F,f映射到上述Tyler展开原理中, 实际F就是关于Loss损失函数的自变量,f可以看作F自变量的增量,这样一来每次某个具体的tree的损失函数我们可以通过Tyler展开的形式,固定展开项数去简化优化任务.

Note:二阶泰勒展开得到的w方向不是梯度方向, 但w同时具备长度,它提供了优化效率最高的方向与步长.

基于上述GBRT的优化策略,以及本节讲述的二阶导数,xgboost就是基于二阶Tyler展开的优化方法,
每次计算Loss函数关于之前的F积累的 梯度g与二阶海森矩阵h, 基于这些内容可以直接求得fm的解析解w*, 该解析解显然是比GBRT中的求解 min SSE(-G)准确的, 基于该w* ,继续找寻candidate分裂点迭代直到本次收敛;

Note: 
海森矩阵往往由于维度太高计算开销大不被使用,这里xgboost之所以使用是因为:分析 h是关于F的,F是关于data数量的, 而h矩阵中关于不同i,j的二阶导记录, 显然是0的,因为model对于不同的sample预测结果之间没有关系,这样实际h开销是一个对角线非0的矩阵,开销并不大;

