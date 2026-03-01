目前学习到的最难的算法类型，尤其是其中的duality对偶，没学懂；


# 一.网络流

## 1基本性质
首当其冲的是网络流模型；该算法本质上和ML非常相似：当问题太过复杂维度过高，简单的线性分析算法如DP, Divide&Conquer 让人头大的时候，我们可以采取图论的graph build model+algorithm
auto update weights的方式来求解；
而网络流是利用图论来解决LP问题的一种范式；

网络流（也是graph）中重要概念：

1.源点S（只有入度）和汇点T（只有出度），其余网络中inner node都是具有”兜不住尿“的流守恒中间节点； 这里引出流的概念。

2.f流，f可以视作资源的流动，资源在网络中的流向； 每个中间节点因为流守恒的性质，前后入度边的sum(f)和出度边的sum(f)需要相等，这本质上就是LP问题中的一组约束条件。

3.capacity容量，cap是每个edge的性质，可以理解为：整个自来水管体系中，每个管道的材质/粗细不同导致的edge内f上限； 每个edge内实际f的大小可以是Dynamic变化的，这取决与算法如何操作，但无论如何不得>cap.

4.reverse反向边.
我们实际build graph的时候，往往不会将cap作为max constant记录，而是将cap记录为实际的rest_cap(max-current_distributed flow);
对于reverse edge的设计为：方向相反，且cap(rest_cap)与正向的cap互补（sum和为max_cap initial）;
#### Notice! 
reverse设计的原理是整个网络流最核心，最重要的部分！！ 
传统的greedy算法在寻找全局最优flow path的时候，往往会产生局部最优并且导致局部path资源（cap）的占用不可撤销； 为了实现可撤销（参考递归暴力中的回溯算法），我们引入reverse edge;

之所以reverse能够起作用是因为： 
后续实际的算法在选择可行path的时候，都是根据每个edge的 cap>0才能选择的；当algorithm到达正向边的A--->B中B node后，由于我们每次分配flow都会对正向/reverse分别操作，保证两者时刻互补守恒，当算法位于B的pos时，它认为B--->A是一条可行的edge,它会将其作为path的候选edges组成；一旦选择了B---->A,那么A---->B同理，cap又会加回来，由此实现了“撤销”的原理。

5.增广路与残留网络；
所谓残留网络就是每个edges引入了上述reverse 的网络流；
所谓增广路是：找到了one path from S to T,并且path上minimum 的edge的cap d>0,这意味着T接受的flow远没有最大，仍可以通过该path上all edges增加d的 f来增加T受到的流。


6.cost，f是资源的流动，cost则是每个edge的性质，一般衡量edge上资源的单价；当问题复杂时可引入带cost的网络流来解决问题

## 2.问题类型

### 2.1.最大流问题 （它的Duality问题是最小割，强对偶条件下是等价的）

这是最基本，常见的网络流问题。
对于网络流模型，我们通常认为源点S的发射f流的能力是$\infty$的，最大流问题就是：让汇点T受到的sum(f)总和在网络流模型固定的情况下最大；
此时系统限制T的sum(f)的瓶颈实际是graph中每个edge的cap,edges之间的连接方式，是一个全局的情况；

至此，我们很清晰的认识到，最大流问题的本质就是一个LP问题， aim是maximum T的入流量，constraints是网络流中每个edges的 max_cap,要求每个edge的实际f<=max_cap.

最大流问题本质上等价于：网络结构下寻找S->T的不相交paths的最多的数量，因为当固定每个edges的f=1时，T的流量就等价于寻找不相交paths的数量。

EK算法本质上是在：将每个实际的path寻找的过程拆分到了不同的round中进行，每个round寻找其中的一部分edges(添加edge上对应flow,与rev的flow);最后all rounds找到的edges（flows）组合结果就是最多不相交paths数量（最大流）；
EK算法的实践就是：每轮次round从S出发，根据BFS算法遍历图结构一直到T(寻找增广path),并且记录该path的edges;将edges上all edge根据Mini cap更新all edges的 正向/反向 cap(保持互补性质)，持续这个过程直到S出发再也找不到到达T的增广path为止。

#### 总结

那么我们究竟是如何实现“最大流”问题的收敛求解的呢？ 当EK算法在新残留网络上再也找不到可行的增广path时，意味着graph中任意可以让S通往T的path上edge(水管)都被堵满阻塞了，进一步加大源头流量对于T的接受不再会有任何改善了，此时到达了max flow的收敛 condition;

从duality的角度分析，所谓max flow,也是“最小割”的问题： 当原graph中由S->T能够产生割时，只要把两部分的最小割之间paths全部切断，双方再也无法通信了（max flow取决于最关键pipe的 cap）,也就是我们所谓的“bottleneck”, 而残留网络找不到增广path的本质就是：最小割的切断。
#### Note:对于残留网络与反向边对于问题求解的理解仍需要进一步加深。对于duality和最小割也是一窍不通。



### 2.2最小费用最大流（MCMF）

本题类型是与最大流问题高度一致的变体问题（稍微更加复杂了），因为引入了每个edge的新属性：cost; 我们现在需要在：保证最大流的前提下，找到最小cost的方案。
#### Note: 不论是MCMF，还是最大流，求解的问题可不是最佳path,往往求解的问题都是与汇点T相关的multi paths的累积best result.

当认为MCMF的算法模糊时，不妨从 flow的等价问题拆分入手： 没有所谓的流，只有不相交的edges;所谓的最大流就是最大不相交paths数量。

既然如此，我们的目标是：在满足最大不相交paths数量的情况下，尽可能压缩paths的cost.

根据最大流最小割定理（两者等价）：
所谓割是：将graph根据S,T作为两端的任意划分两簇后，簇之间所有相连边；如果将某次划分的割中所有的edges全部断开，两簇无法通信！此时找不到S->T的paths了！

我们根据图的拓扑将S,T任意分成两簇，此时S->T的不相交路径数量的上界（>=实际的最大 不相交paths数量）就是 所有S,T分簇方案中，最小的那一个，也就是所谓的：最大量<===>最小割。
实际上他们是duality的，但满足强对偶性，故两者等价；

我们从：找到最小割后，从割的角度来分析S--->T的连通paths情况：
所有的path S--->T, 因为必须要跨越簇，就必须占用最小割中的one path; 那么我们在MCMF中优先根据最短路径算法找到的S--->T的path, 一定会经过该最小割中的edge;所有的paths都是如此，经过这些最小割中的edges,没找到一个current minimum path就occupy其中一个edge：
![[Pasted image 20260121161723.png]]

当sorted paths选取的结果，导致最小割中all edges都被occupy后，那么结果显而易见，到达了max flow,并且由于我们是根据paths的 cost大小排序的，故必定是minimum cost;

### Notice: 
并非每次while(连通)找到的current minimum path都是实际对flow有贡献的，我们在残余网络中会允许该path被撤销； 之所以如此设计是因为：即使某path是当前minimum 的cost,但它可能会导致： path中其余Edges(非最小割上Edge)被占用后，实际最小割中Edges无法被充分利用了，因为包含该最小割中Edge(s)的path中same edge被occupy,此时无法达到max flow的前提，so 残余网络的update()便是为model提供了 fix的功能。

核心： EK算法的残余网络更新算法本质上是保证最大流的算法，为每次path的选择提供了撤销选项。
