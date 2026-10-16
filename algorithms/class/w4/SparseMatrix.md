## 稀疏矩阵的实现
* **使用三元组表实现，用一个结构体数组存取有效值的行列和值**
```cpp
  #include<iostream>
  #include<vector>
  using namespace std;
  template <class T>
  class SparseMatrix{
      private:
          struct Triplet{
          int row;
          int col;
          T val;
          };
          int rows;
          int cols;
          vector<Triplet> data;//建立一个三元组为元素的数组
```

* **构造和析构**
  
``` cpp
     public:
      SparseMatrix(){
          // default is 10 rows, 10 columns
          rows=10;
          cols=10;
      }
      SparseMatrix(int r, int c){
          rows=r;
          cols=c;
      }
      ~SparseMatrix(){
          //vector会自动析构
      }
```

* **填入有效值，获取值**

``` cpp
      void setEntry(int rPos, int cPos, T x) {
          if (rPos < 0 || rPos >= rows ||
              cPos < 0 || cPos >= cols) {
              throw "Matrix index out of range" ;
          }
          for (size_t i = 0; i < data.size(); i++) {//与已有值重合的逻辑
              if (data[i].row == rPos &&
                  data[i].col == cPos) {
                  if (x == 0) {
                      // 用最后一个元素覆盖当前位置，再删掉末尾
                      data[i] = data.back();
                      data.pop_back();
                  }
                  else {
                      data[i].val = x;
                  }
                  return;
              }
          }
          // 原来没有这个位置，只有非零值才需要添加
          if (x != 0) {
              data.push_back({rPos, cPos, x});
          }
      }
      T getEntry(int rPos, int cPos){
         if(rPos<0||rPos>=rows||cPos<0||cPos>=cols)throw"Matrix index out of range";
          for(int i=0;i<data.size();i++){
              if(data[i].row==rPos&&data[i].col==cPos){
                  return data[i].val;
              }
          }
          return 0;
      }
```

* **加减乘**

``` cpp
      SparseMatrix<T> * add(SparseMatrix<T> * B){
          if(rows!=B->rows||cols!=B->cols)throw"Matrices have incompatible sizes";
          SparseMatrix<T>* C=new SparseMatrix<T>(rows,cols);
          C->data=data;
          for(int i=0;i<B->data.size();i++){
              int r=B->data[i].row;
              int c=B->data[i].col;
              T value =C->getEntry(r,c)+B->data[i].val;
              C->setEntry(r,c,value);
          }
          return C;
      }
      SparseMatrix<T> * subtract(SparseMatrix<T> * B){
          if(rows!=B->rows||cols!=B->cols)throw"Matrices have incompatible sizes";
          SparseMatrix* C=new SparseMatrix<T>(rows,cols);
          C->data=data;
          for(int i=0;i<B->data.size();i++){
              int r=B->data[i].row;
              int c=B->data[i].col;
              T value=C->getEntry(r,c)-B->data[i].val;
              C->setEntry(r,c,value);
          }
          return C;
      }
      
      SparseMatrix<T> * multiply(SparseMatrix<T> * B){
          //perform multiplication if the sizes of the matrices are compatible.
          if(cols!=B->rows)throw"Matrices have incompatible sizes";
          SparseMatrix* C=new SparseMatrix<T>(rows,B->cols);
          //矩阵乘法AB(ij)==∑AikBkj(k从1到A的列),这里的∑通过外层对data数组的遍历实现
          //乘法有值当且仅当Aik,Bkj都不为0,故只关注三元组里的值
          //留意遍历的i和j与矩阵里的行列不一样！行和列要看数组里存的值
          for(int i=0;i<data.size();i++){
              for(int j=0;j<B->data.size();j++){
                  if(data[i].col==B->data[j].row){
                      int r=data[i].row;
                      int c=B->data[j].col;
                      T value=C->getEntry(r,c)+data[i].val*B->data[j].val;//Cij的加和还没结束
                      C->setEntry(r,c,value);
                  }
              }
          }
          return C;
      }
```

* **打印**

``` cpp

      // Only call this function if you know the size of matrix is reasonable.
      void printMatrix(){
          for(int r=0;r<rows;r++){
              for(int c=0;c<cols;c++){
                  cout<<getEntry(r,c)<<" ";//未找到值默认为0
              }
              cout<<endl;
          }
      }
      
      //This is for printing only non-zero entries
      void printNZMatrix(){
          vector<Triplet> temp=data;
          int n=temp.size();

          //排序
          for (int i = 0; i < n - 1; i++) {
              for (int j = i + 1; j < n; j++) {
                  if (temp[i].row > temp[j].row ||
                      (temp[i].row == temp[j].row &&
                      temp[i].col > temp[j].col)) {//根据短路特性
                      Triplet t = temp[i];
                      temp[i] = temp[j];
                      temp[j] = t;
                  }
              }
      }

      for (int i = 0; i < n; i++) {
          if (temp[i].val != 0) {
              cout << temp[i].row << " "
                   << temp[i].col << " "
                   << temp[i].val << '\n';}
      }
  }
```

    
