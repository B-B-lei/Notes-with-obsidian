
1.DQN algorithm
利用Deep learning (NN) 模拟Q函数,为什么呢?因为当Q的输入参数如s是continuous变量时(如pos,angle),由于并非像离散变量可以基于查表来创建Q函数了,此时需要用NN来近似表达Q函数的功能
Note: Q是基于Bellman equation的内容,自行查阅

#### 1.自举
没有原始数据中的output label信息,需要random一个NN架构,将input喂给model后,
model产生output,然后组成完整的data后反哺NN进行train 来update W,b in NN;并重复上述流程,
这个过程会使得模型不断收敛到实际优秀的预期强化学习模型; 这便是"自举";

#### 2. $\varepsilon$ greedy
正因为强化深度学习的数据是自举产生的,每次NN的输出结果out layer的units数量是实际场景中的actions种类数量;实际每次纯粹greedy肯定是选择最高评分的action;但是由于random initial的NN,可能导致pure greedy训练结果收敛到局部极值点并非实际结,可以引入每次$\varepsilon$概率选择random action来跳出random initial NN造成的影响;  随着model train的不断深入,可以逐步减小这个概率直到最后使用纯粹的greedy model来进行train;


#### 3.soft update
实际NN内w更新是基于:squad sum以及标准gradient desenct来计算的;而计算方式是:(y_out-y_expected)^2, 这里的expected是Bellman equation中的短期Reward + discount* 长期maxQ(next_state)来得到的,也就是说Q仍然是使用NN内部的w运算得到的;
如果每次train NN的w都变化,那么cost func的 y也在不断立刻变化,训练过程会产生震荡;
所以train过程的w_expected一般独立训练另一个NN(没错两个NN),而w_expected可以采用:$$W=0.95W_{origin}+0.05W_{new}$$
这种保留部分origin W信息的软更新方式,避免过于震荡激烈的硬更新

总结:reinforce learning的算法并非像Machine Learing和 Deep Learning一样成熟,仍处于摸索阶段.