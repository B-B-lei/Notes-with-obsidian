1.Decision Node choose  
决策树基于Decision Node节点将data不停地分成sub_set,最终最后deep层的leaf Node停止  
返回最终的分类情况； 具体每个Node节点的选取可以根据data的某个feature的不同  
种类进行区分，如：下图中有Ear shape作为feature,且种类为binary(pointy/Floppy),则可  
尝试将该feature作为Node节点来divide data_set。  
至于存在如此多的features，如何选择当前situation下最合适的作为Node呢？  
我们期望每次divide都能尽可能做到purity, decrease impurity:  
所谓purity指的就是尽可能将一种类别全部放到divide后的一端:left or right  
这样可以认为是有效的decision node,假如一次divide能够正好binary divide，perfect!  
于是引出了新的问题，我们如何评估每个divide是否purity?如何选择最符合purity principle的feature作为decision node呢？ 于是引出下文变量entropy
 
1.1entropy  
这里结合信息论中的“熵”entropy 分析；假设samples取1的情况概率为p1（根据当前data样本数量统计出来的），则计算当前样本entropy的公式：￼ - p1* log2(p1) - (1-p1) * log2(1-p1);

![Exported image](Exported%20image%2020251007221655-0.png)  

如此可统计出某个samples中有关samples的entropy。当根据某种feature进行划分后，可针对指定的sub_set基于上式进行分析计算，注意该值越大证明数据越混乱,杂乱无章，也就是对应我们的impurity;故我们期望的decision node结果应该是 划分结果的entropy越小越好；
 
1.2 weight_entropy  
另外，应该注意到每次decision node会将原本的data divide into 2 sub_set，故需要综合分析考虑两部分set的entropy; 显然当一个data_set规模很大，另一个很小，但两者的entropy相同（假如都很大）时对于数据分布的情况是截然不同的：前者说明数据整体十分混乱无章，分类情况不好；后者或许对全局没有太大影响。  
基于此我们需要对每次decision的结果进行weight计算(H(p1)为基于p1计算的entropy)
 
H=weight_left *H(p1_in_left) + weight_right * H(p1_in_right)
 
其中weight表示权重，实际为：left_nodes/total_nodes or right/total 来计算；  
P1_in_l/r 表示divide后的 y=1在left/right_sub_set中的占比。  
基于此，我们可以计算decision_make前的entropy与decision_make后的weight_entropy，进行分析比对，当entropy减小，证明此次划分有效，我们称减小值为:  
Information gain(decision tree训练出了有效的information);  
最后，可以根据不同feature下的information gain排序来判断当前Node如何选取，比如下图情况中，显然Ear shape是最好的选择。
   
![Exported image](Exported%20image%2020251007221658-1.png)