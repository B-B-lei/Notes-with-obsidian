## Introduction

所有的概率分布formula,以及RL中的公式，符号，不要强行去记忆它们的形式，all formulas本质上是对实际问题模型的抽象记录助记；理解问题，清晰RL中各部分关系后，再用简化的formula形式化记录下来。


## 1.环境模型
同时引入环境模型，所谓环境模型是指：Agent在s下采取a动作后，得到的反馈（s',r）是什么样的？我们将与Agent执行动作后交互产生反馈的部分抽象为一个具体的环境模型：
$$p(s',r|s,a)\tag{1.1}$$
通过概率分布来给出环境模型的定义，这也代表：even(s,a)确定的场景下，状态转移本身也并非是硬编码definite的，而是给出一个distribution,这很好地模拟了现实场景，也增强了model 的robust能力。

## 2.Q&V

### 2.1 相同s
Q(s,a)代表处于s state下，采取a action后，一直累积到游戏结束后的，得到的总期望奖励。
V(s)代表处于s state下的得到总期望奖励；
s相同时，V是对于current s下可选取不同actions的加权value，即：
$$V_{\pi}(s)=\sum_{a}\pi(a|s)*Q_{\pi}(s,a)\tag{2.1}$$
### 2.2不同s
存在重要公式：
$$Q_{\pi}(s,a)=\sum_{s',r}p(s',r|s,a)*(r+\Upsilon*V_{\pi}(s'))\tag{2.2}$$
公式解析： 
整个agent决策策略使用$\pi$的情况下，该公式含义为:在当前s状态下，采取动作a后产生的期望目标总奖励，与状态发生转移后的V的关系； 为了模型的robust与泛化性，我们引入的环境变量是一个概率分布，并非s,a能够得到准确的唯一（s',r）形式 (这本质是引入的环境变量的一种特例，即probability distribution是0,1确定性分布，other s'发生的概率都是0)， 故我们需要对all situation进行calculate并进行加权。


## 3.Bellman Equation

上述1,2都是贝尔曼方程的铺垫。
直接给出Bellman Equation的定义：
### 3.1 状态价值Bellman Equation
$$V_{\pi}(s)=\sum_{a}\pi(a|s)*\sum_{s',r}p(s',r|s,a)*(r+\Upsilon*V_{\pi}(s'))\tag{3.1}$$
没错，本质上就是将(2.2)中对于$Q_{\pi}(s,a)$的定义代入(2.1)中，这样得到的Bellman Equation是：
状态价值函数的贝尔曼方程。

因为V本质是期望value(期望都是对发散分支的压缩)，它是基于不同a产生的Q(s,a)发散的压缩；而Q(s,a)因为环境模型的因素也是对转移状态s'(variable,不确定value)下V的期望（压缩）；
整合起来得到：V(s)是关于V(s')状态转移后的状态价值函数的双重期望压缩。

### 3.2 动作价值Bellman Equation

$$Q_{\pi}(s,a)=\sum_{s',r}p(s',r|s,a)*(r+\Upsilon*\sum_{a}\pi(a|s')*Q_{\pi}(s',a))\tag{3.2}$$
将(2.1)中的V定义代入(2.2)式中；

### Summary
不论是动作价值/状态价值，核心的思路都是一样的： 找到相同s下，Q与V的关系（V是关于Q的期望），同时找到状态转移下，Q与V的关系（核心是(2.2) formula,主要在与环境模型的不确定性），最后将两者结合得到关于V/Q的一元状态转移方程（Bellman Equation）

所以，两个Bellman Equation都是，基于Q，V本身关系 + 实际状态转移方程（包含Q,V，是二元的），消元化简后的，一元的状态转移方程（only Q or V）.

