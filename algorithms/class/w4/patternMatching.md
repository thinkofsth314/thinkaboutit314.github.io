---
算法: 模式匹配 (Pattern Matching Algorithms)
date: 2026-09-29
tags: 
  - 字符串
  - 模式匹配
---

**模式匹配算法（Pattern Matching Algorithms）**是在一个较长的“主串”（长度为 $n$）中，寻找一个或多个特定的“模式串”（长度为 $m$）出现的位置。

按照匹配机制和应用场景，主流的模式匹配算法主要分为两大类：**单模式匹配**与**多模式匹配**。

---

## 常用单模式匹配算法及 C++ 实现

这类算法用于在一个主串中查找**一个**特定的模式串。

### 1. BF 算法 (暴力匹配)
最基础的实现方式，双指针直接比对。

*   **核心思想**：从主串的第一个字符开始，与模式串逐个字符对比。如果遇到不匹配的字符，主串的指针回退（回溯）到上一次开始的下一个字符，模式串指针回到起点，重新开始比对。
*   **时间复杂度**：最好情况为 $O(n)$，最坏情况为 $O(n \times m)$。
*   **适用场景**：日常简单开发、数据量极小的场景。代码实现最简单，不易出错。

```cpp
#include <iostream>
#include <string>

using namespace std;

// 返回模式串在主串中首次出现的索引，未找到返回 -1
int bruteForce(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    
    if (m == 0) return 0;
    
    for (int i = 0; i <= n - m; i++) {
        int j = 0;
        while (j < m && text[i + j] == pattern[j]) {
            j++;
        }
        // 如果 j 等于模式串长度，说明全部匹配成功
        if (j == m) {
            return i;
        }
    }
    return -1;
}
```

### 2. KMP 算法


* ### 核心思想：
- **1、next数组的定义：不包含当前位置，前面字符串前后缀最大匹配长度。其中长度代表前缀和后缀的字符串长度，匹配表示前缀和后缀相等。**
  - 规定next[0]=-1,next[1]=0;
  - next[i]代表从第1位到第i位的前后缀最大匹配长度（当然不能是字符串本身的长度）。
  - 示例：**字符串：abaabbaba**
    - n[0]=-1,n[1]=0。
    - 对于第五个位置,是b，那就考虑abaa，可以看到前后缀长度取1就能匹配（a对a）→n[4]=1;
    - 对于第六个位置，也是b，考虑abaab，发现长度取2，那么最前面就可以和最后面匹配（ab对ab）→n[5]=2;
   
- **2、匹配方法（假设s2正在匹配s1）**
  - s2匹配到的第一个不同的地方，记录它的下标i（数组下标），并且查找s2的next[i]（记作a）
  - next[i]的值的含义是：目前s2的前a个字符和最后a个字符一样，那么就可以**将s2的头部(第0位)移动到i往前的第a个位置，此时比对位置正好在第a个位置**（如i=13，a=6.则将s2数组的第0位移到原来的第7位）

 
    - <img width="500" height="250" alt="90b259dd4df229bd1a613c3b6164827a" src="https://github.com/user-attachments/assets/dc10f8bb-f80b-4f41-86da-533b7198fa92" />

    - s1固定，s2往后跳
    - 移动后从第a个位置开始比较
  - 重复之前的操作
  - 为什么s2中间跳过的部分不会匹配成功？
    - 因为如果能匹配成功，next数组的值就和原来的不一样
  ```cpp
    #include <iostream>
    #include <string>
    #include <vector>
    
    using namespace std;
    
    int kmp(string s1,string s2){
    //s1中当前比对的位置是x
    //s2中当前比对的位置是y
    int n=s1.length(), m=s2.length(),x=0,y=0;
    vector<int> next=getNext(s2);
    while(x<n&&y<m){//s1比对的位置越界，则匹配失败。s2比对的位置越界，则匹配已经成功
        if(s1[x]==s2[y]){
            x++;
            y++;
        }
        //下面是开始有不匹配元素的处理
        else if(y==0)x++;//s2的比对位置已经在开头。则x往后走一直比对
        else y=next[y];//如果y还可以往前跳，则
    }
    return y==m? x-y:-1;
  }
  ```

- **3、next数组求值**
  - 流程：对于第i个位置，看i-1处的next值。设next[i-1]=7，则看第7位和第i-1位是否一样
    - 一样：next[i]=8
    - 不一样：看next[8],设为3.则继续看next[3]是否和第i-1位一样。
      - 相同：next[i]=3+1=4
      - 不同：继续看next[3],设next[3]=1，比对第1位和第i-1位
        - 相同：next[i]=1+1=2
        - 不同：继续看next[1]=0→比对i-1与第0位
    - 若跳到头了，无法继续跳，则next[i]=0
   
  
  - ```cpp
    #include <iostream>
    #include <string>
    #include <vector>
    
    vector<int> getNext(const string& s) {
    int m = s.length();
    vector<int> next(m, 0);//都初始化为0
    next[0]=-1;
    int i=2;//当前求next值的位置
    int cn=0;//和i-1位置比对的值的下标
    
    while(i<m){
      if(s[i-1]==s[cn])next[i++]= ++cn;//匹配到了，next值就等于匹配到的下标+1
      else if(cn>0)cn=next[cn];//没匹配到，cn往前跳到next[cn]，再匹配
      else next[i++]=0;//cn跳到最前面了，就不能继续跳
    }
      
    return next;//vector可以返回数组本身
    }

  
*   **时间复杂度**：稳定在 $O(n+m)$。
  - 对于取next数组：$O(m)$
    - 运行多少次为三种情况运行次数的加和。
    - 第一种和第三种i都增加了。而i的变化范围：2~m。则加起来只会运行m-2次。
    - 第二种是cn单独变化。由于cn的值一定小于等于m-2（只有第一种会加值），则减小次数必小于等于m-2.(cn=0)
    - 加起来最多运行2m-4次。
  - 对于匹配函数
    - 还是三种情况：第一、二种x往后，x最多跳n次。
    - 第三种只有y往前，且y被x控制住了（y加的时候x都加了），则最多也是执行n次
    - 则匹配的循环为O(n)

* **例题：二叉树的匹配**
  两颗二叉树root和subroot。验证root中是否有与subroot相同的子树（包括结构和数值）
  - 暴力：每一个节点都检查一遍
  

## 课上的代码
``` cpp
#include <iostream>
#include <iomanip>
#include <string>
using namespace std;

int* getNext(string T){
    int tLen = T.length();
    int* next = new int[tLen];
    if(tLen==0) return next;
    next[0] = -1;
    if(tLen==1) return next;
    next[1] = 0;
    for(int j=2;j<tLen;j++){
        int k = next[j-1];
        while(k!=-1 && T[k]!=T[j-1]){
            k = next[k];
        }
        if(k==-1 || T[k]==T[j-1]){
            next[j] = k+1;
        }
    }
    return next;
}

int KMP(string S, string T){
    int sLen = S.length();
    int tLen = T.length();
    if(tLen==0) return 0;
    int* next = getNext(T);
    int i=0, j=0;
    while(i < sLen && j < tLen){
        cout << i << " " << j << endl;
        if(j==-1 || S[i]==T[j]){
            i++;
            j++;
        }
        else{
            j = next[j];
        }
    }
    if(j==tLen){
        return i-j;
    }
    else{
        return -1;
    }
}

```
