## 传统 CTR 预测文献示例：SIFAN

**文献信息**

- **题目**：Session interest model for CTR prediction based on feature co-action network
- **作者**：Qianqian Wang, Fang'ai Liu, Xiaohui Zhao, Qiaoqiao Tan
- **年份**：2025
- **期刊**：*Scientific Reports*, 15, 14571
- **DOI**：10.1038/s41598-025-99671-9
- **研究主题**：CTR 预测；session interest；行为序列；feature co-action network
- **推荐引用格式（APA）**：Wang, Q., Liu, F., Zhao, X., & Tan, Q. (2025). Session interest model for CTR prediction based on feature co-action network. *Scientific Reports, 15*, 14571. https://doi.org/10.1038/s41598-025-99671-9
- **备注**：这篇工作把 session 级兴趣建模与历史-目标间特征协同作用建模放到了同一框架里。

SIFAN 试图解决两个问题：一是很多 CTR 模型把历史行为看成一条无差别长序列，没有处理 session 边界；二是历史行为与目标物品之间的显式特征协同作用经常被弱化。作者因此提出 **SIFAN**，同时引入 `session interest extractor` 和 `feature co-action network (FAN)`。

## 核心创新与研究逻辑

SIFAN 的最大创新可以概括为：**在同一个 CTR 模型里，同时建模 session 级兴趣演化与历史-目标的显式 feature co-action。**

它的实现逻辑是：
1. 输入用户画像、场景画像、目标物品和历史行为序列；
2. 用 `FAN` 学习单个历史行为与目标物品之间的特征协同作用；
3. 把历史行为按 session 划分，并用 `GRU + AUGRU` 提取 session interest；
4. 融合两类表示输出 CTR 概率。

## 模型如何使用 / 结构说明

把 SIFAN 看成“两条协同分支”最清楚：
- `主干`：一条分支学 session 兴趣演化，一条分支学历史-目标显式 co-action。
- `创新模块`：session 结构和显式特征协同都被保留在结构里，而不是混成一个黑盒序列向量。
- `关键理解`：它解决的是长行为序列被过度压扁的问题。

## 关键术语

- **Session Interest**：按 session 划分的兴趣表示，而不是单条长序列兴趣。
- **FAN**：Feature Co-Action Network，显式建模历史行为和目标物品之间的特征协同作用。
- **AUGRU**：带 attentional update gate 的 GRU，用于更好地把兴趣与目标广告对齐。
- **AUC / LogLoss / RMSE**：论文同时报告的三类评价指标。

## 数据集与研究设定

论文 `Table 1` 给出了 4 个数据集的明确统计，并说明统一按 **80% 训练集** 划分；另外文字里说“according to different tasks”，但最明确、可直接引用的信息是这些数据集规模：

| 数据集 | customers | items | features | samples |
|---|---:|---:|---:|---:|
| Books | 46,375 | 43,728 | 49,852 | 277,303 |
| Electronics | 29,209 | 34,816 | 35,784 | 194,771 |
| Avazu | 78,437 | 69,175 | 104,242 | 830,163 |
| Criteo | 28,625 | 30,216 | 43,127 | 196,329 |

也就是说，SIFAN 不是只在 Amazon 行为数据上验证，而是同时用了：
- `Amazon Books`
- `Amazon Electronics`
- `Avazu`
- `Criteo`

这种设计的意义在于：它同时覆盖了**行为序列更强的电商场景**与**经典广告 CTR 场景**。

## 评估基线与实验设计

论文主对比模型包括：
- `DIN`
- `DIEN`
- `DSIN`
- `CAN`
- `CTRL`
- `DONN`

这组基线主要围绕序列兴趣建模、session 建模和特征交互增强展开，和 SIFAN 的研究问题是直接对齐的。

## 实验结果

原论文 `Table 2` 和 `Table 3` 给出的主结果如下。

### LogLoss（Table 2）

| 模型 | Books | Electronics | Avazu | Criteo |
|---|---:|---:|---:|---:|
| DIN | 0.1382 | 0.1406 | 0.2951 | 0.3625 |
| DIEN | 0.1313 | 0.1354 | 0.2836 | 0.3587 |
| DSIN | 0.1142 | 0.1225 | 0.2472 | 0.3473 |
| CAN | 0.1094 | 0.1184 | 0.2289 | 0.3366 |
| CTRL | 0.0997 | 0.1152 | 0.2176 | 0.3262 |
| DONN | 0.0962 | 0.1079 | 0.2101 | 0.3127 |
| **SIFAN** | **0.0873** | **0.1023** | **0.2052** | **0.3004** |

### RMSE（Table 3）

| 模型 | Books | Electronics | Avazu | Criteo |
|---|---:|---:|---:|---:|
| DIN | 0.4527 | 0.5245 | 0.4725 | 0.5825 |
| DIEN | 0.4476 | 0.4913 | 0.4601 | 0.5797 |
| DSIN | 0.4434 | 0.4876 | 0.4579 | 0.5634 |
| CAN | 0.4321 | 0.4725 | 0.4432 | 0.5468 |
| CTRL | 0.4252 | 0.4643 | 0.4402 | 0.5346 |
| DONN | 0.4063 | 0.4431 | 0.4215 | 0.5105 |
| **SIFAN** | **0.3756** | **0.4032** | **0.4001** | **0.4936** |

也就是说，SIFAN 并不是旧稿那种“整体更优”的泛化表述，而是**在 4 个数据集、两张主表上都给出了明确最优值**。这比“优于基线”要严谨得多。

需要补一句：论文正文还提到 `Fig. 4` 展示 AUC，但当前可直接抽取的 PDF 文本里并没有像 Table 2/3 那样稳定、整齐的 AUC 数字表，所以综述里最稳妥的写法是把 **LogLoss 与 RMSE 主表数值**作为核心证据，而不是凭图估 AUC。

## 指标评估与含义

### AUC 的计算逻辑

`AUC = P(s+ > s-)`

衡量排序能力。值越大，说明点击样本越可能被排在未点击样本前面。

### LogLoss 的计算逻辑

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

衡量预测概率与真实点击标签的一致性，越低越好。

### RMSE 的计算逻辑

`RMSE = sqrt( (1/|T|) * Σ_i (y_i - ŷ_i)^2 )`

论文把它作为整体预测误差指标使用，值越低越好。

### 为什么 SIFAN 同时报这三类指标

因为它既想验证：
- 排序是否改善；
- 概率估计是否更准；
- 整体预测误差是否下降。

对于一个同时涉及 session 兴趣和特征 co-action 的模型来说，这样的多指标报告是合理的。

## 文献价值与边界

SIFAN 的价值在于，它代表了较新的 CTR 文献已经不再满足于静态字段交互，而是进一步显式引入 **session-aware interest** 和 **history-target co-action**。这对于说明 CTR predictor 的信息结构越来越复杂很有帮助。

但它仍然是传统预测模型：不会输出自然语言解释，不提供因子级 explanation，也不会把误差信号转成可读反馈去优化系统。因此，它适合做现代 CTR predictor 背景文献，不是解释生成或 LLM 优化方法文献。
