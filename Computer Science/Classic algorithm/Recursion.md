可以参考cs6a1中相关学习记录
[[function and abstraction]]


## Tree Recursion

每次递归函数内部，进一步递归调用自身的次数超过一次，此时递归函数结构会形成树形结构：

1.阶乘递归，本质上每次递归函数只调用了一次自身(类似def multimul(n):  return  n * multimul(n-1)),此时的递归结果为线性结构，并非树形结构。

2,斐波纳契数列，每次递归会调用： return fib(n-1)+fib(n-2); 此时当前递归节点会延伸出两个节点，该结构继续广播会形成树形结构；
   相对的，比如回溯算法中涉及到每个recursion node中的for loop内部的recursion操作，所以也是树形结构；

#### Questions:

