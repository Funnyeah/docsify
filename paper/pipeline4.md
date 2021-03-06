# 机器学习

### 范式
<img align="center"  width='800' height='300' src="picture/pipeline17.png"  />

- 输出y为点击率、阅读时长、停留时长、购买转化率等

### 推荐引擎架构图-离线

<img align="center"  width='800' height='450' src="picture/pipeline18.png"  />

- 用户分为生产（发布）和消费（赞、买、评）等行为内容
- 生产会记录到物料库，消费会记录用户行为离线表到hive等仓库
- 通过用户行为日志的分析得到特征库，再到物料库
- 最重要的就是样本生成，通过hive sql和逻辑脚本生产

### 推荐引擎架构图-在线

<img align="center"  width='800' height='450' src="picture/pipeline19.png"  />

- 离线模型部署上线有一定周期，如几小时、几天等
- 对于时效性要求高的产品，如微博、短视频的热点变化十分快，需要及时捕获用户行为，并反馈到线上
- 优点在于及时反馈用户行为、更细粒度刻画用户、及时反馈内容变化，缺点会有性能压力
- 采用flink、storm等技术实现实时信息流 