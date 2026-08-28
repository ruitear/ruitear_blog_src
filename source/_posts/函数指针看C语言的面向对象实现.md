---
title: 从函数指针看C语言的面向对象实现
date: 2020-05-05 14:28:37
tags:
  - 函数指针
  - 设计模式
categories:
  - 函数指针
---

##### [](#1-什么是函数指针)1. [什么是函数指针](#什么是函数指针)

**指针函数**：
指针函数本质是一个函数，只不过返回值为某一类型的指针（地址值）。函数返回值必须用同类型的变量来接受，也就是说，指针函数的返回值必须赋值给同类型的指针变量。
**函数指针**：
函数指针本质是一个指针，只不过这个指针指向一个函数。指针变量通畅指向一个整形、字符型、或者数组等变量，而函数指针指向的是函数。
常见的函数都有其入口，比如 `main()` 函数是整个程序的入口，我们调用的其他函数都有其特定的入口，正如我们可以通过地址找到相应的变量一样，我们也可以通过地址找到相应的函数。而这个存储着函数地址的指针就是函数指针。可以通过指针访问相应的变量，函数指针也可以像函数一样用于调用函数、传递参数。
**回调函数**
函数指针作为某个函数的参数。理解回调函数，我们先要搞清楚回调函数有什么作用。比如老板、经理、你三个角色。老板通常是规则的指定着，经理按照规则指派相应的人去做事，而你就是任务的具体执行者。当老板要求经理去做一个事情，那么老板就是主调函数，经理就是回调函数，你按照规则去处理事情就是相应回调函数。你处理的结果会反馈给经理，经理拿着你的结果再反馈给老板。老板就可以使用这个结果去做相关的事情了。在这个环节中，你只需要按照指定的规则去做事，而经理不需要考虑事情是怎么做的，他只需要把相应的事情分配给对应的人去处理即可，然后将获取的结果反馈给老板。如果规则有变，只需要对应的员工知道哪里改变了，而经理不需要关注这些细节。
**函数指针的应用场景** 

- 普通的函数指针，指向函数，实现比如勾子函数等

- 回调函数，实现在特定情况下需要不同的处理，或者对不同些数据的不同处理

- 封装，函数模板，构造对象（类似于C++虚函数）等等

结构体，是定义了一个用户自定义内型，那么这个内型就可以定义N多的变量，不同的变量拥有不同的值，也包括函数指针的值，这样就实现了”多态”，模拟OOP中的虚函数。
**函数指针声明** 函数原型`int sum(int,int);`是一个返回值为int类型, 参数是两个int类型的函数.  如何声明该类型函数的指针呢？将函数名替换成(*pf)形式即可，即我们把sum替换成(*fp)即可，fp为函数指针名`int （*fp）(int,int);` 这样就声明了和sum函数类型相同的函数指针fp, `*`和fp为一体，`说明了fp为指针类型`;  `*fp`需要用括号括起来，否则就会变成`int *fp(int,int);`,这时候意义就变化了，成立一个返回值为一个int指针类型的函数，函数一个函数指针。为了避免每次声明函数指针的时候方便，函数指针可以用typedef定义：
```
typedef int (*myFun)(int,int);//为该函数指针起一个新名字
myFun f1;       //声明myFun类型的函数指针f1
```

**函数指针赋值**
```
#include
int mytest(int a,int b)
{
    return a+b;
}
typedef int(*fp)(int,int);
int main(void)
{
    fp func1 = mytest; //表达式1
    fp func2 = &mytest;//表达式2
    printf("%d\n",func1);
    printf("%d\n",func2);
    return 0;
}
```

这里，声明返回类型为int，接受两个int类型参数的函数指针func1和func2，分别给它们进行了赋值。表达式1和表达式2在作用上是一样的。由于函数名在被使用时总是由编译器把它转换为函数指针，因此前面加上&只是显式的说明了这一点而已。

**函数指针的调用**

```
#include
int mytest(int a,int b)
{
    return a+b;
}
typedef int(*fp)(int,int);
int main(void)
{
    fp func = mytest; //表达式1
    func(1,2); //表达式1
    (*func)(3,4); //表达式2
    return 0;
}
```

在函数指针后面加括号，并传入参数即可调用，其中表达式1和表达式2似乎都可以成功调用，但是哪个是正确的呢？ANSI C认为这两种形式等价。

##### [](#2-函数指针的用途)2. [函数指针的用途](#函数指针的用途)

1、作为句柄函数传入，
以库函数qsort排序函数为例，

```
void qsort(void *base,size_t nmemb,size_t size , int(*compar)(const void *,const void *));
```

拆开来看如下：
```
void qsort(void *base, size_t nmemb, size_t size, );
```

拿掉第四个参数后，它是一个无返回值的函数，接受4个参数，第一个是void*类型，代表原始数组，第二个是size_t类型，代表数据数量，第三个是size_t类型，代表单个数据占用空间大小，而第四个参数是函数指针。这第四个参数，即函数指针指向的是什么类型呢？
```
int(*compar)(const void *,const void *)
```

