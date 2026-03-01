
## 从坐标系变换的角度看矩阵乘法

对于Ab这样一个:matrix,vector乘法;  我们可将其剖解为: b是基于某个坐标系a下的坐标(该坐标系a中有自己的基向量,b是这些基向量的组合方式);
A就是该a坐标系的各个基向量分量,其中每个基向量是A中的一个column;(mxn  nx1  刚好对上)
但A中的各个column在a下看来就是彼此正交的基向量如[1,0,...,0],[0,1,0,..0]...[0,0,...1];
而当前我们看到的A中每个column是基于我们的坐标系下,看a坐标系的基向量内容;也就是说A矩阵某种意义上就是:在我们当前的观察系中看到的a坐标系形状;
而Ab的结果为: 对于a坐标系下坐标为b的点或向量,在我们当前坐标系下的位置/向量  这就是坐标系变换的角度看matrxi multiple


# From OneNote
1.为何A x B当 A或B为单位矩阵E时，结果不变仍为B/A？  
从坐标系变换的角度分析：  
当A为E时，左乘矩阵可看作N维向量空间的transfer,而右边的B矩阵可看作N个N-D vector集合。基于相当于变化的内容为：当前向量空间中的最基本bases变化，也就是不变换。  
当B为E时，相当于经过A transfer的向量是正交base vector,则变化到A对应的new_bases对应内容，也就是：A，此时transfer之后vector-space中的base就是A中的vector了；
 
Notice: 我们说变换的时候，变换向量都是基于右边矩阵的colomuns划分的vector
    
2.行列式Determinant:  
行列式(N X N matrix)针对某个matrix而言时，代表：以该matrix为参数transform space后，new_space_single_base_tensor/origin_space_single_base_tensor  
何意呢？行列式本质上是衡量向量空间映射后新的基础单元面积与之前面积之间的比值，也就是缩放因子：这里所谓的基础单元，就是N-D base vector张成的 N-D element(比如2-D是面积，3-D是体积)；
   

3.矩阵的列空间与秩：  
假设只考虑N X N矩阵A A x vector的情况下，返回结果也是一个vector; 等价于：A将当前N-D space下的vector通过space map到新空间，等同于另一个vector;  
matrix的colomns代表了新 mapping成的space中能够张成新space的所有"new base vector",故所有这些colomn vectors 能够张成的向量空间，被称为matrix的列向量空间  
对于矩阵的秩：当matrix为N X N时，将其看作space的transform 过程；如果该matrix最后张成的向量空间发生降维。则该矩阵的秩\<N,行列式值为0  
而当matrix满秩，存在逆矩阵时，所谓的逆矩阵就是：将基于A变换后的向量再通过该逆矩阵变换，能够得到origin-space中原本的vector
 
4.线性方程组：  
如果以线性方程组的角度看：  
A x vec1 = vec2  
该范式，其中A是N X N矩阵，vec1,2是1-D 向量，该方程组的几何意义：  
寻找origin-space中的向量vec1，（可能不止一个），该向量经过A mapping到新 space中的坐标等价于origin space中vec2的坐标； 这样一来我们可以根据vec1的可能情况得到一组解向量；




### 1.向量外积

假设$x$为n x 1列向量； $A=xx^T$结果为 n x n矩阵，该运算也成为向量的外积；
矩阵A具有许多特点：
1.从秩的角度看，r(A)=1,因为A中每个列向量都为原先向量x的倍数；
2.从矩阵乘法的外积和角度理解，实际上每个矩阵乘法都可以看作左右两个矩阵分别进行column,row向量的外积并且求和，每个外积矩阵都对最终结果矩阵对应位置元素贡献了部分信息；同时最终的矩阵r 取决于每个新增加的外积矩阵是否贡献了新的信息；
3.从向量空间的角度理解外积矩阵： 仍然是$A=xx^T$，假设A作为某个向量空间变化的transfer系数矩阵， $Ay=x(x^Ty)$,显然()内部的数据是一个标量，也就是说$Ay$的结果向量，y经过A系数矩阵变换后的坐标向量，是一个与x向量平行的向量，A矩阵强行将任意矩阵都投影到了组成自身的$x$向量方向。


### 2.对向量求导

1.核心法则：对于某个返回值为标量的函数，对其关于某个向量参数求关于向量的导数，可以将向量当作该函数所在的坐标系；对向量求导需要对向量中所有分量进行求偏导数并得到最后的偏导数向量，等价于求：该函数在由该向量组成的线性n-d space中求关于该向量的梯度。


2.雅各比矩阵

2.1 微观理解
实际的通用求导手段：对于 mxn 矩阵A,  有关$A*x$的函数对于$x$求导(x为为nx1矩阵)，结果为mx1向量，将结果的每个分量记为：fi(i from1 to m), 那么我们定义有关$A*x$函数对于x的导函数为：

