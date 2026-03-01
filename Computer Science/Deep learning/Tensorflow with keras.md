
## basic progress in tensorflow :


#### 1.build model——>train model——>predict result
#### 下面介绍从构建模型架构的范式：

1最基础的通过Sequential提供的方式构建Dense list:
Dense(units= ,activation='')的方式来构建每个layer,每个layer(Dense)之间通过,分隔，最后一层output也是通过Dense的方式创建Layer,注意output输出内容为该NN的结果，即针对某个任务的评分之类

2.将input_data的形状作为上述架构层的输入，这主要是为了固定Layer1的units中dimension,之后的Layers中units dimension都与pre_layer中units数量一致，但是Layer1取决于input数据的维度；

3.完成NN构建后，需要将NN绑定到model以使用model的方法；需要确定model的损失函数cost_fucntion与优化算法（比如gradient descent以及其优化变种Adam），这个环节通过model.compile()实现， cost与优化算法都作为params传入；

4.将处理过的dataset 作为params传入model.fit()进行实际model train,这里一般会先对data进行 train, test/cross_validation 划分；

5.想要进行model 的推理通过model.predict()实现；