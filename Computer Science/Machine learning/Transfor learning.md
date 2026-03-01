1.迁移学习的定义：  
当存在两种功能类似的Machine Learning任务时，并且前者已经训练并开源某个model:  
包括神经网络架构，层级，units数目，以及内部units的参数；而后者的任务与前者类似相近时，  
可以考虑基于前者的model进行transfor learing。  
本身train model是一件十分耗时耗数据的事情，更何况涉及到大模型架构，从头开始新训练一个大模型的成本开销极其庞大；并且后者的任务或许也并没有大量的数据集作为支持；  
此时使用前者经过大量海量数据获得的表现较好的Model是一种合理的选择。  
(fine tune,微调)
 
Option1:  
当任务最终返回内容不同时，仅仅简单的将Model的最后Output layer替换为本任务所需要的output layer内容，并重新根据自己的数据集训练最后一层的params,冻结前面所有layers(比如当前任务的数据集不多时，可以考虑)
 
Option2:  
（output肯定要根据自身任务重新考量设计）将Model的所有params作为train_model过程的initialize params,重新根据自身data_set train model。
 
无论是op1还是op2,都需要满足：fine tune的任务与已有模型实现的功能相近类似（比如两者都是图像分类，音频识别……）;  
reason1: 若需要借用已有model的架构与params,必须保证neural network的input shape一致，否则无法 forward prop与back prop;  
reason2: 我们认为nerual network中前面层的功能是识别掌握一些任务最底层具有共性的特点(genric features),后面的layers是识别具体任务中的具体特征；故需要基本的任务底层逻辑一致（比如同是图像识别，network的前Layers可能学习到line,corner, sub_shape等基层特征，与后续新的如letters recognize具备共性）
 
2.data augmentation:  
我们可以针对某种sample的内容进行distort or put_noise_in, 并且flag一致以增加某种category的样本数量