---
title: leetcode刷题-排列和组合问题
date: 2020-05-05 20:35:54
---

以数组和字符串形式的例题总结，涉及到排列和组合相关问题的,主要通过递归或者循环解决问题。

##### [](#1-组合问题)1. 组合问题

【从n个元素中取m个元素–子集】
**例题1.** LeetCode-78-[子集](https://leetcode-cn.com/problems/subsets/) 

> 
给定一组**不含重复元素**的整数数组 nums，返回该数组所有可能的子集（幂集）。
说明：解集不能包含重复的子集。
示例:
输入: nums = [1,2,3]
输出: [[3],[1],[2],[1,2,3],[1,3],[2,3],[1,2],[] ]

**题解**：从数组中连续至少0个元素，构成的子集问题，可以模拟成一个层次遍历的过程；从空集合开始，然后空集合的基础上依次插入元素，先是1个元素的集合，接着是2个元素的集合，依次类推。
首先，创建一个局部变量存放结果，把空集合插入存进去`vector<vector<int> > res(1)`；遍历整个数组，同事嵌套遍历结果数组，往结果数组中依次插入当前遍历到的数组元素，由于结果数组中有一个空子集，嵌套遍历结果数组的时候，就是往空子集中插入第1个元素，此时这个结果肯定是没有出现的排列，再把这个排列插入结果数组；这样结果中就存入了[]和[1];此时进去下一次迭代，数组的第二个元组，在依次往结果数组中的子集插入，也就是[2]依次插入[]和[1]中；以此类推，就可以遍历完所有的排列。
```
/**代码实现**/
vector> subsets(vector& nums) {
    vector > res(1);
    for(int i=0;i tmp=res[j];  
            tmp.push_back(nums[i]);
            res.push_back(tmp);
        }
    }
    return res;
}
```

**例题2.** LeetCode-90-[子集II](https://leetcode-cn.com/problems/subsets-ii/)

> 
给定一个可能**包含重复元素**的整数数组 nums，返回该数组所有可能的子集（幂集）。
说明：解集不能包含重复的子集。
示例:
输入: [1,2,2]
输出: [[2],[1],[1,2,2],[2,2],[1,2],[]]

**题解** 题目中描述有重复元素，所以去重是在排列里面很关键，在上一题的基础上增加去重的操作即可，这里用一个set去去重，由于排列不考虑数的顺序，所以需要排序一下，进行归一化，然后设计一个hash表去重，这里hash表示就是unordered_map,键值用排序后的数组字符化表示；

```
string clcHashValue(vector& nums){
    string res = "";
    sort(nums.begin(),nums.end());
    for(int i=0; i > subsetsWithDup(vector& nums) {
    vector > res(1);
    unordered_set numstt;
    for(int i=0;i tmp=res[j];  
            tmp.push_back(nums[i]);
            string tempHash = clcHashValue(tmp);
            if(numstt.find(tempHash) == numstt.end()){
                res.push_back(tmp);
                numstt.insert(tempHash);
            }
        }
    }
    return res;
}
```

##### [](#2-排列问题)2. 排列问题

**例题1.** 面试题 08.07. [无重复字符串的排列组合](https://leetcode-cn.com/problems/permutation-i-lcci/)

> 
无重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合，字符串每个字符均不相同。
**示例1**:
 输入：S = “qwe”
 输出：[“qwe”,”qew”,”wqe”,”weq”,”ewq”,”eqw”]
**示例2**:
 输入：S = “ab”
 输出：[“ab”,”ba”]

 **题解** 我们把字符串看成两部分，第一部分是它的第一个字符，第二部分是后边的字符。求整个字符串的排列，可以就可以看成两部分，首先求所有可能出现在第一个位置的字符，也就是把第一个字符和后边所有的字符交换。第二部分固定第一个字符求后面字符的排列。在第二部分的时候仍把后边的字符分成2部分，依次类推。这样就是很典型的递归了。
```
void strPerm(vector &result, string &s, int beginIndex) {
    if(beginIndex == s.size()-1) {
        result.push_back(s);
        return;
    }
    for(int i = beginIndex; i permutation(string s) {
    vector result;
    if(s.empty()) {
        return result;
    }
    strPerm(result, s, 0);
    return result;
}
```

**例题2.** 面试题 08.08. [有重复字符串的排列组合](https://leetcode-cn.com/problems/permutation-ii-lcci/)

> 
有重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合。
示例1: 输入：S = “qqe”  输出：[“eqq”,”qeq”,”qqe”]
示例2: 输入：S = “ab”  输出：[“ab”, “ba”]
提示: 字符都是英文字母。字符串长度在[1, 9]之间。

 **题解** 如上题一样，这里主要去重，去重，可以简单地额用一个hash表来解决，这里用unordered_set来创建hash。

```
void strPermu(unordered_set& perLib, vector& res, string &s, int beginIndex) {
    if(s.size()-1 == beginIndex) {
        if(perLib.find(s)==perLib.end()) {
            res.push_back(s);
            perLib.insert(s);
        }
            
        return;
    }
    for(int i = beginIndex; i permutation(string s) {
    vector result;
    if(s.empty()) {return result;}
    unordered_set perLib;
    strPermu(perLib, result, s, 0);
    return result;
}
```

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
