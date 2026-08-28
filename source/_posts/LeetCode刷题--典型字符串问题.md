---
title: LeetCode刷题--典型字符串问题
date: 2020-05-05 18:56:43
tags:
  - leetcode
categories:
  - 函数指针
---

字符串的**子串**和**子序列**，**子串必须是连续的**，**子序列不一定要求联系** 。**什么叫回文串**？如果一个字符串正着读和反着读是一样的，那它就是**回文串**。

##### [](#1-最长回文串)1. 最长回文串

**例题1.** 409. [最长回文串](https://leetcode-cn.com/problems/longest-palindrome/)

> 
给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。
在构造过程中，请注意区分大小写。比如 “Aa” 不能当做一个回文字符串。

> 

> 
- 注意:
假设字符串的长度不会超过 1010。
示例 1:
输入:b “abccccdd”
输出: 7
> 
- 解释: 我们可以构造的最长的回文串是”dccaccd”, 它的长度是 7。
> 

**题解：** 给一个字符串，要求这个字符串的字符能构成的最长回文字符串，建立一个hash统计每个字符的数量，然后遍历hash表，如果字符的个数是偶数，则将这些偶数的字符个数加起来；然后将字符数是奇数的依次减去1，同事统计奇数的个数在奇数>0的时候在累加和上+1，否则+0，得到的结果就是答案可以构成的最长回文字符串。
```
class Solution {
public:
    int longestPalindrome(string s) {
        if(s.size() charLibHash;
        for(auto & ch: s){
            charLibHash[ch]++;
        }
        int oddNum = 0;
        int evenNum = 0;
        for(auto itr = charLibHash.begin(); itr!=charLibHash.end();itr++) {
            if(itr->second%2 == 0){evenNum+=itr->second;}
            else{
                if( itr->second>0 ) {
                    evenNum+=itr->second-1;
                }
                oddNum++;
            }
        }
        return evenNum+(oddNum>0?1:0);
    }
};
```

**例题2.**  5.[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

> 
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

> 

> 
- 示例 1：
输入: “babad”
输出: “bab”
注意: “aba” 也是一个有效答案。
> 
- 示例 2：
输入: “cbbd”
输出: “bb”
> 

**题解：** 方法1 字符串正着读和反着读是一样的，也就是从从回文中心两边互为镜像。因此以中心展开，只有两种可能，1是回文字符串是奇数长度，2是偶数长度；对于 n 长度的字符串，不知道它的回文串中心倒底是奇数数还是偶数，所以我们要对这两种情况都做遍历，也就是 n+(n-1) = 2n - 1，所以时间复杂度为 O(n)；当中心确定后，我们要围绕这个中心来扩展回文，那么最长的回文可能是整个字符串，所以时间复杂度为 O(n)；所以依次遍历字符串，时间复杂度为 O(n^2)。

```
class Solution {
public:
    int palindromeLen(string& s, int beginIndex, int endIndex) {
        while(beginIndex>=0 && endIndex endIndex - beginIndex){
                beginIndex = i - (circleLen - 1) / 2;
                endIndex = i + circleLen/2;
            }
        }
        return s.substr(beginIndex, endIndex-beginIndex+1);
    }
};
```

方法2 **暴力法**，暴力法将选出所有子字符串可能的开始和结束位置，并检验它是不是回文。假设 n 是输入字符串的长度，从字符串头尾开始依次截取字符串，检查是否为回文字符串。从头一遍循环，尾部一次遍历，中间检查是否是回文再一次遍历，因为验证每个子字符串需要 O(n)O(n) 的时间，所以运行时间复杂度是 O(n^3)。

```
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() = 0; i--) {
            int copyIndexI = i;
            string tempStr;
            if (result.size() >= i+1) {  // s.substr(0, i + 1).size()
                break;
            }
            for (int j = 0; j  result.size() ? tempStr : result;
                }
            }
        }
        if (s.size() > 1 && result.empty()) { return s.substr(0, 1); }
        return result;
    }
};
```

**例题3.** 214. [最短回文串](https://leetcode-cn.com/problems/shortest-palindrome/)

> 
给定一个字符串 s，你可以通过在字符串前面添加字符将其转换为回文串。找到并返回可以用这种方式转换的最短回文串。

> 

> 
- 示例 1:
输入: “aacecaaa”
输出: “aaacecaaa”
> 
- 示例 2:
输入: “abcd”
输出: “dcbabcd”
> 

**题解：** 题目要求从字符串开始添加字符，形成的最短回文字符串。**方法1：暴力法**：首先检查字符是不是回文字符串，如果是直接返回，若不是则依次从末尾删除字符串，每删除一个检查一遍当前字符串是否是回文字符串，如果是就将删除的字符串添加到字符串开头；否则继续重复删除和检查的动作，直至删掉n-1个字符；
```
class Solution {
public:
    int iSpaliStr(string& s, int beginIndex, int endIndex){
        while(beginIndex 0 ; endIndex--) {
            if(iSpaliStr(s, 0, endIndex) == true) {
                break;
            }
        }
        string temp = s.substr(endIndex+1, s.size() - endIndex-1);
        // 或者取s.substr(endIndex+1); 取endIndex+1至字符串结尾的子串。
        reverse(temp.begin(), temp.end());
        return temp+s;

    }
};
```

**方法2：暴力升级版**： 寻找从开头开始的最长回文串，然后将末尾的除去最长回文串部分的几个字符倒置后加到原字符串开头即可。将原始字符串逆序，然后比较对应的子串即可判断是否是回文串。

```
**举例**
abbacd
原s: abbacd, 长度记为 n
逆r: dcabba, 长度记为 n
判断 s[0,n) 和 r[0,n)
abbacd != dcabba
判断 s[0,n - 1) 和 r[1,n)
abbac != cabba  
判断 s[0,n - 2) 和 r[2,n)
abba == abba  
从开头开始的最长回文串也就找到了, 接下来只需要使用之前的方法。
将末尾不是回文串的部分倒置加到原字符串开头即可。

class Solution {
public:
    string shortestPalindrome(string s) {
        if(s.empty()) {return s;}

        string str = s;
        reverse(str.begin(), str.end());
        int index = 0;
        int len = s.size();
        for(;index两个字母相同
    cell[i][j] = cell[i-1][j-1]+1
else:
    cell[i][j] = 0

== 公共子序列 ==  
if word_a[i] == word_b[j]:  --->两个字母相同
    cell[i][j] = cell[i-1][j-1]+1
else:
    cell[i][j] = max(cell[i-1][j], cell[i][j-1])
```

**例题1.** 516. [最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

> 
给定一个字符串s，找到其中最长的回文子序列。可以假设s的最大长度为1000。

> 

> 
- 示例 1:
输入:
“bbbab”
输出:
4
一个可能的最长回文子序列为 “bbbb”。
> 
- 示例 2:
输入: “cbbd”
输出: 2
一个可能的最长回文子序列为 “bb”。
> 

**题解：** 求字符串的最长回文序列，理解一下回文，就是正反念都一样，那么把字符串颠倒一下，求回文子序列就相当于求字符串和它的翻转字符串公共子序列。用动态规划求解，

- **状态**：f[i][j] 表示 s 的第 i 个字符到第 j个字符组成的子串中，最长的回文序列长度是多少；

- **转移方程**：
如果 s 的第 i 个字符和第 j 个字符相同的话
`dp[i][j] = dp[i + 1][j - 1] + 2`
如果 s 的第 i 个字符和第 j 个字符不同的话
`dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])`;

**参考方法**：
[1]. [https://leetcode-cn.com/problems/longest-palindromic-subsequence/solution/dong-tai-gui-hua-si-yao-su-by-a380922457-3/](https://leetcode-cn.com/problems/longest-palindromic-subsequence/solution/dong-tai-gui-hua-si-yao-su-by-a380922457-3/)
[2]. [https://leetcode-cn.com/problems/longest-palindromic-subsequence/solution/jian-dan-zhi-jie-de-dong-tai-gui-hua-by-ha-di-2/](https://leetcode-cn.com/problems/longest-palindromic-subsequence/solution/jian-dan-zhi-jie-de-dong-tai-gui-hua-by-ha-di-2/)
```
class Solution {
public:
    int longCommStr(vector>& dp, string& str1, string& str2) {
        for(int i = 1; i >& dp, int Len){
        for(int i = 0; i> dynamicsp(s.size()+1, vector(s.size()+1));
        initDpArr(dynamicsp, s.size());

        return longCommStr(dynamicsp, s, str);
    }
};
```

**例题2.** 1143. [最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

> 
给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列。
一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，”ace” 是 “abcde” 的子序列，但 “aec” 不是 “abcde” 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。
若这两个字符串没有公共子序列，则返回 0。

> 

> 
- 示例 1:
输入：text1 = “abcde”, text2 = “ace”
输出：3
解释：最长公共子序列是 “ace”，它的长度为 3。
> 
- 示例 2
输入：text1 = “abc”, text2 = “abc”
输出：3
解释：最长公共子序列是 “abc”，它的长度为 3。
> 
- 示例 3:
输入：text1 = “abc”, text2 = “def”
输出：0
解释：两个字符串没有公共子序列，返回 0。
> 
- 提示:
1 <= text1.length <= 1000
1 <= text2.length <= 1000
输入的字符串只含有小写英文字符。
> 

**题解：** 动态规划，直观遍历
```
class Solution {
public:
    int longCommStr(vector>& dp, string& str1, string& str2) {
        for(int i = 1; i >& dp, int Len){
        for(int i = 0; i> dynamicsp(text1.size()+1, vector(text2.size()+1));
        return longCommStr(dynamicsp, text1, text2);
    }
};
```

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
