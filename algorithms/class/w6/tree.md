# 树和二叉树
## 树
### 基本概念
* 结点的度：结点拥有的子树的个数
* 树的度：结点度数的最大值
* 叶子（终端结点）：度=0的结点
* 分支：度≠0
* 深度：最大层数
* 有序树：同一层各子树从左至右有先后顺序
* 森林：多棵树的集合

### 树的遍历
* **前序遍历**：访问根节点→从左至右访问每一颗子树
  - <img width="300" height="340" alt="3f1864a1b705af149d29c1a4dfee4ba4" src="https://github.com/user-attachments/assets/0b8d6155-6976-44fb-be00-e5e20eec0b73" />

* **后序遍历**：从左至右访问每一颗子树→访问根结点
  - <img width="300" height="350" alt="52846f3d3c978f084c66f837a1a6573e" src="https://github.com/user-attachments/assets/48a2d2a5-c125-4648-9387-d0d118513b9b" />

* **层序遍历**：从左到右访问每一层
  - <img width="300" height="355" alt="b4685f120539722e5fcb175f016fbd0f" src="https://github.com/user-attachments/assets/ed2b375c-a26e-41a3-97df-83d4cbc67a51" />

* **命题：已知一棵树的前序遍历和后序遍历，可以唯一地还原整棵树**
  - 对数的结点个数n进行数学归纳证明
  - n=1，只有一个根节点，可以还原
  - 设小于n个结点的都可以唯一还原。设前序A1,...An，后序B1,...,Bn，则A1为根结点
    - A2为第一颗子树的根节点→存在j s.t.Bj=A2。则B1...Bj为第一颗子树的后序→A2及之后的j-1个结点为第一颗子树的前序
    - 由归纳假设：第一颗子树可以唯一还原。
    - 删去这两段，则再用同样的方法，依次唯一确定所有子树。

``` cpp
template <class T>
class LTree{
    private:
        T data;
        VecList<LTree<T> *> children;
    public:
        LTree(){

        }
        LTree(T x){
            data = x;
        }
        ~LTree(){

        }
        void addChild(LTree<T>* lt){
            children.insertEleAtPos(children.getLength(),lt);
        }
        void addChild(T x){
            LTree<T>* lt = new LTree<T>(x);
            addChild(lt);
        }
        LTree<T>* getChild(T x){ // 只获得这个结点的孩子，不获取孩子的孩子等
            for(int i=0;i<children.getLength();i++){
                LTree<T>* lt = children.getEleAtPos(i);
                if(lt->data==x){
                    return lt;
                }
            }
            return nullptr;
        }
        void setData(T x){
            data = x;
        }
        T getData(){
            return data;
        }
        void printPreOrder(){//前序
            cout << data << " ";
            for(int i=0;i<children.getLength();i++){
                LTree<T>* lt = children.getEleAtPos(i);
                lt->printPreOrder();
            }
        }
        void printPostOrder(){//后序
            for(int i=0;i<children.getLength();i++){
                LTree<T>* lt = children.getEleAtPos(i);
                lt->printPostOrder();
            }
            cout << data << " ";
        }
        void printLevelOrder(){//层序：使用队列
            LinkQueue<LTree<T>*> q;
            q.enQueue(this);
            while(!q.isEmpty()){
                LTree<T>* lt = q.deQueue();
                cout << lt->data << " ";
                for(int i=0;i<lt->children.getLength();i++){
                    q.enQueue(lt->children.getEleAtPos(i));
                }
            }
        }
};
```

### 树的表示
* **双亲表示法**
  - 用数组存储所有结点，每个结点保存：
    - 数据域：结点的数据。
    - 双亲域：父结点在数组中的下标。
  - 根没有父结点，可以用 `-1` 表示。
  - | 数组下标 | 结点 | 父结点下标 |
    | --- | --- | --- |
    | 0 | A | -1 |
    | 1 | B | 0 |
    | 2 | C | 0 |
    | 3 | D | 0 |
    | 4 | E | 1 |
    | 5 | F | 1 |
  - 特点
    - 查找一个结点的父结点很方便，时间复杂度为 O(1)。
    - 查找一个结点的所有孩子，通常需要扫描整个数组，时间复杂度为 O(n)。
    - 若要表示有序树，还需要约定兄弟的先后顺序，例如按数组下标递增排列。
* **孩子表示法**
  - 每个结点保存一个孩子列表，记录其所有孩子的位置。
  - 孩子列表可以用链表实现，也可以用 `vector` 等顺序容器实现。对于有序树，列表按孩子从左到右的顺序存储。
  - | 结点 | 孩子列表 |
    | --- | --- |
    | A | B、C、D |
    | B | E、F |
    | C | 空 |
    | D | 空 |
    | E | 空 |
    | F | 空 |
  - 实际存储时，列表中通常保存孩子结点的数组下标或指针。
  - **特点**
    - 可以直接找到一个结点的所有孩子。
    - 若一个结点有 $k$ 个孩子，遍历其孩子列表的时间复杂度为 $O(k)$。
    - 没有单独记录父结点，查找父结点通常需要搜索。

