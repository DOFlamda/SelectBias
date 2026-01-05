论文中标明了代码存放于https://github.com/HowardZJU/CFR-Pro，但实际这个仓库是空的。

涉及的数据集：两个半合成数据集，[ACIC2016挑战赛数据](https://github.com/BiomedSciAI/causallib/blob/master/causallib/datasets/data/acic_challenge_2016/README.md)与IHDP数据集，前者ACIC的GitHub库中获取，包括4802个观察值和58个协变量；后者kaggle竞赛获取，包括25个协变量的747个观察。所有数据集都被随机打乱，并以0.7：0.15：0.15的比例划分为训练、验证和测试集，在所有拆分中保持相同的处理单元比例，以确保数字可靠性。
