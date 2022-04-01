<!-- <div align='center' ><font size='100'>wide&deep</font></div> -->
?> wide&deep

### 背景
&emsp;&emsp; 文中设计了一种融合浅层（wide）模型和深层（deep）模型进行联合训练的框架，综合利用浅层模型的记忆能力和深层模型的泛化能力，实现单模型对推荐系统准确性和扩展性的兼顾。对提出的W&D模型，文中从推荐效果和服务性能两方面进行评价：

- 效果上，在Google Play 进行线上A/B实验，W&D模型相比高度优化的Wide浅层模型，app下载率+3.9%。相比deep模型也有一定提升。
- 性能上，通过切分一次请求需要处理的app 的Batch size为更小的size，并利用多线程并行请求达到提高处理效率的目的。单次响应耗时从31ms下降到14ms。

&emsp;&emsp;与传统搜索类似，推荐系统的一个挑战是如何同时获得推荐结果准确性和扩展性。推荐的内容都是精准内容，用户兴趣收敛，无新鲜感，不利于长久的用户留存；推荐内容过于泛化，用户的精准兴趣无法得到满足，用户流失风险很大。相比较推荐的准确性，扩展性倾向与改善推荐系统的多样性。

### wide模型
&emsp;&emsp;在目前大规模线上推荐排序系统中，通用的线性模型如LR被广泛应用。线性模型通常输入二进制的one-hot稀疏表示特征进行训练。比如特征“user_installed_app=netflix”为1，表示用户已安装netflix。交叉特征AND（user_installed_app=netflix，impresion_app=Pandora）表示既安装了netflix app同时又浏览过Pandora的用户特征为1，否则为0。wide模型可以通过利用交叉特征高效的实现记忆能力，达到准确推荐的目的。wide模型通过加入一些宽泛类特征实现一定的泛化能力。但是受限与训练数据，wide模型无法实现训练数据中未曾出现过的泛化。

### deep模型
&emsp;&emsp;像FM和DNN这种Embedding类的模型，可以通过学习到的低纬度稠密向量实现模型的泛化能力，包括可以实现对未见过的内容进行泛化推荐。当模型query-item矩阵比较稀疏时，模型的会过分泛化，推荐出很多无相关性的内容，准确性不能得到保证。

### w&d模型
&emsp;&emsp;文中提出W&D模型，平衡Wide模型和Deep模型的记忆能力和泛化能力。
<img align='center' width='1000' height='300' src="picture/wide&deep.png" />

### Google Play 推荐系统
<img align='center' width='500' height='300' src="picture/w&d_2.png" />

&emsp;&emsp;和大多数推荐系统类似，Google Play推荐系统主要分Retrieve和Ranking两个阶段。（youtube论文的Matching和Ranking，详情请戳[DNN](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/m9FidZ9V_kictC2vPhazVA))

&emsp;&emsp;Retrieve阶段负责召回候选集，缩减待排序item数据，文中是O（10）数量级。Ranking进行进行精排序，即最终展示的app list 顺序。 Query包括用户画像，上下文特征等内容；Item就是App。交互行为特征可能是点击和购买。

### w&d详情

&emsp;&emsp;Wide部分就是基础的线性模型，表示为y=WX+b。X特征部分包括基础特征和交叉特征。交叉特征在wide部分很重要，可以捕捉到特征间的交互，起到添加非线性的作用。交叉特征可表示为：

&emsp;&emsp;<img align='center' width='250' height='60' src="picture/w&d_3.png" />

&emsp;&emsp;Deep部分就是个前馈网络模型。特征首先转换为低维稠密向量，维度通常O（10）-O（100）。向量随机初始化，经过最小化随时函数训练模型。激活函数采用Relu。前馈部分表示如下：

&emsp;&emsp;<img align='center' width='200' height='40' src="picture/w&d_4.png" />

&emsp;&emsp;在联合模型中，Wide和Deep部分的输出通过加权方式合并到一起，并通过logistic loss function进行最终输出。

&emsp;&emsp;<img align='center' width='380' height='40' src="picture/w&d_5.png" />