这是一个接受两个const void*类型入参，返回值为int的函数指针。
这个参数告诉qsort，应该使用哪个函数来比较元素，即只要我们告诉qsort比较大小的规则，就可以帮我们对任意数据类型的数组进行排序。

结构体中包含函数指针,可以像一般变量一样,包含函数指针变量.下面是一种简单的实现
```
struct opt  
{  
    int x,y;  
    int (*func)(int,int); //函数指针  
}; 
void main()  
{  
    struct opt demo;  
    demo.func=add2; //结构体函数指针赋值  
    //demo.func=&add2; //结构体函数指针赋值  
    printf("func(3,4)=%d\n",opt.func(3,4));  
    demo.func=add1;  
    printf("func(3,4)=%d\n",opt.func(3,4));  
}
```

C语言中的struct是最接近类的概念，但是在C语言的struct中只有成员，不能有函数，但是可以有指向函数的指针，们使用函数。
```
typedef struct student  
{  
    int id;  
    char name[50];   
    void (*init)();  
    void (*process)(int id, char *name);  
    void (*destroy)();  
}stu; 

void init()  { /*do something*/ }  
void process(int id, char *name)   { /*do something*/ }
void destroy() { /*do something*/ } 

int main()  
{  
    stu *stu1 = (stu *)malloc(sizeof(stu));  
    stu1->id=1000;  
    strcpy(stu1->name,"C++");  
    stu1->init=init;  
    stu1->process=process;  
    stu1->destroy=destroy;  
    printf("%d\t%s\n",stu1->id,stu1->name);  
    stu1->init();  
    stu1->process(stu1->id, stu1->name);  
    stu1->destroy();  
    free(stu1);  
    return 0;  
}
```

##### [](#3-函数指针的面向对象应用)3. [函数指针的面向对象应用](#函数指针的面向对象应用)

函数指针是解耦对象关系的最佳利器。

###### [](#3-1-命令模式)3.1 [命令模式](#命令模式)

命令模式通过增加中转数据结构，使命令下达和命令执行二者依赖于接口，从而达到二者时间上不相关、二者变化方向独立的目的。
【好处：代码清晰明了，容易添加和删除，易维护】

```
typedef struct {
  uint8_t cmd;
  void (* handle)(char *buffer);
} package;
 
static const package packageItems[] =
{
  {0x01, parse_temperature},
  {0x02, parse_humidity},
  {0x03, parse_illumination},
  {0xFF, NULL},
};
static uint8_t parse(void *buffer, uint16_t length)
{
  package *frame = (package *)buffer;
  if (frame == NULL) { // 异常处理 }

  const package *entry;
  for (entry = packageItems; entry->handle != NULL; ++entry) {
    if(frame->cmd == entry->cmd) {
      entry->handle(frame->data);
      break;
    }
  }
}
```

什么时候会用到这个命令模式呢？

- 按键处理

- 协议解析（串口，网口，CAN，等等）

###### [](#3-2-策略模式)3.2 [策略模式](#策略模式)
```
//开关配置过程
typedef void (*ProcStrategy)();
ProcStrategy procStrategy;
if (strategy == A)
{
    procStrategy = ProcStrategyA;
}
else
{
    procStrategy = ProcStaregyB;
}

//核心逻辑处理过程
...
procStrategy();
...
```

###### [](#3-3-观察者模式)3.3 [观察者模式](#观察者模式)

函数需要抽象出来一个action函数
```
void Eat() { // do something}
void Drink() { // do something}
void Rest() { // do something}

typedef void (*lAction)();
lAction flikuiAction[3] = {Eat, Drink, Rest};

//命令响应程序
void DoCmd(Cmd *cmd)
{
    for(int i = 0; i cmdNum; i++)
    {
        flikuiAction[cmd->cmd[i]]();
    }
}
```

###### [](#3-4-状态模式)3.4 [状态模式](#状态模式)

有这么一个需求，宋江命令李逵杀敌，李逵此时有很多种状态，李逵要根据自己不同的状态做出不同的反应：如果正在吃饭，就扔掉碗进入空闲态，如果处于空闲，就拿起斧子进入战斗状态，如果处于战斗状态，就不做响应。
```
typedef void (*LikuiDoAction)();

LikuiDoAction likuiDoAction[3][3] = 
{			/* EATING */		/* FIGHTING */		/* IDLE */
/* EAT */	{DoNothing,			ThrowAxe,			TakeBowl},
/* FIGHT */	{ThrowBowl,			DoNothing,			TakeAxe},
/* IDLE */	{ThrowBowl,			ThrowAxe,			DoNothing}
}

void LikuiAction()
{
    likuiDoAction[SongjiangCmd][LikuiStatus]();
}
```

##### [](#4-函数指针与函数调用的性能分析)4. [函数指针与函数调用的性能分析](#函数指针与函数调用的性能分析)

直观上看应该是函数调用开销更小。函数指针调用方案中需要先访问数据区，再访问函数，增加指令开销，同时，数据取值与函数指令加载必须串行执行，影响CPU流水性能；函数调用方案可以做内联优化。

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
