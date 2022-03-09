# 面试题3：数组中重复的数字
TODO: 太乱了，后面回来做
## 1. 可以修改目标数组

## 2. 不能修改目标数组






&emsp;
&emsp; 
# 面试题4：二维数组中的查找
## 1. 题目描述
&emsp;&emsp;  在一个 `n * m` 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。现有矩阵 `matrix` 如下：
```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```
给定 `target = 5`，返回 `true`。
给定 `target = 20`，返回 `false`。

## 2. 解答
### 2.1 解题思路
从右上角开始走，利用这个顺序关系可以在`O(m+n)`的复杂度下解决这个题：
> 如果当前位置元素比target小，则row++
> 如果当前位置元素比target大，则col--
> 如果相等，返回true
> 如果越界了还没找到，说明不存在，返回false
> 

### 2.2 代码实现
```cpp
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if(matrix.empty() || matrix[0].empty()){
            cout << "Input error: empty array." << endl;
            return false;
        }

        int row = 0;
        int col = matrix[0].size() - 1;

        for(; row < matrix.size(); ++row){
            for(; col >= 0 ; --col){
                if(target == matrix[row][col])
                    return true;
                else if(target > matrix[row][col])
                    break;
                else if(target < matrix[row][col])
                    continue;
            }
        }

        return false;
    }
};
```
看了书上的实现后，发现遍历可以通过一个`while`来实现：
```cpp
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if(matrix.empty() || matrix[0].empty()){
            cout << "Input error: empty array." << endl;
            return false;
        }

        int row = 0;
        int col = matrix[0].size() - 1;

        while(row < matrix.size() && col >= 0){
            if(target == matrix[row][col])
                return true;
            else if(target > matrix[row][col])
                ++row;
            else if(target < matrix[row][col])
                --col;           
        }

        return false;
    }
};
```

## 3. 优化
&emsp;&emsp;  结合二分法可以再次降低复杂度。






&emsp;
&emsp; 
# 面试题5：替换空格
## 1. 题目描述
请实现一个函数，把字符串 s 中的每个空格替换成"%20"。示例 1：
```
输入：s = "We are happy."
输出："We%20are%20happy."
```
## 2. 解答
### 2.1 C++解法
这个就没啥难度了，直接用`std::string`很容易就能解决
```cpp
class Solution {
public:
    string replaceSpace(string s) {
        if(s.empty()){
            cout << "Erro: Empty string." << endl;
            return "";
        }

        size_t sp_cnt = count(s.begin(), s.end(), ' ');
        cout << "sp_cnt" << sp_cnt << endl;

        // string ret_str[s.size() + 2*sp_cnt];
        string ret_str;
        for(int i = 0; i < s.size(); ++i){
            if(s[i] == ' '){
                ret_str.push_back('%');
                ret_str.push_back('2');
                ret_str.push_back('0');
            }
            else
                ret_str.push_back(s[i]);
        }
        return ret_str;
    }
};
```
**收获：**
> ① `std::string`似乎不能指定长度。
> ② `std::string`在`push_back()`的时候只能一次插入一个`char`
> 

### 2.2 c语言解法
#### 2.2.3 思路
思路如下：
> ① 先遍历一次目标字符串，计算出空格的个数`n`，然后在原字符串的基础上加上`2n`就是要返回的字符串长度；
> ② 填充新字符串的时候从后往前遍历，这样比较容易实现一点；
> 
```cpp
char* replaceSpace(char* s){
    if(s == NULL){
        printf("Bad input, null pointer.");
        return NULL;
    }

    int len = strlen(s) + 1; // 把 \0 给加上
    int sp_cnt = 0;

    for(int i = 0; i < len; ++i){
        if(s[i] == ' ')
            ++sp_cnt;
    }

    int len_new= len + 2*sp_cnt;
    char *ret_str = malloc(sizeof(char) * len_new);

    for(int i = len-1; i >= 0; --i){
        if(s[i] == ' '){
            ret_str[--len_new] = '0';
            ret_str[--len_new] = '2';
            ret_str[--len_new] = '%';
        }else{
            ret_str[--len_new] = s[i];
        }
    }    
    return ret_str;
}
```
LeetCode和书上不太一样，书上要求的是通过实参传进去的指针返回，而LeetCode是要求通过返回值来返回，因此需要通过`malloc()`来动态分配内存，要不然返回的就是局部变量

**收获：**
> ① `strlen()`返回的长度不包含`\0`；
> ② `malloc()`是按字节分配的，返回的是一个指针，语法如下：`char *ptr = malloc(sizeof(char) * len);`
> 