* **孩子兄弟表示法**

* code

## 二叉树
### 概念
* 每个结点最多只有两个子结点。可以没有左子结点但有右子结点。
* **左（右）斜树**：所有结点只有左（右）子树
  - 结点数=层数
* **满二叉树**：所有分支结点都有两个分支，所有叶子都在同一层
  - k层满二叉树的结点数为2^k-1
* **完全二叉树**：所有的结点编码都和满二叉树一样
  - 最多只有一个度为1的结点，且孩子为左孩子

### 二叉树的特性
#### 二叉树中叶子结点的个数比度2的结点数多1个
- 证明<img width="400" height="320" alt="49f72b00b0374fc6bcbeafd0cbb52328" src="https://github.com/user-attachments/assets/d70578fc-3a04-4516-96d9-e2cf5b6a878a" />

- **推论：结点数为偶数，则必有度1的结点**

#### 具有n个结点的完全二叉树深度为log2(n)+1
#### 叶子的编码
- <img width="400" height="320" alt="2953a21efb1c69d383a16616dded634a" src="https://github.com/user-attachments/assets/67cc0405-306d-48ff-b4a1-4e64c94b84e6" />


### 二叉树的遍历
* **中序遍历**
  - <img width="400" height="200" alt="2130d8cb5b21dcf53fc7bb6711a947f7" src="https://github.com/user-attachments/assets/33b24b4f-1f5e-4d88-b0fb-3268ae04e41d" />

* **命题(还原二叉树):前/后/层序+中序可以唯一地还原二叉树**
  - 证明
#### **非递归算法（模拟递归）**
* **前序**
  - 思路：为了能在左子树遍历之后找到右子树，将遇到的右结点都存入栈
    - 各结点：打印结点值→看右：右孩子放入栈。若右为空直接看左孩子
    - →再看左：若左不为空，来到左孩子；若左孩子为空：**弹出栈顶**，并且来到栈顶
  - ``` cpp
    void printPreOrderNR(){
            LinkStack<BinTree<T>*> s;
            BinTree<T>* bt = this;//现在研究的结点
            while(bt!=nullptr || !s.isEmpty()){
                if(bt!=nullptr){
                    cout << bt->data << " ";//打印结点值
                    if(bt->right!=nullptr) s.push(bt->right);//右孩子入栈
                    bt = bt->left;//来到左孩子
                }
                else{
                    bt = s.pop();//左孩子为空，弹出栈顶并且来到栈顶
                }
            }
        }
* **中序**
  - 思路：为了能在遍历左子树之后找到根节点，**将所有遇到的根节点放入栈**
    - 来到一个结点时，
      - 若非空：**让它本身入栈，来到左孩子**
      - 若为空：**弹出栈顶并输出，来到栈顶的右孩子**
  - ``` cpp
            void printInOrderNR(){
            LinkStack<BinTree<T>*> s;//栈
            BinTree<T>* bt = this;//目前在的结点
            while(bt!=nullptr || !s.isEmpty()){
                if(bt!=nullptr){
                    s.push(bt);
                    bt = bt->left;
                }
                else{
                    bt = s.pop();
                    cout << bt->data << " ";
                    bt = bt->right;
                }
            }
        }
    ```
* **后序**
  - 思路：为了让左子树→右子树→根每个环节都能找到，需要**给每个入栈的结点加指标**
    - 去到一个结点，入栈，标记为1，去到左孩子
    - 若左孩子为空，弹栈顶，取出它的标记
      - 若标记为1，说明还没去到右孩子，还不能打印：再次入栈，标记变为2，去到右孩子
      - 若标记为2，说明左右子树都去过了，可以打印了
  - ``` cpp
      void printPostOrderNR(){
              LinkStack<BinTree<T>*> s;
              LinkStack<int> sN; //stack_Number
              BinTree<T>* bt = this;
              while(bt!=nullptr || !s.isEmpty()){
                  if(bt!=nullptr){
                      // first time encountering node
                      s.push(bt);
                      sN.push(1);
                      //cout << "Pushing " << bt->data << " for the first time" << endl;
                      bt = bt->left;
                  }
                  else{
                      bt = s.pop();
                      int num = sN.pop();
                      //cout << bt->data << " popped for the " <<(num == 1 ? "first" :"second") << " time" << endl;
                      if(num==1){
                          s.push(bt);
                          sN.push(2);
                          //cout << "Pushing " << bt->data << " for the second time" << endl;
                          bt = bt->right;
                      }
                      else{
                          cout << bt->data << " ";
                          bt = nullptr;
                      }
                  }
              }
          }
    ```