考虑一个将 $n$ 维向量 $\mathbf{x}$ 映射到 $m$ 维向量 $\mathbf{f}$ 的函数 $\mathbf{f}: \mathbb{R}^n \to \mathbb{R}^m$。 输入向量 $\mathbf{x}$ 和输出向量 $\mathbf{f}(\mathbf{x})$ 分量表示如下： $$ \mathbf{x} = \begin{pmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{pmatrix}, \quad \mathbf{f}(\mathbf{x}) = \begin{pmatrix} f_1(\mathbf{x}) \\ f_2(\mathbf{x}) \\ \vdots \\ f_m(\mathbf{x}) \end{pmatrix} $$
函数 $\mathbf{f}$ 对 $\mathbf{x}$ 的导数是一个 $m \times n$ 矩阵，称为雅可比矩阵 $\mathbf{J}$。它由所有一阶偏导数构成。 $$ \mathbf{J} = \frac{\partial \mathbf{f}}{\partial \mathbf{x}} = \begin{pmatrix} \frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} & \cdots & \frac{\partial f_1}{\partial x_n} \\ \frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} & \cdots & \frac{\partial f_2}{\partial x_n} \\ \vdots & \vdots & \ddots & \vdots \\ \frac{\partial f_m}{\partial x_1} & \frac{\partial f_m}{\partial x_2} & \cdots & \frac{\partial f_m}{\partial x_n} \end{pmatrix} $${雅可比矩阵的结构解读} 
矩阵维度为 $m \times n$，其中 $m$ 是{输出维度}， $n$ 是{输入维度}。 矩阵的第 $i$ 行是第 $i$ 个输出分量 $f_i$ 的{梯度} $\nabla f_i$ 的f{转置}： $$ \text{Row}_i = \left( \frac{\partial f_i}{\partial x_1}, \frac{\partial f_i}{\partial x_2}, \dots, \frac{\partial f_i}{\partial x_n} \right) = (\nabla f_i)^T $$ 矩阵元素 $\mathbf{J}_{ij}$ 表示第 $i$ 个输出 $f_i$ 对第 $j$ 个输入 $x_j$ 的变化率： $$ \mathbf{J}_{ij} = \frac{\partial f_i}{\partial x_j} $$ 线性变换示例} 对于线性函数 $\mathbf{f}(\mathbf{x}) = \mathbf{A}\mathbf{x}$，其中 $\mathbf{A} \in \mathbb{R}^{m \times n}$，其雅可比矩阵为常数矩阵 $\mathbf{A}$： $$ \frac{\partial (\mathbf{A}\mathbf{x})}{\partial \mathbf{x}} = \mathbf{A} $$
2.2 宏观理解：
![[Pasted image 20251017141439.png]]
可以从两个角度考虑雅各比矩阵的内容：
从row的角度，雅各比矩阵是函数结果f关于x的每个分量的偏导数集合成的向量/矩阵；
从column的角度，雅各比矩阵是函数结果的每个row分量分别对于$x$向量的梯度的集合成向量/矩阵


2.3
考虑二次型函数 $f(\mathbf{x}) = \mathbf{x}^T \mathbf{A} \mathbf{x}$，其中 $\mathbf{x} \in \mathbb{R}^n$, $\mathbf{A} \in \mathbb{R}^{n \times n}$。

{一般情况 ($\mathbf{A}$ 为任意方阵)}： （注意，这里结果是在机器学习等语义下默认转为了列向量形式，按照雅各比矩阵的思路定义应该是该结果的转置）$$ \frac{\partial (\mathbf{x}^T \mathbf{A} \mathbf{x})}{\partial \mathbf{x}} = (\mathbf{A} + \mathbf{A}^T) \mathbf{x} $$

{特殊情况 ($\mathbf{A}$ 为对称矩阵, $\mathbf{A} = \mathbf{A}^T$)}： $$ \frac{\partial (\mathbf{x}^T \mathbf{A} \mathbf{x})}{\partial \mathbf{x}} = 2 \mathbf{A} \mathbf{x} $$
总结：
雅各比矩阵为求多元输入参数（如向量）函数的导数的广义范式；特别的，当函数的输出值为标量时，雅各比矩阵的表现形式结果经过转置处理后为：与原向量的形状相同的向量，也就
是我们最开始定义的，对向量中每个分量都分别进行求偏导的形式；





# 矩阵求导核心：微分、迹与 Frobenius 内积

## 1. 矩阵求导的本质与惯例

### A. 广义导数：雅可比矩阵

