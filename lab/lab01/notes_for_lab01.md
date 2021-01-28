### notes for short-circuit
[课程链接](https://www-inst.eecs.berkeley.edu/~cs61a/fa20/lab/lab01/)

1. True or False 的判定
2. and or 的使用
> 具体请看`short-circuit.py`文件


### notes for debug
[课程链接](https://www-inst.eecs.berkeley.edu/~cs61a/fa20/articles/debugging.html)

1. 学会查看具体的报错语句
2. 在函数中插入doctest asserting的正确姿势
3. print statement 就是用来判断返回的值是否是我们希望的值，来确保调用的这个函数没有问题（起码在这个值上没有问题）
4. release版本的代码最好不要出现`print`，但`assert`可以一直存在。
   > One major benefit of assert statements is that they are more than a debugging tool, you can leave them in code permanently. **A key principle in software development is that it is generally better for code to crash than produce an incorrect result**, and having asserts in your code makes it far more likely that your code will crash if it has a bug in it
5. 接上，**crash比起输出错的结果更better**（这点是我没想到的，但仔细想想也可以理解，毕竟这是错误的code，如果输出错误，不一定能发现，但crash肯定能发现）