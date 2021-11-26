# kaggle-Riiid-Answer-Correctness-Prediction-Solution
https://www.kaggle.com/c/riiid-test-answer-prediction



## 比赛介绍：

Riiid线上AI教育公司，提供个性化/千人千面的教育服务，本次竞赛需要通过学生过往的讲座记录和答题记录，来预测学生当前在回答的这道题目是否能够答对。

官方此前已经做了很多研究，发了两篇论文。
SAKT      https://arxiv.org/abs/1907.06837 
SAINT     https://arxiv.org/abs/2002.07033  Riiid团队
SAINT+   https://arxiv.org/abs/2010.12042  Riiid团队



## 比赛trick点

### 传统机器学习和深度学习的结合
比赛一开始我们采用了传统的LGBM做分类，达到了铜牌的水平。
之后我们采用了论文中的SAINT+模型，并加以改进后，对lgbm和saint+进行ensemble后得到了最终的银牌，前2%。
比赛结束后我们发现单saint+结果就可以到达银牌（前3%～4%）

### lag time特征
我们自定义的saint+和论文中最大的不一样，来自我们对lag time进行了分箱处理，按秒、分、天做了三组lag time。
原因是Lag time 往往时间跨度大，对lag做分箱效果更好。


### 全局的groupby 导致数据泄漏
比赛一开始我们在计算用户的准确率、答题数量的统计时，使用了全局的groupby导致了未来时序中的数据穿越了
后来我们改进成了，对每一行当前时间节点之前的数据统计，提升了很多分数。

### 删除了lecture
我们在lgbm中使用lecture数据，并提升了分数，但是在saint+中，lecture特征并没有带来帮助，我们在saint+便没有使用lecture


### test数据传递方式
这次比赛提交方式是通过kaggle api一批一批的生成test data。目的是为了防止你利用test data的本身时间上的特性来预测，以防数据穿越。
test_data一共2500k行，每一批传过来的数据20行左右。
每一批传过来数据，包含当前批次的数据的特征，和上一批的数据的label。

### 冷启动
本次比赛test data中的所有content_id（讲座和题目），都不会出现新的。但会出现新的user_id（模拟新用户进来的冷启动问题）。


## 模型结构

### 输入特征
1. content_id
2. answered_correctly
3. part
4. prior_question_elapsed_time
5. prior_question_had_explanation
6. lag_time1 - convert time to seconds. if lag_time1 >= 300 than 300.
7. lag_time2 - convert time to minutes. if lag_time2 >= 1440 than 300 (one day).
8. lag_time3 - convert time to days. if lag_time3 >= 365 than 365 (one year).
I found lag time split to different time format boosting score around 0.003.

### Transformer
#### Encoder Input
1. question embedding
2. part embedding
3. position embedding
4. prior question had explanation embedding

#### Decoder Input
1. position embedding
2. reponse embedding
3. prior elapsed time embedding
4. lag_time1 categorical embedding
5. lag_time2 categorical embedding
6. lag_time3 categorical embedding
Note that I have tried categorical and continuous embedding in prior elapsed time and lag time. The performance of categorical embedding is better than continuous embedding.

### 参数
1. max sequence: 100
2. d model: 256
3. number of layer of encoder: 2
4. number of layer of decoder: 2
5. batch size: 256
6. dropout: 0.1
7. learning rate: 5e-4 with AdamW




## 代码运行前所需要的预备数据

1. https://www.kaggle.com/c/riiid-test-answer-prediction/data # kaggle官方比赛数据集

2. 链接：https://pan.baidu.com/s/1NLTXY_aaJUuUfLrM_uw9mg 提取码：5oxi # 其余数据，百度网盘



## 版本
python 3.8

numpy==1.19.2

pandas==1.1.5

sklearn==0.24.0

lightgbm==3.0.0

joblib==1.0.0

tqdm==4.54.1

matplotlib==3.2.1

pytorch==1.7.0+cu110

logging==0.5.1.2

psutil==5.7.2




## 代码包含四份文件

1.LGBM gen_data.ipynb 用作生成LGBM所需的特征

2.LGBM train.ipynb 用作训练LGBM模型

3.SAINT train.ipynb 用作训练SAINT模型

4.LGBM+SAINT inference 用作在kaggle上做预测

## TL;DR
Riiid线上AI教育公司，提供个性化的教育服务，竞赛需要通过学生过往的讲座记录和答题记录，来预测学生当前在回答的这道题目是否能够答对。我们采用了SAINT+模型，并加以改进后，对lgbm和saint+进行ensemble后得到了最终的银牌。
