# 天池竞赛：天猫用户复购预测

这是天池的一个长期赛，用的是脱敏的天猫数据。这个竞赛的出发点是是商家期望能够确定已经原来购买过的用户哪些是可能的潜在复购者，并对这些潜在的忠诚用户提供精细化营销，以降低运营成本。提供的数据包括用户，商家，商品，品牌以及种类等多种特征，期望能够用提供的数据集预测用户在未来六个月内可能复购的可能性。
这个赛题可以抽象为一个对应user和merchant的一个二分类问题，所以可供选择的模型较多。  
竞赛地址以及数据集在[这里](https://tianchi.aliyun.com/competition/entrance/231576/introduction). 

1. 特征工程：本次竞赛的特征工程部份主要对用户和商家建立画像，然后做了一些特征交叉。
2. 模型选择：xgboost，lightgbm，DIN
3. 最终结果：xgboost（0.68017），lightgbm(0.68094)

模型调参过程中用了GridSearchCV来寻找最优参数，另对xgboost和lightgbm的调参步骤略有不同。

**xgboost调参步骤**：  
1.先确定eta（学习率），0.01到0.3之间选取合适较大值。
2.调整max_depth，控制树深度，初始10，也可以在5-10之间网格搜索最佳。
3.调整subsample，控制样本抽样率，在0.3-1之间网格搜索最佳。
4.调整min_child_weight，越大模型越保守。
5.调整colsample_bytree,特征子抽样，在0.3-1之间网格搜索。
6.再次降低eta求精确模型。

**lightgbm调参步骤**：
1. 先确定learning_rate，一般都在0.01到0.3之间选，可刚开始选较大便于快速收敛；n_estimators为迭代数量，这个根据经验调整，可用早停法截取。
2. 调整max_depth和max_leaves，这两个控制树深度和模型复杂度，一般后者小于$2^{max_depth}$.
3. 调整min_child_sample和min_child_weight,这两个也是控制过拟合用的。
4. 调整colsample_bytree和subsample，特征子抽样和样本采样，也是空值过拟合用。
5. 调整reg_alpha和reg_lambda，分别是L1和L2正则，控制过拟合。
6. 再次降低learning_rate求的最精确模型。
