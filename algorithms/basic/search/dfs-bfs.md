# 搜索算法的思路(树中某个结点为goal)
* **（已经发现，待探索的结点统称fringe。）**
* **1、从fringe中选择一个结点A，进行goal test**
* **2、看A下一步可能走的结点（这一步称为A的展开）**
* **3、将A展开后得到的结点放入fringe，从fringe中弹出A**
* **4、在fringe中根据搜索算法探索下一个结点**

# DFS 
## 概念和特点
* **从左至右遍历每一个子树，要求遍历完最下面的叶子节点才能遍历下一条子树**可以类比成一个**执着**的人。
* 当树的大小无限，dfs无法找到goal
* **遍历时间复杂度：O(n)，空间复杂度：O(n)**
* 设树的最大深度为m，则找寻

<img width="370" height="250" alt="F638A53C0A374F8C1790343038" src="https://github.com/user-attachments/assets/6ee51de3-64d9-4641-ad5e-1293181e67ad" />

## eg1.整数加法分解 (非递增约束)
**题目描述**：将正整数 N (N <= 20) 分解为多个正整数之和，要求分解的序列非递增，且包含双重排序约束。

```cpp
// 注意边界检查
void dfs(int rem, int start, vector<int>& path) {
    if (rem == 0) {
        printPath(path);
        return;
    }
    // 剪枝：i 的上限取 start 和 rem 中的较小值，强制保证非递增
    for (int i = min(start, rem); i >= 1; i--) {
        path.push_back(i);
        dfs(rem - i, i, path);
        path.pop_back(); // 回溯，恢复现场
    }
}
```

## eg2. 全排列问题
