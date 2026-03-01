

Median of medinas算法

为什么选择group的大小5(O(n)), 而3退化为(nlogn) ?
选择S_和S+的时候,精度越小当前轮次找到的近似median越接近全局median,导致下一次迭代的median聚类真是median越远,算法退化

