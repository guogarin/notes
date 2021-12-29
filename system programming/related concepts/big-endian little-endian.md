# 介绍
## 1.1 定义
 **大端字节序** 是指一个整数的高位字节（23～31 bit） 存储在内存的低地址处， 低位字节（0～7 bit） 存储在内存的高地址处。 
 **小端字节序** 则是指整数的高位字节存储在内存的高地址处，而低位字节则存储在内存的低地址处。 
## 1.2 如何记忆？
大端字节序 又称为高位优先(big-endian)，可以理解为优先存储高位的字节，而存放又肯定是从 低->高 开始放，因此是高位字节放在低地址；
小端字节序 又称为低位优先(little-endian)，可以理解为优先存储低位的字节，而存放又肯定是从 低->高 开始放，因此是低位字节放在低地址；






&emsp;
&emsp;
&emsp; 
# 2. 如何写程序区分？
```cpp
#include＜stdio.h＞

void byteorder()
{
    union{
        short value;
        char union_bytes[sizeof(short)];
    }test;
    test.value=0x0102;
    if((test.union_bytes[0]==1) && (test.union_bytes[1]==2)){
        printf("big endian\n");
    }else if((test.union_bytes[0]==2) && (test.union_bytes[1]==1)){
        printf("little endian\n");
    }else{
        printf("unknown...\n");
    }
}
```
**原理：**
(1) `short`类型为2字节，
(2) `0x0102` 第一个字节为1，第二个字节为2
(3) `union`是一个联合体，所有变量公用一块内存，只是在不同的时候解释不同。其在内存中存储是按最长的那个变量所需要的位数来开辟内存的。
(4) 将`value`置为 `0x0102`，则：   
> * 若 `test.union_bytes[0]==1` 则为大端(因为01是高地址，02是低地址)；
> * 若 `test.union_bytes[0]==2` 则为小端；
> 






&emsp;
&emsp;
&emsp; 
# 3. 现代PC采用哪种字节序比较多？
&emsp;&emsp; 现代PC大多采用小端字节序， 因此小端字节序又被称为**主机字节序**。






&emsp;
&emsp;
&emsp; 
# 4. 网络通信中的字节序问题？
## 4.1 何为网络字节序？
&emsp;&emsp; 大端字节序也称为**网络字节序**

## 4.2 在网络中，若两台计算机采用的字节序不一样，应该怎么办？
&emsp;&emsp; 当格式化的数据（比如32 bit整型数和16 bit短整型数）在两台使用不同字节序的主机之间直接传递时，接收端必然错误地解释之。那应该怎么解决呢？解决问题的方法是： 
> 发送端总是把要发送的数据转化成大端字节序数据后再发送，而接收端知道对方传送过来的数据总是采用大端字节序，所以接收端可以根据自身采用的字节序决定是否对接收到的数据进行转换（小端机转换， 大端机不转换）。因此大端字节序也称为**网络字节序**，它给所有接收数据的主机提供了一个正确解释收到的格式化数据的保证。
>  

## 4.3 同一台机器的两个进程需要考虑字节序的问题吗？
&emsp;&emsp; 需要，即使是同一台机器上的两个进程（比如一个由C语言编写，另一个由JAVA编写）通信，也要考虑字节序的问题（JAVA虚拟机采用大端字节序）。

## 4.4 在Linux环境下，如何转换为网络字节序？
Linux提供了如下4个函数来完成主机字节序和网络字节序之间的转换：
```cpp
#include＜netinet/in.h＞
unsigned long int htonl(unsigned long int hostlong);
unsigned short int htons(unsigned short int hostshort);
unsigned long int ntohl(unsigned long int netlong);
unsigned short int ntohs(unsigned short int netshort);
```
它们的含义很明确：比如`htonl`表示“host to network long”， 即将长整型（32 bit） 的主机字节序数据转化为网络字节序数据。 这4个函数中， 长整型函数通常用来转换IP地址， 短整型函数用来转换端口号（当然不限于此。 任何格式化的数据通过网络传输时， 都应该使用这些函数来转换字节序） 。