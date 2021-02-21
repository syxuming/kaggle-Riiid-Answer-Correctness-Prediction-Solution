# kaggle-Riiid-Answer-Correctness-Prediction-Solution
https://www.kaggle.com/c/riiid-test-answer-prediction


一、比赛介绍：
Riiid线上教育公司，需要通过学生过往的讲座记录和答题记录，来预测学生当前在回答的这道题目能不能做对。


二、比赛特殊说明

1.、建议先去kaggle看一下官方数据集的介绍 https://www.kaggle.com/c/riiid-test-answer-prediction/data

2.这次比赛提交方式是通过kaggle api一批一批的生成test data。目的是为了防止你利用test data的本身时间上的特性来预测，以防数据穿越。
test_data一共2500k行，每一批传过来的数据20行左右。

每一批传过来数据，包含当前批次的数据的特征，和上一批的数据的label。

3.本次比赛test data中的所有content_id（讲座和题目），都不会出现新的。但会出现新的user_id（模拟新用户进来的冷启动问题）。


三、代码运行前所需要的预备数据

1. https://www.kaggle.com/c/riiid-test-answer-prediction/data # kaggle官方比赛数据集

2. 链接：https://pan.baidu.com/s/1NLTXY_aaJUuUfLrM_uw9mg 提取码：5oxi # 其余数据，百度网盘


四、版本
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



五、代码包含四份文件

1.LGBM gen_data.ipynb 用作生成LGBM所需的特征

2.LGBM train.ipynb 用作训练LGBM模型

3.SAINT train.ipynb 用作训练SAINT模型

4.LGBM+SAINT inference 用作在kaggle上做预测
