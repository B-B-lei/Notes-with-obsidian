
## 1. Linear Regression

到底什么是Linear Regression?我们build的model真就是简单的:$wx+b$,然后作为真实世界中值域连续庞大的变量空间被如此简单的Formula预测出来?(当然可以是polynomial多项式形式增加复杂度)?
直接精准地取得value不用考虑不确定性(引入probability, 置信区间?)

Bishop的Book给出不同的解析方式:

---
仍然采用Gaussin分布作为基本的模型假设,考虑到Model需要有预测continuous value的能力,建模方式为选取$$N(t | y(w,x),\sigma^2)$$$t表示高斯分布的自变量,y(w,x)对应实际样本数据关于参数w的线性变换,\sigma^2表示Gaussin分布的方差$形成高斯分布Model.
仍然采用max likelihood作为指导思路寻找最优参数($w,\sigma^2$),对概率分布取最大值的过程中,认识到log函数的单调性不会改变最优化问题解,将乘积(联合概率分布=边缘分布乘积,对于独立同分布样本的联合概率分布就是乘积形式)转为简单计算方便的$\sum$形式.
对$w,\sigma^2$分析最优化得到对应value的过程,认识到maximum等价于:
$$min\quad E(w)=min\quad \frac{1}{2}\sum_{i}{[y(w,x_i)-t]^2}$$
上式为熟悉的MSE最小平方和误差函数.
如何解释实际Model进行predict得到对应x下t的过程呢? 不同于之前简单的点估计解释,上式本质上对所有x作为input都生成一个具体的Gaussin分布,在该分布下选取t最直接的方式就是Maximum likelihood: 选取Gaussin distribution的均值作为t的预测结果,则t=y(w,x); 本质上我们背后的指导思想仍为probability theory,没有抛弃概率,不确定性的指导.

---
