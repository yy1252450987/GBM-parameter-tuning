# Gradien Boosting Machine Parameter Tuning

## GBM算法流程（二分类）：
1. Initialize the outcome
2. Iterate from 1 to total number of trees <br>
  2.1 Update the weights for targets based on previous run (higher for the ones mis-classified) <br>
  2.2 Fit the model on selected subsample of data <br>
  2.3 Make predictions on the full set of observations <br>
  2.4 Update the output with current results taking into account the learning rate <br>
3. Return the final output.

## GBM parameter:
1.	Tree-Specific Parameters: 这些参数会影响模型中的每个单独的树。
2.	Boosting Parameters: 这些参数会影响模型中的boosting过程。
3.	Miscellaneous Paramters: 对模型整体功能起作用。

![](https://www.analyticsvidhya.com/wp-content/uploads/2016/02/tree-infographic.png)

## Tree-Specific参数:
1.	min_samples_split: 
*	定义每个节点的最小划分样本数，若大于该值继续划分，反之则终止划分。
*	可用于控制过拟合，较高的值可以防止模型学习到的关系是对某些样本是高度特异化的。
*	特别高的值会造成欠拟合，应该通过CV进行调参。
2.	min_samples_leaf:
*	定义叶子结点的最小样本数。
*	类似于min_samples_split，可用于控制过拟合。
*	对于非平衡类问题，通常选择较小的值，因为类样本数较少的类占主导的区域是非常小的。
3.	min_weight_fraction_leaf:
*	类似于min_samples_leaf，但是定义的占所有样本数的比例，而不是一个整数。
*	只能定义min_samples_leaf或者min_weight_fraction_leaf的其中一个。
4.	max_depth:
*	定义树的最大深度。
*	可用于控制过拟合，因为深度过深的话，会让模型学习到的关系对某些样本具有高度特异性。
*	应该使用CV调参。
5.	max_leaf_nodes:
*	定义树的最大叶子结点数。
*	可用于替代max_depth，因为如果是个二叉树的话，深度为n的树最多只有2^n个叶子结点。
*	若定义了max_leaf_nodes，模型会忽视max_depth参数
6.	max_features:
*	定义搜寻最佳分裂时的最大特征数目，一般特征是随机选择。
*	经验之谈，当特征数目为所有特征数目总和的平方根时，模型效果较好，但是我们同样应该试试上限到30-40%的特征数时模型的效果。
*	较高的值为导致过拟合，但也和实际问题相关。

## Boosting 参数:
1.	learning_rate: 
*	该参数影响了每一棵树对最终结果的影响(step2.4)，GBM先初始化一个估计，然后通过每一棵树的结果进行更新。该参数就是控制估计值更新变化的程度(数量级)。
*	通常我们偏好较低的学习率，因为它可以让模型学习到一些特异性的特征并能很好的进行泛化。
*	学习率较低的话，需要更多的树去学习所有的关系因此计算耗费较高。
2.	n_estimators:
*	需要建模的树的数目。
*	尽管GBM的树数目较高时也能保持鲁棒性，但是它仍可能在某一点过拟合。因此在特定学习率下，需要通过CV进行调参。
3.	subsample
*	定义每一棵树选择多少比例的样本。通过随机采样进行选择。
*	值略微比1小可以减小方差从而使模型的鲁棒性增强。
*	值约为0.8左右性能较好，但是最好还是cv进行调参。
## Miscellaneous 参数:
1.	loss:
*	定义每一次分裂时需要最小化的损失函数。
*	对于分类和回归的不同问题会有所差异，通常默认就好，除非你知道自定义的函数对模型会有什么样的影响。
2.	init:
*	影响输出的初始结果。
*	适用于一个模型的输出是另外一个模型的输入的情况。
3.	random_state:
*	相同的随机种子会产生相同的结果。
*	这个对调参十分重要，如果不固定随机数，我们不同参数就无法进行性能上的比较了。
*	可能选取某个随机数会造成过拟合现象。我们可以选择不同的随机数进行建模，但是计算耗费高不建议使用。
4.	verbose:
*	模型拟合时，输出形式。0表示不输出，1表示按某一间隔输出，>1表示全部输出。
5.	warm_stat:
*	这个参数使用恰当可以帮助许多。
*	通过该参数，我们可以基于先前拟合好的模型再拟合其他的树，可以节省大量时间，更高级的应用可以自己探索。
6.	presort:
*	选择是否先对数据进行预先排序

## GBM调参流程:
1.	设定相对较高的learning_rate,通常为0.1，在某些问题上0.05-0.2都可以试试。
*	初始化其他参数：min_sample_split=500(总数的～0.5-1%)；min_samples_leaf=50(可主观设定，为了防止过拟合)；max_depth=8（通常5-8都可以）；max_feature=’sqrt’(经验)；subsample=0.8(最常用的值)
2.	在该learning_rate下，确定最佳的n_estimators，应该是40-70左右。记住要选择一个能跑的动的值，因为我们后续都是基于该值进行fine-tuning和测试的。
*	fine-tuning： 从20-80间隔10，gridsearch CV。
  *	如果最佳值在20附近，那么你应该降低learning_rate到0.05，然后重新gridsearch CV。
  *	如果最佳值在～100左右，那么你训练其他参数的时间会很长，所以你应该尝试更高的learning_rate。
3.	固定learning_rate和n_estimators，确定Tree-specific参数(按参数的重要性排序)。 <br>
3.1	max_depth和min_samples_split：
*	fine-tuning: 这一步可以根据你的电脑配置进行合并CV，max_depth从5-15间隔2，min_samples_split从200到1000间隔200，gridsearch CV。可设的更加宽泛或窄小。
  *	如果两个值都偏大的话，设置的更加宽泛一点。
  *	如果只有max_depth处于合理值附近，固定max_depth，min_samples_split可以和另外一个参数min_samples_leaf进行合并调参。
  *	如果两个值都很合理，直接选取就好了。 <br>
3.2.	min_samples_leaf
*	fine-tuning: 从30-70，间隔10，gridsearch CV。 <br>
3.3.	max_feature:
*	fine-tuning: 从7-19，间隔2，gridsearch CV。
4.	确定subsample，然后降低learning_rate并等比例的提高n_estimators获取鲁棒性更强的模型。 <br>
4.1	subsample:
*	fine-tuning: 从0.6-0.9，间隔0.05，gridsearch CV. <br>
4.2	learning_rate和n_estimatos
*	按不同的比例降低learning_rate和提高n_estimatos（0.5,0.1,0.05,0.01,0.005）

参考：https://www.analyticsvidhya.com/blog/2016/02/complete-guide-parameter-tuning-gradient-boosting-gbm-python/