&emsp;&emsp;联合训练和模型集成要进行区分，他们有着以下两点区别：

- 训练方式。 集成模型的子模型部分是独立训练，只在inference阶段合并预测。而联合训练模型是同时训练同时产出的。
- 模型规模。集成模型独立训练，模型规模要大一些才能达到可接受的效果。而联合训练模型中，Wide部分只需补充Deep模型的缺点，即-记忆能力，这部分主要通过小规模的交叉特征实现。因此联合训练模型的Wide部分的模型特征较小。
联合模型求解采用FTRL算法，L1正则。深度部分用AdaGrad优化算法。

&emsp;&emsp;<img align='center' width='500' height='300' src="picture/w&d_6.png" />

### 总结
&emsp;&emsp;W&D由浅层（或单层）的Wide部分神经网络和深层的Deep部分多层神经网络组成，输出层采用softmax或logistics regression综合Wide和Deep部分的输出。Wide部分有利于增强模型的“记忆能力”，Deep部分有利于增强模型的“泛化能力”。

### 为什么Wide部分要用L1 FTRL训练？

&emsp;&emsp;简要介绍一下，你可以把FTRL当作一个稀疏性很好，精度又不错的随机梯度下降方法。由于是随机梯度下降，当然可以做到来一个样本就训练一次，进而实现模型的在线更新。所以在四五年前，大部分公司还是线性模型为主的时代，FTRL凭借非常好的在线学习能力成为主流。

&emsp;&emsp;说完了FTRL，再说L1正则化，参加过算法岗面试的同学可能都碰到过那个经典面试题“为什么L1正则化比L2正则化更容易产生稀疏解？”。问题的答案现在当然已经是显学了，但这里“稀疏”这个性质又冒出来了。也就是说FTRL with L1非常注重模型的稀疏性。这也就是问题的答案，W&D采用L1 FTRL是想让Wide部分变得更加稀疏。

&emsp;&emsp;再白话一点就是，L1 FTRL会让Wide部分的大部分权重都为0，我们准备特征的时候就不用准备那么多0权重的特征了，这大大压缩了模型权重，也压缩了特征向量的维度。

&emsp;&emsp;wide部分采用了两个id类特征的乘积，这两个id类特征是：User Installed App 和 Impression App

&emsp;&emsp;这篇文章是Google的应用商店团队Google Play发表的，我们不难猜测Google的工程师使用这个组合特征的意图，他们是`想发现当前曝光app和用户安装app的关联关系，以此来直接影响最终的得分`。但是两个id类特征向量进行组合，在维度爆炸的同时，会让原本已经非常稀疏的multihot特征向量，变得更加稀疏。正因如此，wide部分的权重数量其实是海量的。为了不把数量如此之巨的权重都搬到线上进行model serving，`采用FTRL过滤掉哪些稀疏特征`无疑是非常好的工程经验。

### 为什么Deep部分不特别考虑稀疏性的问题？

&emsp;&emsp;大家注意观察可以发现Deep部分的输入，要么是Age，#App Installs这些数值类特征，要么是已经降维并稠密化的Embedding向量，工程师们不会也不敢把过度稀疏的特征向量直接输入到Deep网络中。所以Deep部分不存在严重的特征稀疏问题，自然可以使用精度更好，更适用于深度学习训练的AdaGrad去训练。

### 再说回模型的泛化能力和记忆能力

&emsp;&emsp;我想到这应该把文首的问题回答清楚了。最后我想再说回所谓wide部分的“记忆能力”。其实大家可以看到，所谓的“记忆能力”，可以简单理解为发现“直接的”、“暴力的”、“显然的”关联规则的能力。比如该问题中，Google W&D期望在wide部分发现这样的规则：

&emsp;&emsp;用户安装了应用A，此时曝光应用B，用户安装的B概率大。而Deep部分就更黑盒一些，它把能想到的所有特征扔进这个黑盒去做函数的拟合，显然这样的过程会“模糊”一些直接的因果关系，泛化成一些间接的，可能的相关性。从这个角度来说，所谓“泛化能力”和“记忆能力”就更容易被直观的理解了。

