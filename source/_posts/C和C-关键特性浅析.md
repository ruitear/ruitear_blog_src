---
title: C和C++关键特性浅析
date: 2020-05-12 00:25:41
tags:
  - C++
  - C
categories:
  - 语言特性
---

##### [](#1-命名空间namespace与static)1. [命名空间namespace与static](#命名空间namespace与static)

namespace的是前世今生，在C语言中定义了3个层次的作用域，即文件(编译单元)、函数和复合语句。`C++`引入了类作用域，类是出现在文件内的。在不同的作用域中可以定义相同名字的变量，互不于扰，系统能够区别它们。全局变量的作用域是整个程序，在同一作用域中不应有两个或多个同名的实体，包括变量、函数和类等。在程序设计中，只要小心注意，可以争取不发生错误。但是，大型的应用软件，往往不是由一个人独立完成的，而是若干人合作完成的，不同的人分别完成不同的部分，最后组合成一个完整的程序。假如不同的人分别定义了类，放在不同的头文件中，在主文件(包含主函数的文件)需要用这些类时，就用#include命令行将这些头文件包含进来。由于各头文件是由不同的人设计的，有可能在不同的头文件中用了相同的名字来命名所定义的类或函数，这样就会产生命名冲突。在程序中还往往需要引用一些库(包括`C++`编译系统提供的库、由软件开发商提供的库或者用户自己开发的库)，为此需要包含有关的头文件。如果在这些库中包含有与程序的全局实体同名的实体，或者不同的库中有相同的实体名，则在编译时就会出现名字冲突。
C 语言和早期的C++语言没有提供有效的机制来解决这个问题，没有使库的提供者能够建立自己的命名空间的工具。人们希望`ANSI C++`标准能够解决这个问题，提供—种机制或者工具，使由库的设计者命名的全局标识符能够和程序的全局实体名以及其他库的全局标识符区别开来。这个时候namespace就应运而生了。【namespace支持跨文件】

- **什么是命名空间？** 一个由程序设计者命名的内存区域，程序设计者可以根据需要指定一些有名字的空间域，把一些全局实体分别放在各个命名空间中，从而与其他全局实体分隔开来。

- **命名空间的作用**
建立一些互相分隔的作用域，把一些全局实体分隔开来。

- 
**命名空间的定义**
`namespace ns1 {int a；double b; void fun() { //do something};}`

- 
**使用命名空间成员**
命名空间名：：命名空间成员名，例如 `std::vector`

- **命名空间的使用** (1)使用命名空间中的全部成员
`using namespace std;`在本作用域(using语句所在的作用域)中引入命名空间std所有的成员的声明，以备使用；(2)使用命名空间中的特定成员;using后面的命名空间成员名必须是由命名空间限定的名字。`using std::string;`在本作用域(using语句所在的作用域)中引入命名空间std成员string的声明以备使用；也就是在使用了无命名namespace的文件中，使用命名空间中的成员，不必(也无法)用命名空间名限定。

- **无名的命名空间** C++还允许使用没有名字的命名空间，由于命名空间没有名字，在其他文件中显然无法引用，它只在本文件的作用域内有效。

###### [](#1-1-编译属性)1.1 [**编译属性**](编译属性)

在C中，给变量(局部/全局变量)、函数，加上 static表示当前函数和变量的linkage 为 internal，也就是对外部不可见, 这样就可以在不同的unit(源一般指当前文件) 中定义同名的函数和变量了。在C++中，在不同的编译unit中定义同名的函数和变量的，这样就有了命名空间namespace，解决了变量和函数名字冲突问题，同时给函数和变量限定为 internal linkage。

###### [](#1-2-static-修饰的对象成员和成员函数)1.2 [**static 修饰的对象成员和成员函数**](修饰的对象成员和成员函数)

**static修饰的对象成员**不能通过调用构造函数来进行初始化，因此static修饰的数据成员必须在类外进行初始化且只会初始化一次。【静态变量内存分配在 .data段】
**static修饰的成员函数**为静态成员方法，静态成员方法可以在类内或类外定义，但必须在类内声明；；static成员方法没有this指针，不能直接引用非static数据成员或调用类的非static成员方法，只能调用类的static成员数据和static成员方法；static成员不是任何对象的组成，不依赖对象的调用所以static成员方法不能被声明为const，因为const只限定该类的对象；static成员方法不能同时被声明为虚函数。

参考文献：

