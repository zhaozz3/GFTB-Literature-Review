## 传统 CTR 预测文献示例：DCN

**文献信息**

- **题目**：Deep & Cross Network for Ad Click Predictions
- **作者**：Ruoxi Wang, Bin Fu, Gang Fu, Mingliang Wang
- **年份**：2017
- **会议**：Proceedings of the ADKDD'17
- **研究主题**：CTR 预测；显式特征交互；cross network
- **推荐引用格式（APA）**：Wang, R., Fu, B., Fu, G., & Wang, M. (2017). Deep & cross network for ad click predictions. In *Proceedings of the ADKDD'17*.
- **备注**：这是“显式交互神经化”路线的关键经典论文之一。

DCN 的代表性在于，它不是简单把 embedding 丢进更深的 MLP，而是明确提出了 **cross network**，让网络层按可控方式显式构造有限阶特征交互。相比 Wide & Deep 中需要人工指定交叉特征，DCN 试图用网络结构自动学习这些交互；相比普通 DNN 只做隐式混合，DCN 则更强调“交互就是模型结构的一部分”。

## 核心创新与研究逻辑

DCN 回答的问题是：CTR 里很多有效信息来自字段之间的交互，但纯 DNN 学到的是隐式交互，既不高效，也未必容易覆盖关键的低阶到中阶交互。于是论文提出：

1. 先用 embedding 把高维稀疏输入转成低维向量；
2. 用 **cross network** 显式逐层构造特征交互；
3. 与普通 **deep network** 并行建模，再联合输出。

它的关键研究逻辑是：**显式交互和隐式交互不必二选一，可以在同一个网络里同时学。**

## 模型如何使用 / 结构说明

把 DCN 看成“embedding 之后分两路”就很好理解：
- `输入层`：多字段稀疏特征先做 embedding 和拼接。
- `cross 分支`：每一层都把当前表示与原始输入做乘性交互，显式生成更高阶组合。
- `deep 分支`：标准多层感知机，学习隐式复杂模式。
- `融合层`：把 cross 分支和 deep 分支输出拼接后，接最终预测层。

它的结构逻辑可以概括成一句话：**用 cross network 保证显式特征交互能力，用 deep network 补足更自由的非线性表达能力。**

## 关键术语

- **Cross Network**：DCN 中专门用于显式构造特征交互的网络分支。
- **Explicit Feature Interaction**：模型结构明确编码出的交互，而不只是让 DNN 隐式去学。
- **Embedding and Stacking Layer**：把稀疏离散输入映射并拼接成 dense 向量。
- **LogLoss**：CTR 概率预测质量指标。
- **Memory Efficiency**：在给定效果下使用更少参数。

## 数据集与研究设定

DCN 的数据设定是明确的，且比很多工业论文更具体：

- **Criteo Display Ads dataset**
  - 用于公开 CTR 评测
  - 论文特别说明：在这个数据集上，`0.001` 的 logloss 改进就已经很重要
- **Dense dataset**
  - 来源于 7 天用户日志
  - 规模约 **`41 million records`**

也就是说，这篇论文不是只在一个单一 benchmark 上评估，而是同时用了：
- 一个公开高维稀疏广告点击数据集 `Criteo`
- 一个规模约 4100 万样本的内部 dense 数据集

## 评估基线与实验设计

DCN 的主要比较对象包括：

- **LR**：逻辑回归
- **FM**：因子分解机
- **DNN**：去掉 cross 层后的深网络
- **DC**：Deep Crossing
- **DCN**：论文提出方法

论文不仅比较最终 logloss，还进一步比较：
- 达到某个目标 logloss 所需的参数量
- 在固定参数预算下，哪种模型性能更好
- 增加 cross layer 数量后，验证集 logloss 如何变化

这种实验设计很有价值，因为它不只证明“DCN 分数更好”，还证明它 **更省参数、更高效地学习交互**。

## 实验结果

论文 Table 1 给出的最佳 test logloss 如下：

| Model | Best Test LogLoss |
|---|---:|
| LR | 0.4474 |
| FM | 0.4464 |
| DNN | 0.4428 |
| DC | 0.4425 |
| **DCN** | **0.4419** |

这说明：
- DCN 相比 DNN 从 `0.4428` 降到 `0.4419`
- 相比 Deep Crossing 从 `0.4425` 降到 `0.4419`
- 相比 LR / FM 的提升更明显

论文 Table 2 还给出了达到目标 logloss 所需参数量：

| Target LogLoss | DNN 所需参数 | DCN 所需参数 |
|---|---:|---:|
| 0.4430 | `3.2 × 10^6` | `7.9 × 10^5` |
| 0.4460 | `1.5 × 10^5` | `7.3 × 10^4` |
| 0.4470 | `1.5 × 10^5` | `3.7 × 10^4` |
| 0.4480 | `7.8 × 10^4` | `3.7 × 10^4` |

这组结果非常重要，因为它说明：**为了达到相近 logloss，DCN 需要的参数量显著少于 DNN。**

论文 Table 3 也显示，在不同内存预算下 DCN 都优于 DNN。例如：
- `5 × 10^4` 参数预算下，DNN 为 `0.4480`，DCN 为 `0.4465`
- `2.5 × 10^6` 参数预算下，DNN 为 `0.4431`，DCN 为 `0.4423`

因此，这篇论文最准确的 benchmark 结论是：**DCN 不仅取得了最优 logloss，而且在参数效率上明显优于普通 DNN。**

## 指标评估与含义

### LogLoss

CTR 的 LogLoss 写作：

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

其中：
- `y` 是点击标签；
- `p` 是预测点击概率。

论文以 LogLoss 为核心指标，是因为 DCN 主要讨论的是概率预测质量与模型效率。

### 为什么论文强调参数预算

因为工业 CTR 系统不仅要“准”，还要“能部署”。如果两个模型精度接近，但一个模型用更少参数达到同等 logloss，那么它通常更容易训练、存储和上线。

## 文献价值与边界

DCN 的价值在于：

1. 它把 **显式特征交互** 变成了网络结构设计问题；
2. 它补上了 Wide & Deep 与纯 DNN 之间的重要一环；
3. 它用参数效率实验说明了：更好的交互建模不一定意味着更重的模型。

它的边界包括：

- 仍然主要聚焦结构层面的交互学习，没有触及后来的序列兴趣、多模态内容等扩展；
- 指标主打 logloss，没有像后续很多论文那样同时大规模展示 AUC、线上指标和解释性分析；
- cross network 设计后来还有很多变体，如 DCNv2 等，说明这条路线还在继续发展。