最广义的导数是**雅可比矩阵** $\mathbf{J} = \frac{\partial \mathbf{f}}{\partial \mathbf{x}}$。它适用于**向量到向量**的函数 $\mathbf{f}: \mathbb{R}^n \to \mathbb{R}^m$。

### B. 机器学习惯例（分母布局）

在统计学习和优化中，我们通常将梯度 $\nabla f$ 定义为：
* **列向量**（与输入 $\mathbf{x}$ 同形）。
* **梯度矩阵**（与输入 $\mathbf{X}$ 同形）。
这种惯例是为了方便参数更新：$\mathbf{W}_{\text{new}} = \mathbf{W} - \eta \mathbf{G}$。

## 2. 微分与最佳线性近似

### A. 微分的定义

微分 $\mathrm{d}f$ 是函数 $f(\mathbf{x})$ 变化的**最佳线性近似**。对于标量函数 $f(\mathbf{x})$，其微分是梯度向量 $\nabla f$ 与输入微分 $\mathrm{d}\mathbf{x}$ 的内积：
$$
\mathrm{d}f = \nabla f(\mathbf{x})^T \mathrm{d}\mathbf{x}
$$

### B. 矩阵微分与 Frobenius 内积

对于标量函数 $f(\mathbf{X})$ 对矩阵 $\mathbf{X} \in \mathbb{R}^{m \times n}$ 求导，其微分 $\mathrm{d}f$ 可以推广为梯度矩阵 $\mathbf{G} = \frac{\partial f}{\partial \mathbf{X}}$ 与微分矩阵 $\mathrm{d}\mathbf{X}$ 的 **Frobenius 内积** $\langle \mathbf{G}, \mathrm{d}\mathbf{X} \rangle_F$:
$$
\mathrm{d}f = \langle \frac{\partial f}{\partial \mathbf{X}}, \mathrm{d}\mathbf{X} \rangle_F
$$
这里注意一点，所谓的Frobenius内积，计算方式是：将两个同形的矩阵同方向、顺序展开拉长成两个同形向量做内积；

## 3. Frobenius 内积与矩阵的迹

### A. 矩阵的迹 (Trace)

**迹** $\text{Tr}(\mathbf{A})$ 仅对方阵 $\mathbf{A} \in \mathbb{R}^{n \times n}$ 定义，是其主对角线上元素的和：
$$
\text{Tr}(\mathbf{A}) = \sum_{i=1}^{n} \mathbf{A}_{ii}
$$

**关键性质：循环置换不变性**
$$
\text{Tr}(\mathbf{A}\mathbf{B}\mathbf{C}) = \text{Tr}(\mathbf{B}\mathbf{C}\mathbf{A})
$$

### B. 迹与 Frobenius 内积的关系（核心推导工具）

Frobenius 内积可以用矩阵的迹来表示，是矩阵求导推导中被广泛使用的形式：
$$
\langle \mathbf{A}, \mathbf{B} \rangle_F = \text{Tr}(\mathbf{A}^T \mathbf{B})
$$
#### 注意：
这里之所以存在迹与Frobenius内积的推导关系： 考虑内积，相当于同位同形元素做乘积(因为$df$的每个分量式子为$\frac{\partial{f}}{\partial{x}}*dx$),其中A,B矩阵的每个位置元素与偏导数，微分 元一一对应，也就是：$a_{ij}*b_{ij}$；表现在转置矩阵乘积就是：$a_{ij}*b_[ij]$，结果为$A^TB$矩阵的对角线元素；Tr对这些结果求和，得到的就是Frobenius定义的内积，


## 4. 矩阵求导推导步骤

标量函数 $f(\mathbf{X})$ 的梯度 $\mathbf{G} = \frac{\partial f}{\partial \mathbf{X}}$ 可以通过以下步骤求得：

1.  **计算微分 $\mathrm{d}f$：** 利用微积分链式法则求出 $\mathrm{d}f$ 的表达式。
2.  **转化为迹的形式：** 利用迹的性质（特别是循环不变性），将 $\mathrm{d}f$ 的表达式整理为：
    $$
    \mathrm{d}f = \text{Tr}(\mathbf{G}^T \cdot \mathrm{d}\mathbf{X})
    $$
3.  **提取梯度：** 此时，与 $\mathrm{d}\mathbf{X}$ 进行内积运算的矩阵 $\mathbf{G}$ 即为所求的梯度 $\frac{\partial f}{\partial \mathbf{X}}$。

最后，那么如何求取$G^T$呢？难道拆开运算吗？ 不，我们通过利用矩阵求导的上述规律进行归纳合并，利用微分与上述式子归纳出 df的表达式，让d$X$位于Tr()的右侧，左侧即为关于X矩阵的导数所求。