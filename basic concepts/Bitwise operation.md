[toc]





&emsp;
&emsp; 
# 1 移位操作(bit shifts)
##  1.1 什么是移位？
&emsp;&emsp; 移位是一个二元运算符，用来将一个二进制数中的每一位全部都向一个方向移动指定位，**溢出的部分将被舍弃，而空缺的部分填入一定的值**。
&emsp;&emsp; 在类C语言中，左移使用两个小于符号"`<<`"表示，右移使用两个大于符号"`>>`"表示。

## 1.2 有哪些移位操作？
根据移位后空缺的位是补`0`还是补符号位，可以分为 算术移位 和 逻辑移位：
> &emsp;&emsp; **算术移位**时，溢出两端的位都被丢弃。算术左移中，右侧补上`0`；算术右移中，左侧补上符号位（补码中的最高位），以保持原数的符号不变。
> &emsp;&emsp; **逻辑移位**时，移位后空缺的部分全部填`0`。因此，逻辑左移和算术左移完全相同。
> 

## 1.3 c/c++的移位是哪种？
&emsp;&emsp; 对于左移，算术移位和逻辑移位没有区别；
&emsp;&emsp; 对于右移，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移）