- [https://www.cnblogs.com/skullboyer/p/10200039.html](https://www.cnblogs.com/skullboyer/p/10200039.html)

- [https://www.cnblogs.com/JefferyZhou/archive/2012/09/24/2700306.html](https://www.cnblogs.com/JefferyZhou/archive/2012/09/24/2700306.html)

##### [](#2-虚函数表vtbl与（虚）函数表指针vptr)2. [虚函数表vtbl与（虚）函数表指针vptr](#虚函数表vtbl与（虚）函数表指针vptr)

**虚函数表**：存储虚函数信息的表； **虚函数表指针**：指向虚函数表的指针。

###### [](#2-1-无虚函数的类)2.1 [**无虚函数的类**](#无虚函数的类)

讲C++的虚函数表之前，先看一下C语言是如何实现一个面向对象的类。
```
// 方法1
#include
typedef struct{
    int height;
    int age;
    
    void (*func)(struct* stu);
} student;
void profile(struct* stu) {printf("height:%d age:%d\n", stu.height, obj->age);}
}
int main() {
    student jhon;
    jhon.height = 170;
    jhon.age = 20;
    jhon.func = profile;
}
```

要有面向对象的效果，只能给struct里面添加函数指针，然后把函数赋值给指针；实际使用中这种用法比较少，主要因为函数指针的空间成本；每实例化一个结构体对象，就会有一个函数指针，例如8字节吗，如果有这个对象有m个函数指针，n个实例对象，那么消耗额外的内存是`m*8*n`字节。因此C语言通常不这样用，而是直接用外部定义一个函数。

```
// 方法2
#include
typedef struct{
    int height;
    int age;

} student;
void profile(struct* stu) {printf("height:%d age:%d\n", stu.height, obj->age);}
}
int main() {
    student jhon;
    jhon.height = 170;
    jhon.age = 20;
    profile(&jhon);
}
```

对于一个C++的类，

```
#include
class student{
public:
    int height;
    int age;
    void profile() {printf("height:%d age:%d\n", height, age);}
} ;

}
int main() {
    student jhon;
    jhon.height = 170;
    jhon.age = 20;
    jhon.profile();
}
```

跟C语言实现对比，看着像第一种方式通过在结构体定义函数指针的方式来实现。但实际相当于第二种方式。为什么呢？**C++中类的操作和封装只是针对程序员的，而编译器还是面向过程的**，编译器会给类的成员函数添加额外的类指针参数，在运行期间传入对象实际的指针。这说明**类的数据成员和成员函数是分离的**。每个函数都有一个地址（指针），不论这个函数是全局函数还是类的成员函数；在类不含虚函数的情况下，编译器在编译的时候就会把函数的地址确定下来，运行期间直接去调用这个地址的函数，这就是**静态绑定**方式（static binding）的函数调用。

###### [](#2-2-有虚函数的类)2.2 [有虚函数的类](#有虚函数的类)

为什么会出现虚函数？为了解决面向对象中多态的实现。
```
#include
class student{
public:
    int height;
    int age;
    virtual void profile() {printf("height:%d age:%d\n", height, age);}
} ;
class seniorStu: public student{
public:
    int height;
    int age;
    string address;
    virtual void profile() {printf("height:%d age:%d\n address:%s", height, age, address.c_str());}
} ;
int main() {
    student bob(185,20);
    seniorStu jhon(170,25,"四川成都");
    jhon.profile();
    // 输出 height: 170 age:25 address:四川成都
    bob.profile();
    // 输出 height: 185 age:20
    student* jim = &jhon;
    jim->profile();
    // 输出 height: 170 age:25
    student& tom = &jhon;
    tom.profile();
    // 输出 height: 170 age:25 
    return 0;
}
```

上述，可以看到用父类指针指向子类的地址，最终调用的profile函数韩式子类的
这个现象称之为**动态绑定**（dynamic binding）后者说**延迟绑定**（lazy binding）。这里如果把子类的virtual，这个代码将调用父类的profile，输出：

> 
输出 height: 170 age:25 

输出 height: 170 age:25 

这是由于**类的数据成员和函数成员是分离的**。

###### [](#2-3-虚表指针和虚函数表)2.3 [虚表指针和虚函数表](#虚表指针和虚函数表)

从内存布局上，只能看到成员变量，看不到成员函数。因为在编译的时候就确定调用哪个函数。
将了这么多，可以回归到整体函数虚函数表了，也就是C++多态的实现。

```
#include
#include
class student{
public:
    int height;
    int age;
    virtual void profile() { printf("height:%d age:%d\n", height, age);}
    virtual void status() { std::coutprofile();
    // 输出 height: 170 age:25
    student& tom = &jhon;
    tom.profile();
    // 输出 height: 170 age:25
    return 0;
}
```

这个示例父类有两个虚函数，子类重载了这两个虚函数。查看编译器的内存布局，
clang有个命令可以输出对象的内存布局（不同编译器内存布局未必相同），

> 
clang -cc1 -fdump-record-layouts -stdlib=libc++ student.cpp

可以看到类似于这样的内存分布：
```
*** Dumping AST Record Layout
    0  | class student
    0  | (student vtable pointer)
    x  | int height
    x  | int age

*** Dumping AST Record Layout
    0  | class seniorStu
    0  |   class student (primary base)
    0  |     (student vtable pointer)
    x  |     int height
    x  |     int age
    x  |   string status
```

在父类student的起始位置有一个student vtable pointer；子类seniorStu是在他的基础上多了自己的成员status。对于含有虚函数的类，在编译期间编译器会给这种类自动的在起始位置添加一个**虚表指针**（vptr），让虚表指针vptr指向一个虚表；虚表中存储了实际的函数地址。**那么虚表里面存储了那些详细的内容呢？** g++有打印虚表的操作（请在Linux上使用g++）会自动写到一个文件里：

> 
g++ -fdump-class-hierarchy student.cpp

可以看出如果vptr指向的并不是虚表的表头而是表头加偏移量而获得一个地址，也就是虚函数的位置。
```
Vtable for student
student::_ZTV7student: 4u entries
    0 (int (*)(...))0
    8 (int (*)(...))(& _ZTI7student)
    16 (int (*)(...))student::profile
    24 (int (*)(...))student::status

Class student
    size=24 align=8
    base size=20 base align=8
    student (0x0x7f9b1fa8c960) 0
    vptr=((& student::_ZTV7Actress) + 16u)

Vtable for Sensei
    seniorStu::_ZTV6seniorStu: 4u entries
    0 (int (*)(...))0
    8 (int (*)(...))(& _ZTI6seniorStu)
    16 (int (*)(...))seniorStu::profile
    24 (int (*)(...))seniorStu::status

Class seniorStu
    size=24 align=8
    base size=24 base align=8
    seniorStu (0x0x7f9b1fa81138) 0
    vptr=((& seniorStu::_ZTV6seniorStu) + 16u)
    student (0x0x7f9b1fa8c9c0) 0
    primary-for seniorStu (0x0x7f9b1fa81138)
```

###### [](#2-4-总结一下)2.4 [**总结一下**](#总结一下)

**1、如果一个类中有一个虚函数**，这个类就有一个虚函数表。虚函数表示类的，而不是属于对象的，在编译时就确定了，存放在只读的数据段（非代码段）。每实例化一个类对象都有一个虚函数表指针，指向类的虚函数。虚函数表指针属于类的对象。存放在堆上或者栈上(**什么时候存放在堆，什么时候存放在栈**上需要再探讨一下)。**2、对于有派生子类的情况**：如果基类有虚函数，不管派生类实现或者没有虚函数，都有虚函数表。基类的虚函数表和派生类的虚函数表不是同一个表。如果派生类没有重写基类的函数，则派生类的虚函数表和基类的虚函数表内容是一样的。如果派生类重写了基类的虚函数，则培生累的虚函数表中用的是派生类的函数。**3、对于多继承的情况**，含有虚函数的基类有多少个，派生类就有多少个虚函数表；派生类有的而基类没有的，添加在派生类的第一个虚函数表中，虚函数表的结果是*表示还有下一个虚函数表，结果是0表示是最后一个虚函数表。

参考文献： 

- [https://www.zhihu.com/question/389546003/answer/1194780618](https://www.zhihu.com/question/389546003/answer/1194780618)

- [https://blog.csdn.net/weixin_30552635/article/details/99157190](https://blog.csdn.net/weixin_30552635/article/details/99157190)

##### [](#3-C-C-的内存分布)3. [C/C++的内存分布](#C/C++的内存分布)

| 内存区 | 用途 | 申请释放方式 |
|---|---|---|
| 栈区 | 存储局部和临时变量，函数调用时存储函数调用时，存储函数的返回指针用于控制函数的调用和返回 | 程序开始是自动分配内存，结束自动释放 |
| 堆区 | 存储动态内存分配 | 程序员手动申请，手动释放；若程序员不释放，在程序结束是，系统回收 |
| BSS段 | 存储未初始化的变量数据，包括初始化为0的全局变量 | 只占运行时内存空间不占文件空间。在程序整个周期内，BSS段的数据一致存在 |
| data区 | 存储已经初始化的变量数据，为数据分配空间，数据保存在目标文件中 | —– |
| 代码段TXT | 存储程序代码的内存映射，以及函数体的二进制代码 | —– |
| 文字(只读)常量区 | 存字符串常量放等 | 程序结束后有系统释放 |
| 全局区(静态区) | 全局变量和静态变量存储在一起，未初始化或者初始化为0的存在BSS，初始化不为0的全局变量或者静态变量存在此处 | 程序结束后由系统释放 |

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
