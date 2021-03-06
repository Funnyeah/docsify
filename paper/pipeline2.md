# 物料召回

### 推荐系统模块示意图

<img align="center"  width='600' height='400' src="picture/pipeline5.png"  />

### 行业常见召回策略

<img align="center"  width='700' height='400' src="picture/pipeline_61.png"  />

<img align="center"  width='700' height='400' src="picture/pipeline6.png"  />

- 多路召回解决并发的问题，需要在几十毫秒从亿级数据筛选出几百
- 多路召回还可以解决数据多样性问题

### 不同策略实现机制拆解

#### 1.热门类
<img align="center"  width='800' height='400' src="picture/pipeline7.png"  />

- 用户对视频、网站、文章的点击、点赞、评论，相当于是sql查询的count统计，可以加权重
- 键值对存入redis中，一般由线下定时小时、天的物料挖掘
- 物料召回是在线实时的，如:app刷一刷就立马推荐了

#### 2.兴趣类

<img align="center"  width='800' height='400' src="picture/pipeline8.png"  />

- 相比于热门的直接统计，兴趣类会从用户行为日志拆解出内容标签等
- 通过1h、1d、1w等时间段的统计
- 在线使用时，根据用户标签的偏好，推送对应热门标签物料
  
#### 3.协同过滤

<img align="center"  width='800' height='400' src="picture/pipeline9.png"  />

- 基于用户协同过滤，把和用户A兴趣相同的用户B感兴趣的内容推给A
- 基于内容协同过滤，很多用户都同时访问了内容A和C,那么我们认为AC内容是相似的，当用户看了A时就会推C给用户

<img align="center"  width='800' height='400' src="picture/pipeline10.png"  />

- 基于内容协同,会统计两两内容间同时出现的次数，形成协同矩阵，如图AD出现次数为2次
- 当召回时候，一个用户已经阅读了A，此时会推给他D
- 随着用户越来越多，协同矩阵越来越准确，实际效果来看，基于内容的协同过滤是十分有效的

适用场景

<img align="center"  width='800' height='240' src="picture/pipeline11.png"  />

- 基于用户的协同适用于头条、短视频等，但是新用户的冷启动会有问题
- 基于商品的协同适用于电商

#### 4.向量召回

<img align="center"  width='800' height='400' src="picture/pipeline12.png"  />

- 前面多路召回会存在一个问题：不同召回的权重是多少
- 双塔模型：用户特征和物料特征放入模型中相互学习，得到近似空间分布
- e.g.用户一次点击会产生当时用户的特征和点击的那个物料的特征
- 当我们在召回时候只知道用户当前的特征，通过模型得到当前用户向量，然后在物料向量中查和当前用户向量相似的进行推荐