### 通用框架示意图

Youtube DNN

<img align="center"  width='500' height='300' src="picture/pipeline_1.png"  />

- 物料表示数亿的商品、视频、博文、新闻等数据
- 将数以数据推送给用户是不现实的，所以第一个漏斗召回层将数亿变为数百条数据
- 再将数百条做精排，推送给用户
- 因为是千人千面，个性化体现在查询变量（上下文）和用户历史行为
- 特征和物料都抽象为key-value的键值对存在库中

### 召回示例说明

找到你可能关心的内容
<img align="center"  width='600' height='300' src="picture/pipeline2.png"  />

- 采用多路找回，如基于兴趣、位置、xx策略等召回
- 一般一路召回就一到二百，多路就是五百到一千条内容

### 排序示例说明

找到最可能关心的内容
<img align="center"  width='600' height='300' src="picture/pipeline3.png"  />

- 物料如视频、商品、博文等抽象出各级标签
- 用户的历史特征也会抽象出各级标签
- 通过算法，输入用户特征x以及粗召回的物料内容特征，得到转化率的预测（商品购买、视频阅读时长），0~1排序

### 总结

<img align="center"  width='700' height='400' src="picture/pipeline4.png"  />

- 内容理解（文本NLP、图片视频理解、语音理解）