[CatBoost, XGBoost, LightGBM](https://mayuanucas.github.io/xgboost-lightgbm/#catboost)

### One_hot encoder

按照CatBoosting官方建议，不在数据预处理时进行转换，使用内置编码。

参数套路：https://zhuanlan.zhihu.com/p/136697031

XGBoost、LightGBM、CatBoost： https://mayuanucas.github.io/xgboost-lightgbm/#catboost





文献综述中都只采用ROC曲线对模型进行评估，但是这是一个数据失衡的数据集，ROC乐观的结果无法证实反应模型性能，使用Confusion metrics