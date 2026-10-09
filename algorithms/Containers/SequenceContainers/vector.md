## vector的使用
* **1. 引入与初始化**

在使用之前，必须包含对应的头文件。

```cpp
#include <iostream>
#include <vector>

int main() {
    // 1. 创建空 vector
    std::vector<int> vec1;

    // 2. 指定初始大小 (包含 5 个元素，默认初始化为 0)
    std::vector<int> vec2(5);

    // 3. 指定初始大小和初始值 (包含 5 个元素，全部为 10)
    std::vector<int> vec3(5, 10);

    // 4. 列表初始化 (C++11，最常用)
    std::vector<int> vec4 = {1, 2, 3, 4, 5};

    // 5. 通过另一个 vector 拷贝构造
    std::vector<int> vec5(vec4);
    
    return 0;
}
```


* **2. 访问元素**
---
| 方法 | 说明 | 边界检查 |
|---|---|---|
| vec[i] | 访问索引为 i 的元素 | ❌ 越界会导致未定义行为 |
| vec.at(i) | 访问索引为 i 的元素 | ✅ 越界抛出 std::out_of_range 异常 |
| vec.front() | 访问第一个元素 | - |
| vec.back() | 访问最后一个元素 | - |
| vec.data() | 返回指向底层数组首元素的裸指针 | - |
---
示例代码：
``` cpp
std::vector<int> vec = {10, 20, 30};
std::cout << vec[0] << "\n";       // 输出: 10
std::cout << vec.at(1) << "\n";    // 输出: 20
std::cout << vec.front() << "\n";  // 输出: 10
std::cout << vec.back() << "\n";   // 输出: 30
```


* **3. 修改容器 (增删改)**
``` cpp
std::vector<int> vec;

// --- 添加元素 ---
vec.push_back(10);    // 在尾部添加 10
vec.emplace_back(20); // (C++11) 推荐！直接在尾部构造对象，性能更好

// --- 删除元素 ---
vec.pop_back();       // 删除尾部元素 (删除 20)，无返回值

// --- 插入元素 ---
// 在头部插入 5 (注意：频繁在头部插入性能较差)
vec.insert(vec.begin(), 5);      
// 在索引 1 的位置插入 3 个 7
vec.insert(vec.begin() + 1, 3, 7); 

// --- 擦除元素 ---
vec.erase(vec.begin());          // 删除首个元素
vec.erase(vec.begin(), vec.begin() + 2); // 删除区间 [begin, begin+2) 的元素

// --- 清空 ---
vec.clear(); // 清空所有元素，size 变为 0
```


* **4. 容量与状态**
理解 size 和 capacity 的区别.
---
| 方法 | 说明 |
|---|---|
| vec.size() | 返回当前实际包含的元素数量。 |
| vec.capacity() | 返回在不重新分配内存的情况下，最多可容纳的元素数量。 |
| vec.empty() | 检查容器是否为空，返回 bool (size == 0)。 |
| vec.reserve(n) | 预分配内存，使 capacity 至少为 n。 |
| vec.shrink_to_fit() | (C++11) 要求容器释放未使用的内存，使 capacity 降至和 size 相同。 |
---

``` cpp
std::vector<int> vec;
// 预先分配 100 个元素的空间，避免插入时多次触发昂贵的内存拷贝扩容
vec.reserve(100); 

if (vec.empty()) {
    std::cout << "Vector is empty.\n";
}
```


* **5. 遍历方法**

``` cpp
std::vector<int> vec = {1, 2, 3, 4, 5};

// 方法 1: 范围 for 循环 (C++11，最推荐，代码最简洁)
for (int val : vec) {
    std::cout << val << " ";
}

// 修改元素时使用引用
for (int& val : vec) {
    val *= 2; 
}

// 读取复杂对象时使用 const 引用，避免拷贝开销
for (const auto& val : vec) {
    std::cout << val << " ";
}

// 方法 2: 传统下标遍历 (适合需要使用索引的场景)
for (size_t i = 0; i < vec.size(); ++i) {
    std::cout << vec[i] << " ";
}

// 方法 3: 迭代器遍历
for (auto it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << " ";
}
```

* **6. 常用算法结合**
vector 通常与 <algorithm> 头文件中的算法配合使用。

``` cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> vec = {4, 1, 8, 5, 3};

    // 1. 排序 (默认升序)
    std::sort(vec.begin(), vec.end()); 
    // 排序 (降序，使用反向迭代器)
    std::sort(vec.rbegin(), vec.rend());

    // 2. 查找 (查找值为 5 的元素)
    auto it = std::find(vec.begin(), vec.end(), 5);
    if (it != vec.end()) {
        std::cout << "找到了 5\n";
    }

    // 3. 翻转
    std::reverse(vec.begin(), vec.end());
    
    return 0;
}
```
