\> [!caution] This page contained a drawing which was not converted.   

正则化的本质是为了避免某些vector\<feature\>中的element过于庞大，引入的惩罚机制；
 
当基础model使用polynomial并且含有许多高次幂项数的时候，模型本身具备十分复杂的特点(可以近似逼近十分复杂的连续函数，根据魏尔斯特拉斯逼近定理)，故可能导致模型产生的形状本身是overfitting的(针对该data set过拟合),但不具备泛型的能力(在新data上predict结果表现不好)；此时通过将这些高次幂项的系数添加惩罚机制（正则化的引入）来避免高次幂项数参数过大从而防止overfitting。
   

对于Linear regression,引入正则化后的new_cost function:

![Exported image](Exported%20image%2020251007221918-0.png)  

添加对系数的额外惩罚项，如此一来计算gradient descent时会额外加速wj参数的reduce 过程；尤其当wj value过大时，penalty力度更大；  
注意到一般的standard regularization仅仅针对w系数，不对b进行操作。
   

对于logistic regression的，引入正则化后的new cost funciton:￼

![Exported image](Exported%20image%2020251007221921-1.png)

注意到额外的regularization新引入的new term与linear regression完全一致，区别仅在于前面的原始cost function部分不同；