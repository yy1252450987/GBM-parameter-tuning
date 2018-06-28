# Paramter Tuning for XGBoost
## XGBoost的优势
1. 正则化:
  * 标准的GBM没有正则化，因此XGBoost引入正则化可以帮助减少过拟合
  * 事实上，XGBoost也称作‘regularized boosting’技术
2. 并行:
  * XGBoost实现了并行过程，相比于GBC运算速度有了惊人的提升
  * 这里就不赘述它如何实现并行的
  * Hadoop也可以使用XGBoost
3. 高灵活性:
  * XGBoost可以自己定义优化目标和评价标准
4. 处理缺失值：
  * XGBoost有一套内建机制处理缺失值
  * XGBoost将缺失值作为一个参数，在不同结点会分裂到不同分支中，且测试时也归内到相同分支中。
5. 剪枝：
  * GBM当分裂时loss为负的时，将会停止分裂，因此更像是贪婪算法。
  * XBoost另一方面会根据max_depth进行分裂，并且进行树回溯开始剪枝，移除掉那些没有获得正增益的分裂。
  * 另一个优势就是如果出现-2的分裂,之后又一个+10的分裂情况时，GBM遇到-2的分裂的话就会停止分裂，但XGBoost会继续分裂，
  因为它发现有+8的增益，因此这两个分裂都会保留。
6. 内建CV：
  * 因为在boosting过程中，XGBoost允许用户每一步进行交叉验证，因此它单轮很容易获得准确的最优值。
  而GBM我们必须通过grid-search来搜索最佳参数。
7. 基于现有模型进行训练：
  * XGBoost可以基于之前迭代的结果继续训练，在某些情况下十分实用。sklearn的GBM也有这个特征，因此它们在这个点上时一样的。
## XGBoost 参数
1. General 参数：
1.1
1.2
1.3
1.4
2. Booster 参数：
3. Learning Task 参数
