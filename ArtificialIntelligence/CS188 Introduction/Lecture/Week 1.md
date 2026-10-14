## Search Problems
[cs188-sp26-lec02.pdf](https://github.com/user-attachments/files/33326510/cs188-sp26-lec02.pdf)

### Rational Agents
* **maximize its utility**
* **Environment and Agent**
 <img width="350" height="370" alt="94c9e2e11e981b495e1059f4968d2b03" src="https://github.com/user-attachments/assets/475e7071-332a-4643-9f8b-387f38041eaa" />

#### Reflex Agent **只根据目前的情况决定下一步，不考虑后果**
#### Planning Agent **Ask "what if"**

* **search P]problem**: **search models are models**
  - consistent
   - A state space S：目前的位置
   - An initial state s0：初始位置
   - Action A(s) in each state：根据位置的行动函数
   - Transition model Result(s,a)：行动的后果
   - A goal test G(s)：行动总目标
   - Action cost c(s,a,s')
  - 抽象化模型和搜索问题
    - <img width="600" height="360" alt="e8ee3e8f7c037fc263760c793bf6c008" src="https://github.com/user-attachments/assets/8c8e8c13-d8df-45a1-8304-7af1e331844a" />
  - search space
    - 所有可能出现的状态，比如吃豆人的位置、幽灵的位置、每个点位是否有豆子等
    - <img width="500" height="320" alt="50366aee1dae3dc35e79130b692a756e" src="https://github.com/user-attachments/assets/874612d8-c9b1-4880-b170-99e15a9f0d9a" />
  - 流程图：State Graphs, Search Trees
    - Space Graphs: 每一种可能的状态只出现一次
      - 
    - Search Trees: 树结构，每一层表示下一次行动可能出现的位置


    
* 



### Search Problems
### Uniformed Search Methods
* **Depth-First Search**
* **Breadth-First Search**
* **Uniform-Cost Search**
