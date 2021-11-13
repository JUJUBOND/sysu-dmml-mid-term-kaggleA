# sysu-dmml-mid-term-kaggleA
sysu dmml mid-term kaggleA
[TOC]



### 基本模型性能

| 单模     | 五次五折 | 验证集  | 预测集  |
| -------- | -------- | ------- | ------- |
| NN       | 0.91391  | 0.91036 | 0.91191 |
| RF       | 0.9257   | 0.92699 | 0.92719 |
| XGB      | 0.9302   |         |         |
| ADA      | 0.9293   |         |         |
| GB       | 0.9303   |         |         |
| ET       | 0.9297   |         |         |
| **聚合** |          |         |         |
| softvote | 0.92279  | 0.93069 | 0.92662 |
| stack    | 0.93341  | 0.93808 | 0.93647 |

这里stack是细调后达到的验证与预测总体最优效果，有的模型并未深入调。

### 基本思路

- 数据探索：这里应该是老师预处理后的数据所以无缺失，主要集中在看数据分布和可视化。
- 特征选择：借助RF模型进行特征重要性排序来选择，以及透视表与可视化找出可能没用的特征。
- 降维处理：用Vif和数据的描述对某些特征合成，减少后续尝试耗费的时间。
- 模型选择：准备一堆不同类型和不同参数的模型，dropout可能没用的特征进行尝试，保留表现好的若干个模型的参数和特征选择，最后用Pipeline实现聚合。
- 模型调试：按单模$$\rightarrow$$stack中的lr顺序调，lr一般有些过拟合。

### 可能的改进

- 特征构造：设想缺货多出现在第一次补货前和第二次补货前，可视化发现缺货多出现在四指标略低于零的区域，后续直接用原参数和特征组合大概少0.0007，可能调一下会比只排列组合原始特征表现好。但也可能是我理解有问题，lead_time认为是周数可能会表现得更精确（前文认为是月数）。
- 更复杂的stack：用多个lr随机连接基分类器，再接个lr嗯拟合，或者直接用NN也行。

File descriptions

train.csv - the training set
test.csv - the test set
sampleSubmission.csv - a sample submission file in the correct format

The dataset is historical data, which contains information of an anonymous company’s products for the 8 weeks prior to the week that is going to be predicted.
Data fields

sku - Stock keeping unit, a unique product identifier
national_inv - Current inventory level for the product
lead_time - Transit time for the product
in_transit_qty - Amount of the product in transit from source
forecast_3_month - Forecast sales for the next 3 months
forecast_6_month - Forecast sales for the next 6 months
forecast_9_month - Forecast sales for the next 9 months
sales_1_month - Sales quantity for the prior 1 month
sales_3_month - Sales quantity for the prior 3 months
sales_6_month - Sales quantity for the prior 6 months
sales_9_month - Sales quantity for the prior 9 months
min_bank - Minimum recommend amount to stock
potential_issue - Source issue for part identified; 1 indicates that some issues are identified, and 0 indicates no issue
pieces_past_due - Amount overdue from source
perf_6_month_avg - Source performance for the prior 6 months
perf_12_month_avg - Source performance for the prior 12 months
local_bo_qty - Amount of stock orders overdue
deck_risk - Part risk flag; 1 indicates risk identified, and 0 indicates no risk
oe_constraint - Part risk flag; 1 indicates risk identified, and 0 indicates no risk
ppap_risk - Part risk flag; 1 indicates risk identified, and 0 indicates no risk
stop_auto_buy - Part risk flag; 1 indicates risk identified, and 0 indicates no risk
rev_stop - Part risk flag; 1 indicates risk identified, and 0 indicates no risk
went_on_back_order - Product actually went on backorder. This is the target value.

