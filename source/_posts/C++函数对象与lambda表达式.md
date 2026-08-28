---
title: C++函数对象与lambda表达式
date: 2020-06-13 22:32:17
tags:
  - C++
categories:
  - C++
---

**函数对象**，一个类如果将`()`运算符重载，那么这个类就称为函数对象类，使用形式看起来像函数调用，实际执行了函数调用，因此坐函数对象。**`lambda`表达式**也称匿名函数，是无需定义标识符（函数名）的函数，如果函数只使用一次或者有限的次数，使用`lambda`表达式会比较方便。

##### [](#1-函数对象的如何实现)1. [函数对象的如何实现?](#函数对象的如何实现?)

C++中将对象当做函数使用，是函数式编程的思想。这里不作深入讨论，只讨论如何使用；举个简单例子：
【1】无入参的函数对象：
```
class ObjType
{
public:
    void operator() ()  // 无参数类型的()重载
    {
        cout
T accumulate(InIt first, InIt last, T val, Pred opt)
{
    for (; first != last; ++first) {
        init = opt(init, *first);
    }
    return init;
}
```

模板被实例化之后，opt(init, *first)需要定义，opt只能是函数指针或者函数对象。在使用时，实参只能传递函数名，函数指针或者函数对象。这里以函数对象为例举例说明：

```
#include 
#include 
#include 
template
class SumPow
{
public:
    SumPow(int pow) :m_pow(pow) { }
    const T operator() (const T& total, const T& value)
    { //计算 value的pow次方和
        T v = value;
        for (int i = 0; i  a2 = { 1, 2, 3 };
    // 实例化为函数指针
    cout (2)) (2)) ::iterator first, vector ::iterator last, int init, int(*op)(int, int))
{
    for (; first != last; ++first)
        init = op(init, *first);
    return init;
}
```

参数`SumPow<int>(2)`编译器实例化成了函数对象，有等效于如下定义的形式，形参是函数对象，相当于调用`opt.operator()(init, *first);`,也就是SumPow类的operator函数；
```
int accumulate(vector::iterator first, vector::iterator last, int init, SumPowers op)
{
    for (; first != last; ++first)
        init = op(init, *first);
    return init;
}
```

##### [](#2-lambda表达式)2.[lambda表达式](#lambda表达式)

在使用STL的时候，往往需要用很多函数对象或者函数指针。这些对象或者函数指针大部分是一次性，编写在需要的时候编写这样的代码有点浪费。对于只使用一次的函数对象或者函数，lambda表达式是一个有效的解决办法。Lambda表达式也是的闭包的同义词，但是两者也是有区别的，闭包是任何能够访问不在局部和参数列表里定义里面的自由变量的函数实例。`C++`中的lambda在`C++11`中才支持。Lambda 表达式的定义形式如下：

```
[外部变量访问方式说明符](参数表)->返回值类型{语句块}
```

**外部变量访问方式说明符**是`=`或`&`，表示{}中用到的按值捕获还是按引用捕获, 按值捕获表示定义在{}外面的变量，在{}中不允许被改变，按引用捕获表示允许修改。当然，在{}中也可以不使用定义在外面的变量。**`->`返回值类型**可以省略。如下是一个**lambda表达式**：
如果上边不够直观的话，看下边这个图，就更一幕了然。其中，1是捕获说明符；2是捕获参数列表；3是可选的`mutable`声明；4是可选的异常声明；5是返回类型；6是lambda操作域；
![image](/images/imglib/lambda.png)

** 捕获列表 capture clause **

| 捕获 | 作用 | 捕获 | 作用 |
|---|---|---|---|
| [] | 不捕获任何变量 | [] | 不捕获任何变量 |
| [&] | 引用捕获所有变量 | [=] | 以值捕获所有变量 |
| [&x] | 引用只捕获x | [x] | 以值只捕获x |
| [&, x] | 引用捕获所有变量，x是例外 | [=, &x] | 值捕获所有变量，其中x是例外 |
| [this] | 以引用捕获当前对象 | [*this] | 以传值方式捕获当前对象 |

捕获列表 **capture clause**
举个例子，如果lambda主体total通过引用捕获变量，factor通过值捕获变量，则以下捕获子句是等效的：
```
[&total, factor]
[factor, &total]
[&, factor]
[factor, &]
[=, &total]
[&total, =]
```

如果捕获子句默认& 按引用捕获，那么`identifier`在capture捕捉列表中不能再有以引用捕获的形式也就是`& identifier`。同样，如果capture默认按值捕获`capture-default =`，则capture该捕获列表不能有按值捕获的形式`= identifier`。而且，标识符不能在捕获列表中出现多次。**需要注意的是**默认在捕获列表没有明确捕获方式的时候`[]`，根据捕获参数列表传参形式决定是值捕获还是引用捕获, 形如`[](int &x){x=x*2;}`。
```
struct S { void f(int i);};
void S::f(int i) {
    [&, i]{};      // OK
    [&, &i]{};     // ERROR: 按引用捕获，在声明i按引用捕获多余
    [=, this]{};   // ERROR: this when = is the default
    [=, *this]{ }; // OK: captures this by value. 
    [i, i]{};      // ERROR: 重复声明捕获
    [=]{++i;};      // ERROR: 值捕获，不能修改对象
}
```

说了这么多，**lambda具体怎么用呢**？看一看，下边的例子：
```
int main()
{   
    int a[4] = { 11, 1, 37, 9 };
    /* 按值捕获 捕获值不可修改*/
    sort(a, a + 4, [=](int x, int y) -> bool {return x 感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
