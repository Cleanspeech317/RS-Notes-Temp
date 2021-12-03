EGES

可伸缩性、稀疏性、冷启动

方法基于图嵌入框架，从用户序列构造出item图，学习到item的嵌入。为了解决稀疏性和冷启动问题，将side info也利用到图嵌入框架中。

### 2 FRAMEWORK

#### 先验知识——图嵌入框架+DeepWalk

通过随机游走得到节点序列，skip-gram模型学习节点表示

![image-20210125230517925](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125230517925.png)

![image-20210125235739511](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125235739511.png)

#### 从用户行为构造物品图

- 先前一些CF模型，只考虑的item的共现，没有考虑item的序列性，考虑序列可以更准确表达用户的偏好
- 基于会话窗口的用户行为（session-based users’ behaviors）：不使用用户的全部行为，设置一个时间窗口，只选发生在时间窗口内的行为，实际选取1小时作为时间窗口
- 物品A后面紧接着出现物品B，则有向边从A指向B，$e_{AB}$ 等于所有用户的序列中A-B紧接着出现的次数

#### 基础图嵌入

- 通过随机游走得到节点序列，skip-gram模型学习节点表示，随机游走的转移概率：

![image-20210125233145152](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125233145152.png)

- skip-gram的选取 w 的window size，那么每条序列中的每个中心item会有2w个样本

![image-20210125233332005](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125233332005.png)

![image-20210125233315905](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125233315905.png)

- 应用负采样，损失函数变成：

![image-20210125233412995](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125233412995.png)

#### 带有side info的图嵌入

- 电子商务RS中item的side info 有类别、商店、价格等。side info作为关键特征，广泛的应用在ranking阶段，很少用在matching阶段。

- 假设：带有相似side info的items应该在嵌入空间中相近，提出GES。

- 1个item id嵌入，n个side info，n个side info的嵌入，每个item 有n+1个嵌入，将这些嵌入采用平均池化，得到item的最终表示。

  ![image-20210126000327586](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210126000327586.png)

![image-20210126000130934](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210126000130934.png)

- 这种方法对于冷启动可以进行更准确的推荐（找最相似的metadata?）

#### 加强版的带有side info的图嵌入

- 设想：不同的side info对于物品的表示贡献不同，提出EGES。

- 采用加权平均池化代替平均池化。比起GES多了一个权重矩阵A，$A_{ij}$ 表示第i个item的第j个嵌入的权重。

  ![image-20210126001024912](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210126001024912.png)

- 其中 $e$ 是为了使得权重>0

  ![image-20210126001253493](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210126001253493.png)

- Q：那么权重矩阵就是先初始化，再梯度更新？



### 5 RELATED WORK

#### 图嵌入

分为三种：

- 分解方法：LINE，近似分解邻接矩阵，并且保留一阶相似性和二阶相似性
- 深度学习方法：加强模型捕获图的非线性能力
- 基于随机游走的方法

#### 带有side info的图嵌入

设想：有相似side info的物品应该在嵌入空间中相似

异质图

#### 用于RS的图嵌入

