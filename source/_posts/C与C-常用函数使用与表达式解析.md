---
title: C与C++常用函数使用与表达式解析
date: 2020-05-24 23:07:17
tags:
  - C++
  - C
categories:
  - 语言特性
---

##### [](#1-malloc和realloc)1. [malloc和realloc](malloc和realloc)

这两个函数都是申请内存的函数，他们有什么区别和联系呢？那些场景会出现意想不到的结果呢？下面具体探讨这两个函数.

###### [](#1-1-malloc函数)1.1  [malloc函数](#malloc函数)

`void *mallco(size_t size);` `malloc` 向系统申请分配指定size个字节的内存空间。返回类型是 `void*` 类型。

| malloc |
|---|
| 1、malloc 函数返回的是 void * 类型，定义的哪种类型的指针，必须强转为此类型指针; |  |
| 2、函数的实参为 sizeof(int) ，用于指明一个整型数据需要的大小； |  |
| 3、malloc是必须指定内存大小的空间，比如想分配50个int类型的空间：int* p = (int*) malloc ( sizeof(int) * 50 )； |  |
| 4、是malloc 只管分配内存，并不能对所得的内存进行初始化，所以得到的一片新内存中，其值将是随机的； |  |

> 
 闲话长谈malloc(0)与malloc(-1)返回值是什么样的？

```
#include
using namespace std;
int main(){
    int *ptr1=(int*)malloc(sizeof(int)*(0));
    int *ptr1=(int*)malloc(sizeof(int)*(-1));;
    cout 
**If size is zero**, the return value depends on the particular library implementation (it may or may not be a null pointer), but the returned pointer shall not be dereferenced.

表明**malloc(0)**是跟据，不同库的设置有着不同的表现，翻翻glibc(是GNU发布的c运行库,linux系统中最底层的api)的源码瞅瞅，有如下的说明：

> 
**If n is zero**, malloc **returns a minumum-sized chunk**. (The minimum size is 16 bytes on most 32bit systems, and 24 or 32 bytes on 64bit systems.)  On most systems, size_t is an unsigned type, so calls with negative arguments are interpreted as requests for huge amounts of space, which will often fail. The maximum supported value of n differs across systems, but is in all cases less than the maximum representable value of a size_t.

也就是说在Linux上执行 malloc(0)时，会拿到一个指向一小块内存的指针，这个指针指向的内存的大小是由机器决定的。这里不再细究，有兴趣可以参考: [当你 malloc(0) 时会发生什么](https://zhuanlan.zhihu.com/p/40490357)

###### [](#1-2-realloc函数)1.2 [realloc函数](#realloc函数)

`void *realloc(void *mem_address, unsigned int newsize);`
作用：重新申请内存空间，不影响原有数据，但是新分配的地址可能不一样。

| realloc |
|---|
| 1、当mem_address为null，size不为0，则realloc()和malloc()类似。分配一个newsize的内存块，返回一个指向该内存块的指针；如果没有足够可用的内存用来完成重新分配，则返回null而原来的内存块保持不变； |  |
| 2、将分配的内存扩大，则有以下情况：（1）如果当前内存段后面有需要的内存空间，则直接扩展这段内存空间，realloc()将返回原指针；（2）如果当前内存段后面的空闲字节不够，那么就使用堆中的第一个能够满足这一要求的内存块，将目前的数据复制到新的位置，并将原来的数据块释放掉，返回新的内存块位置。（3）如果申请失败，将返回NULL，原来的指针仍然有效； |  |
| 3、如果内存减少，则仅改变下索引信息，但并不代表被减少的部分内存还可以安全访问，这己经释放； |  |
| 4、如果newsize大小为 0，行为是未定义的，受具体实现来说， 目前VC++是相当于free(ptr)，并返回一个空指针，具体描述。 |  |

> 
 **If there is not enough memory**, the old memory block is not freed and null pointer is returned.

> 
 **If ptr is NULL**, the behavior is the same as calling malloc(new_size).

> 
 **If new_size is zero**, the behavior is implementation defined (null pointer may be returned (in which case the old memory block may or may not be freed), or some non-null pointer may be returned that may not be used to access storage).

 【参考文献】：
 1、[https://en.cppreference.com/w/c/memory/realloc](https://en.cppreference.com/w/c/memory/realloc)
 2、[https://en.cppreference.com/w/c/memory/malloc](https://en.cppreference.com/w/c/memory/malloc)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
