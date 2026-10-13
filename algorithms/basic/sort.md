# 排序
## 快速排序
* **思想：分治**
* **思路**：确定分界点x（左边界，右边界，中点等）→让≥arr[x]的数在x右侧，≤arr[x]的数在x左侧→递归处理左右两段
* 暴力解
  - 开辟两个数组，遍历两遍原数组，小的放在a[],大的放在b[]
  - 时间复杂度：O(n)
    
### 实现
* **思路**
  - 两个指针i，j，起点是数组的头l和尾r
  - i从头向后遍历，直到遇见第一个≥x的数→i停止，j开始向前遍历，直到遇到第一个≤x的元素
  - 交换i和j指向的元素，并让i和j同时向中间移动一位，继续上一步
  - **停止标志：i和j相遇(i>=j)**
    
* **代码实现**
  - 注意边界问题：递归使用j的时候，分界点x不能选择q[r]，否则陷入死循环
  - **时间复杂度：O(log2n)**
  - ``` cpp
    void quickSort(int q[],int l,int r){
      if(l>=r) return;
      int x=q[l+(r-l)/2], i=l-1, j=r+1;
      while(i<j)
      {
        do i++ ; while(q[i]<x);
        do j-- ; while(q[j]>x);
        if(i<j) swap(q[i],q[j]);
      }
  
      quickSort(q,l,j);
      quickSort(q,j+1,r);
      }
    ```
    
## 归并排序
* **思路(先递归后合并)**
  - 1、以**中间点**为分界线，递归排序左边和右边
  - 2、将两个有序的数组合并为一个
    - eg：3，5，9和1，4，8合成为1，3，4，5，8，9
* **代码实现：双指针算法**
  - 1、开辟一个答案数组，两指针分别位于原数组区分出的左右数组的开头
  - 2、比较两个指针指向的值，哪个更小就将值搬到答案数组，对应指针向后跳。当某个指针跳到最末尾了就停止，另一个数组剩下的值全部搬到答案数组
  - <img width="250" height="200" alt="ec381ce49361cba199185824291d1267" src="https://github.com/user-attachments/assets/041f0c88-b62c-4f19-af1d-ea5747239db8" />
  - ``` cpp
    #include<iostream>
    #include<vector>
    using namespace std;
    #define N 100000
    int tmp[N];
    void mergeSort(int q[],int l,int r){
        if(l>=r)return;
        int mid=(l+r)/2;
        mergeSort(q,l,mid),mergeSort(q,mid+1,r);
    
        int k=0,i=l,j=mid+1;
        while(i<=mid&&j<=r){
            if(q[i]<=q[j])tmp[k++]=q[i++];
            else tmp[k++]=q[j++];
        }
        while(i<=mid)tmp[k++]=q[i++];
        while(j<=r)tmp[k++]=q[j++];
    
        for(i=l,j=0;i<=r;i++,j++)q[i]=tmp[j];
    }
    ```
  



    

