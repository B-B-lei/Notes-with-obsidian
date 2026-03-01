
### 本篇简单记录LaTex优雅地输入数学公式的语法

注：本篇直接通过LaTex编辑数学符号，展示在编号下一行，请注意查收
注：LaTex中进行数据符合，统一作为参数处理使用{}符号；当实际数学公式需要使用
{}时需要使用\转义符 ： \{     \}
注：一旦使用了数学公式转义后，内部所有文本内容都会编成斜体并且不识别空格，通过\text{}可以将
斜体文本转为正常文本内容；


Supplement:
\tag{m.n} 与"$ $    $ $"formula一同使用，给公式标记number
$$\tag{1.1}$$

#### 一些基础符号的应用：

1.数学公式通过"$ $"实现行内的编辑，实际数学公式在美元符号之间编写；
   而"$ $     $ $"实现行间公式，会另其一行居中排布。
   $$y=a\cdot x+b$$


2.拉丁字母、阿拉伯数字和 +-*/= 运算符均可以直接输入获得;
   空格需要使用\quad或\qquad实现；
$x\quad y$
$x\qquad y$




3.点乘 : \cdot  (注意t后面有个空格,所有运算符后面基本都需要空格)
$\cdot$

4.上标: ^  ; 下标: _ 
$x_i$
$x_{ij}$     
$x^e$
$x^{-e^2}$
注：当上下标为复合内容时使用{}包裹


4.根号: \sqrt{}
$\sqrt{x^2}==x$


5.分式: \frac{numerator}{denominator}
$\frac{2}{4}==\frac{1}{2}$


6.不等号: \neq  ; 恒等号:\equiv
$x\neq x+1$
$x\equiv y$


7.取模: \bmod
$4\bmod 2\equiv 0$


8.上下行标记: \overline  ; \underline
$\underline{“这句话划重点!”}$

$\overline{"不知道这个上划线有什么用"}$


9.上方大括号与下方大括号： \overbrace, \underbrace ; 与上述上下标复合使用,如：
\overbrace{主体内容}^{大括号上方注释内容}.
$\overbrace{1+2+\cdots+n}^{\frac{n\cdot(1+n)}{2}}$


#### 敲黑板，重点的来了：

1.向量 \vec ;   右侧箭头 \overrightarrow   左侧箭头 \overleftarrow。
$\vec{x}$
$\overrightarrow{x\cdot y}$


2.无穷\infty
$\infty$


3.箭头\to  ;积分\int   ;极限\lim(此为直方体，原本$ $都为斜着的)  ;求和\sum   ;连乘\prod
注：此处特殊运算都需要与上述上下标复合使用！
$\lim_{b\to\infty}\quad  \int_{0}^{b}f(x)+b\quad\mathrm{d}x$       (这里d也使用了 \mathrm{d}的处理，和lim一样转为直体)

$\sum_{i=0}^{n-1}n\cdot (n-1)$

$\prod_1^{n}=n!$


4.样本均值\hat，期望\bar，和一个波浪\tilde：
$\hat{x}$
$\bar{x}$
$\tilde{x}$


5.三圆点 :   
下圆点\ldots:  $1,2,\ldots ,n$   (小写的L);
中圆点\cdots:  $1+2+\cdots +n$
垂直圆点\vdots:  $start\quad\vdots\quad end$
斜圆点\ddots:  $\ddots$
注： $\underbrace{\ddots \text{and} \vdots}_\text{useful in matrix}$



5.矩阵:（）类型矩阵为pmatrix;  []类型矩阵为bmatrix
矩阵定义简单范式：
```text
$$\begin{bmatrix}
1 & 2 & \cdots \\
67 & 95 & \cdots \\
\vdots  & \vdots & \ddots \\
\end{bmatrix}$$
```
其中begin{}  end{}构成矩阵的首位；中间部分&用于切换列；\\用于强制换行，下面为演示样例:
$$\begin{bmatrix}
1 & 2 & \cdots \\
67 & 95 & \cdots \\
\vdots  & \vdots & \ddots \\
\end{bmatrix}$$



6.希腊字母表，不多BB上图：
![[LaTex-Greek-Alphabet.png]]
$\alpha$
$\beta$


心得：使用LaTex编辑数学公式的时候，可以清楚的感觉到：LaTex中的运算符随相对独立，但是具体的复合内容使用方式与程序设计语法的函数调用类似，整个需要被处理编辑的整体作为参数传入函数，通过function{parameter}的方式完成调用；并且parameter允许是另一个保护function{}处理的复合类型，也就是说允许嵌套；


### References:
Online:https://zhuanlan.zhihu.com/p/110756681