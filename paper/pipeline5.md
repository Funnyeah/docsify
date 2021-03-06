# 召回之矩阵分解

<img align="center"  width='800' height='450' src="picture/pipeline20.png"  />

- 用户内容矩阵表示用户行为，问号表示未知，就是可能需要推荐的
- 但是数亿量级的矩阵很难计算，所以需要矩阵分解近似成U和V
- U(m,k)：m表示用户数量，k表示隐特征，维度一般为30～100，是挖掘出的用户对内容的共性特征抽象

### 使用场景
<img align="center"  width='500' height='150' src="picture/pipeline21.png"  />

- u2i:用户喜欢的内容，即矩阵A的一行，根据得分返回topN
- i2i:内容相关的内容，频道推荐、猜你喜欢
- u2u2i:将用户喜欢的up的相似的up主的内容召回推荐

<img align="center"  width='800' height='400' src="picture/pipeline22.png"  />

- 基于用户行为日志生成训练样本
- 模型训练基于spark\flink ml,将训练样本通过模型训练分解为U和V特征向量，实现特征分解

<img align="center"  width='800' height='400' src="picture/pipeline23.png"  />

- 用户和内容做相似计算，就是u2i，根据用户喜欢推荐内容
- 内容和内容做相似计算，就是i2i，根据内容推荐内容
- 用户和用户做相似，就是u2u2i，根据用户的相似推荐内容

<img align="center"  width='800' height='400' src="picture/pipeline24.png"  />

- 物料存储到KV系统，业内叫倒排索引，和日常索引有所差异，查询速度快满足在线性能

### 特点
<img align="center"  width='700' height='300' src="picture/pipeline25.png"  />

### 矩阵分解实现算法-ALS(交替最小二乘法)

<img align="center"  width='800' height='400' src="picture/pipeline26.png"  />

- 求导求极值
- spark ml有现成工具实现
- [demo戳这](https://blog.csdn.net/weixin_36288711/article/details/112193501?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.pc_relevant_antiscanv2&utm_relevant_index=6)