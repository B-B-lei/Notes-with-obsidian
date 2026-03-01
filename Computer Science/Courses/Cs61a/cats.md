
1.计算一个typed字符串与目标字符串之间不同的字符数目，并且.....别的不用管了，这是核心需求；针对这道题我们依托之前的recursion思路来实现完成，也就是：
每层函数都return当前比对的字符串中不同的数目，而返回的值通过问题拆解可以简化为：
if current_char 相同 :
return 1+recursive(strings[1:])
else:
return 0+recursive(strings[1:])
也就是说当前的len=n的字符串比较结果依托于：当前字符的比较与接下来的n-1个字符串比较，而后者我们可以使用递归的方式完成，此为分而治之，寻求问题的子问题



2.针对一个：求解typed与source strings 经过最小edit次数使得edit(typed)=source的函数设计：
本身该问题是一个十分复杂的问题：我每次修改策略是什么？(ADD,REMOVE,SUBSTITUDE),修改策略是一个可选有限项；
而每一次不同的修改策略都从全局上看可能是合法，合理的策略；并且每次策略后的修改结果string也不同，需要使用该不同的string再去与source进行比较(修改后，比较的部位也会发生改变)；
遇到这种乍一看十分复杂很多状态都在变的问题，我们一定要寻找不变量：比如我模拟寻找比较string的时候，采取pos来定位
两个需要比较的string中的位置；除此之外该问题由于情况太多太过复杂，寻常的寻找迭代边界并且进行迭代的算法一定是行不通的（或者太过复杂把脑袋烧坏了）。避免这种种情况我们采取的策略是：穷居所有可能情况，并且这种穷居由于边界条件不明朗不能采用常规的迭代来实现，我们进行递归操作，通过递归的方式进行穷居所有情况，并且返回起其中的最优解；
实现起来可以是两种方式：所有递归函数共享一个全局信息：可以是变量/数据结构；  或者，递归函数本身依赖于自身的递归返回值来判断自身的返回值，这种结构可以绘制出： 树型图来进行分析；  



3.Memorization：
在cats的最后一道题中，提出了这样一个概念：由于之前的递归方法(见2)实际运行起来会产生大量的重复运算(可参考斐波那契数列（Fibonacci sequence）)，又或者当该函数被调用十分频繁的时候，由于每次同样的输入形式都仍然会产生大量的重新冗余计算(可以应用到实际应用场景中)，我们可以采用缓存的方法，将函数的相同输入作为tuple(之所以是元组是因为在python中元组不可更改，可作为Hash mapping的key)作为字典dict的key，将函数输出作为value建立映射记录并存入hashtable中；我们对于需要频繁使用的function都可以维护一张这样的hashtable;
后续：当函数的input parameters元组第一次出现时，我们call function,并且将mapping add to hashtable;当appear again时我们不会重复call function,而是直接去hashtable中数值取出提交给用户；
这种建立hashtable作为Cache的方法，与动态规划有异曲同工之妙，在Python中的实现方式可以通过@Wrapper包装函数来实现，具体步骤为：高级函数expect f作为input,内部def Wrapper()接受该f期待的参数，当外层高级函数env中的Cache miss的时候，才会call func,否则直接return Cache[record];
这个技术称为：Memorization（记忆化）

## References

[[Divide and Conquer]]
[[Recursion]]
[[Dynamic Programming]]