&emsp;
&emsp; 
# 剑指 Offer 06. 从尾到头打印链表
## 1 题目详情
输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。示例 1：
```
输入：head = [1,3,2]
输出：[2,3,1]
```

## 2 C++实现
### 2.1 解题思路
&emsp;&emsp; 先顺序遍历链表，在遍历过程中将节点的值`push_back`到一个`voctor`中，直到最后一个节点；
&emsp;&emsp; 返回的时候把前面构建的`vector`逆序返回(通过`rbeing()`和`rend()`构建一个新的`vector`)
### 2.2 代码实现
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        vector<int> ret;
        if(head == NULL){
            cout << "Bad Input: null pointer" << endl;
            return ret;
        }
        
        ListNode* ptr = head;
        while(ptr != NULL){
            ret.push_back(ptr->val);
            ptr = ptr->next;
        }
        return vector<int>(ret.rbegin(), ret.rend());
    }
};
```

## 3 c语言实现
### 3.1 解题思路
&emsp;&emsp; 书中用的是`std::stack`实现的，但问题时，既然都用C++标准库了，为什么不直接用`std::vector`。
&emsp;&emsp; 书中还提供了另一种解法：利用函数递归，但存在函数调用栈溢出的问题。






&emsp;
&emsp; 
# 剑指 Offer 07：重建二叉树
## 1. 题目详情
复习了几种遍历再来 TODO:







&emsp;
&emsp; 
# 剑指 Offer 08：
复习了几种遍历再来 TODO:






&emsp;
&emsp; 
# 剑指 Offer 09：用两个栈实现队列
## 1. 题目详情
&emsp;&emsp; 用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 `-1` )
示例 1：
```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```
示例 2：
```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

## 2 C++实现
### 2.1 解题思路
&emsp;&emsp; 


```cpp
class CQueue {
public:
    stack<int> in;
    stack<int> out;
    CQueue() {

    }
    
    void appendTail(int value) {
        in.push(value);
    }
    
    int deleteHead() {
        int ret = -1;
        if(out.empty()){
            while(!in.empty()){
                out.push(in.top());
                in.pop();
            }
        }
        if(out.empty())
            return ret;
        else{
            ret = out.top();
            out.pop();
        }
        return ret;
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
```






&emsp;
&emsp; 
# 剑指 Offer 10- I. 斐波那契数列
## 1. 题目详情
写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：
```
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。
答案需要取模 `1e9+7（1000000007）`，如计算初始结果为：`1000000008`，请返回 `1`。
示例 1：
```
输入：n = 2
输出：1
```
示例 2：
```
输入：n = 5
输出：5
```
提示：
```
0 <= n <= 100
```
## 2. 解答
```cpp
class Solution {
public:
    int fib(int n) {
        if(n < 0){
            cout << "ERROR: Bad input." << endl;
            return -1;
        }

        long long n_2 = 0, n_1 = 1, ret = 0;
        while(n-- > 0){
            n_2 = n_1;
            n_1 = ret;
            ret = (n_1 + n_2) %1000000007;
        }
        return ret;
    }
};
```






&emsp;
&emsp; 
# 剑指 Offer 10- II. 青蛙跳台阶问题
## 1. 题目详情
一只青蛙一次可以跳上`1`级台阶，也可以跳上`2`级台阶。求该青蛙跳上一个 `n` 级的台阶总共有多少种跳法。
答案需要取模 `1e9+7（1000000007）`，如计算初始结果为：`1000000008`，请返回 `1`。
示例 1：
```
输入：n = 2
输出：2
```
示例 2：
```
输入：n = 7
输出：21
```
示例 3：
```
输入：n = 0
输出：1
```
提示：
```
0 <= n <= 100
```
TODO: 书上还有另一个扩展，也看看






&emsp;
&emsp; 
# 剑指 Offer 11. 旋转数组的最小数字
&emsp;&emsp; 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
&emsp;&emsp; 给你一个可能存在 重复 元素值的数组 `numbers` ，它原来是一个升序排列的数组，并按上述情形进行了一次旋转。请返回旋转数组的最小元素。例如，数组 `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一次旋转，该数组的最小值为`1`。  
示例 1：
```
输入：[3,4,5,1,2]
输出：1
```
示例 2：
```
输入：[2,2,2,0,1]
输出：0
```

```cpp
class Solution {
public:
    int minArray(vector<int>& numbers) {
        size_t n = numbers.size();
        if(n == 0){
            cout << "Error: Bad input." << endl;
            return -1;
        }

        if(n <= 1)
            return numbers[n-1];
        int ret = numbers[0];
        int i = 1;
        for(; i < n; ++i){
            if(numbers[i-1]>numbers[i])
                ret = numbers[i];
        }

        return ret;
    }
};
```
