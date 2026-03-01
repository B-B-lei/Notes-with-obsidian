
### 1.数据集划分

Train_data/Cross_validation_data/Test_data  
由于模型总是在train_data上train,最后如果通过train_data来evaluate model的final性能总是有失偏颇的  
由于我们想要 point out model在generalization data上的性能表现，故需要model run在其从未接触过的data上，故可将整个data set divided to Train/Test  
Train用来fit模型， Test用来evaluate模型性能表现；
 
而即使选择某个模型的训练方式，model由于不同超参数或比如polynomial的 dimension建模，导致我们必须进行model choose时，我们又需要选择最合适的一个model combine;此时如果仅仅根据Test_data分别evaluate 不同choices,最后选出高scores的model方法也有失偏颇：总是返回更乐观的情况，实际generalization situation会更糟；  
此时我们需要调整评估策略：data_set分成三部分；其中train仍用来fit model,之后在cross_validation 数据集(验证集)上对不同choices model grades,选出我们认为最优秀的model(因为我们将validation_data当作generalization data)；最后，如果我们想要直观地反馈一个我们的工作成果：我们认为最合适的model的性能，同理需要在它未曾接触过的data上run 一个scores出来，则我们通过validation中的best_scores model run in test data，从而得到最后的模型性能指标结果
   

2.  
注意到一点：当使用model的参数不同features之间range相差极大，需要处理range如：z-score等方法时，一般首先根据train_data的内容调用一个专门standard_deal 的model, 后续记得不论是在validation上还是test data set上也需要standard_deal data的时候，需要使用与train_data的相同参数来标准化（让model predict前对于数据处理的参数一致），方法就是通过同样的model对其它data进行fit。
    
3.Bias(underfitting) and variance(overfitting)。

![Exported image](Exported%20image%2020251007221817-0.png)

Bias过大一般指cost_function J数值过大的情况(模型过于简单，欠拟合)； variance过大一般指cost_in_train 与cost_in_validation 差距过大情况（模型过于复杂，导致学习到了专属于data set的噪声，导致在新数据validation上表现不好，过拟合）
   

4.Learning Curve  
Learning Curve一般由两条二维曲线构成，horizontal axis为数据集规模m; vertical axis为cost_function(error value);一般绘制关于m的，J_train曲线与J_validation(交叉验证)曲线；  
对于J_train与J_validation两个值来说，J_train\>J_validation是常态并复合逻辑；(data经过train_data训练，并且未见过validation_data)；  
对于model train process，train用数据规模越大，不论模型如何，拟合略来略庞大数量的点都会变得愈发困难（假设非overfitting的情况），故J_train会逐渐升高并趋于flat平缓(因为J_cost本身是取平均的值，到达某个point后即使数量更大也不会产生太大变化)，而J_validation的值会随着m增大而下降（what we expected）最后也逐渐趋于flat。

![Exported image](Exported%20image%2020251007221826-1.png)

当模型算法本身的选择存在问题，导致较大bias时，盲目增加m是无效的（见上图）；  
Recap:  
Learning curve能够有效反馈当前模型算法性能效果。  
(1).当J_train与J_cv随着m增大逐渐收敛flat但两者差距很大，则代表variance很大，并且若此时J_train很小与J_cv相差很大，则说明model过拟合；  
(2).J_train与阈值shreshold相差较大，则说明模型欠拟合，此时需要增加模型复杂度；  
(3).当J_train与J_cv随着m增大逐渐收敛接近并且都很小，则此时model是我们理想中的模型；  
基于learning_curve,我们可以train model的时候采取分subset的策略逐渐测试并动态分析curve;当遇到如(2)情况说明模型不好可以及时终止无用训练更新模型算法；当两者variance相差很大但J_train小于shreshold时，可以尝试继续增大m规模
    
5.Steps we can take to fix bias/variance problems:
 
If bias too much:  
#it means model too easy even performance in train_data is not good  
1.增加模型复杂度：如增加模型features dimension or Polynomial expand.  
2.decrease lambda(param in Regularizaition),让model train params的过程更加专注1聚焦于Cost_function而非Regularization,从而有效减小 Bias(Bias就是shreshold J与train_J的差值)；
 
If variance too much:  
#it means model too complex so that overfitting Train_data even with noise  
1.减小模型复杂度，如筛选features to decrease dimensions  
2.increase lambda , 增加对model params的惩罚力度，避免params过大；  
3.increase data numbers; overfitting仅仅针对该小规模数量级，增大samples数量能有效冲刷overfitting效果
   

6.Precision/Recall(准确率/召回率) 与F1分数  
6.1 P and R  
之前学习周志华《机器学习》时，遇到准确率与召回率一直没法很好的理解并记忆这两个参数的含义意义是什么，以下这张图很好地进行了说明：
 ![Exported image](Exported%20image%2020251007221828-2.png)  

譬如对于binary 分类来说，1/0的ratio并不一直都是均匀分布5:5的，比如patient病例检测，假如涉及到稀有病例，如ratio=0.05的samples，哪怕实际的model并没有用，一个patinet都检测不出来，那么此时计算model的普通准确率也是约为99.5%；  
看似很好，但这数据并没有意义，我们需要其他的手段去衡量模型的性能，如上图介绍的参数：precision/recall  
这两个参数都是基于model性能计算出的参数。其中precision表示model预测情况本身的准确性；recal表示模型能够预测成功多少个原始samples中数量。  
途中预测失误的情况按照正确/全部的比率是:85%,但是实际的P与R只有0.75,0.6。实际效果并不理想。
   

6.2 AUC曲线

![Exported image](Exported%20image%2020251007221831-3.png)

上图内容为《机器学习》中的ROC曲线  
注： Precision/P也叫查准率；Recall/R也叫查全率；  
理想情况下我们期望model的P，R越高越好，但实际应用绝无可能，并且P,R实际上并不能兼得：当我们针对模型的输出结果（比如经过sigmoid activation）设置threshold后，我们便可以统计出P,R参数。  
当某些任务我们需要保证model预测结果的准确率P时(比如某些重大稀少疾病，cure cost庞大不能随便断言，务必准确的时候才下定义)，此时我们倾向于将shreshold定的很高只将probability很高的内容分类为1，则此时必然得到很高的P，但我们也一定会遗漏一些实际的case导致Recall下降；  
反之对于普通的流行性传染病，我们可以将shreshold降低，宁可预防治疗不可泛滥，此时Recall会上升反而Precision准度会下降；故我们可以在train完model后，通过不同threshold的设置绘制一张P/R曲线，即为上图ROC曲线，来分析我们最终该如何确定shreshold(究竟如何选择取决于最后model服务于的任务性质)。
 
6.3 F1分数

![Exported image](Exported%20image%2020251007221834-4.png)

当模型数量很多需要我们均衡分析性能时，由于P,R是两个参数值，并且大小是相对的，我们很难直观地分析出谁更好，此时将P,R结合产生的调和平均参数F1 score便起到了作用； F1 score将P,R取倒数后放在分母的位置，此时若存在P,R很小的情况（P,R任一个值很小，该model实际上都没什么使用意义），会导致F1的值变得很小；故F1值越大证明模型性能越好；本质上F1 score是一个math中的调和平均值(Harmonic mean), 所谓调和平均更加重视小值，这里F1 score对于小P,R十分敏感。故上图中我们可以人认为Algorithm 1 性能最出众。