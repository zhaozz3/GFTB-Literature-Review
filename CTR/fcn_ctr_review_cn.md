## 传统 CTR 预测文献示例：FCN

**文献信息**

- **题目**：FCN: Fusing Exponential and Linear Cross Network for Click-Through Rate Prediction
- **作者**：Honghao Li, Yiwen Zhang, Yi Zhang, Hanwei Li, Lei Sang, Jieming Zhu
- **年份**：2024（arXiv 版本）；论文页眉所示会议版本为 KDD 2026
- **来源**：arXiv preprint / KDD 2026 版本 PDF
- **研究主题**：CTR 预测；显式特征交互；cross network；高阶交互噪声控制
- **推荐引用格式（APA）**：Li, H., Zhang, Y., Zhang, Y., Li, H., Sang, L., & Zhu, J. (2024). FCN: Fusing exponential and linear cross network for click-through rate prediction. arXiv. https://arxiv.org/abs/2407.13349
- **备注**：这篇工作沿着 DCN 系列继续推进，目标是在不依赖 DNN 的情况下把显式交互做深、做强。

FCN 的问题意识非常明确：传统 DCN 类模型虽然高效，但线性交叉的表达能力有限；而更复杂的高阶交互若处理不当，又会带来参数冗余和噪声。作者因此提出 **FCN**，把 **Linear Cross Network (LCN)** 与 **Exponential Cross Network (ECN)** 融合起来，在同一个显式交互框架中同时覆盖低阶与高阶特征组合。

## 核心创新与研究逻辑

FCN 的最大创新不是“再加一个 DNN 分支”，而是反过来尝试**只用显式交互也做到 SOTA**。论文的三点关键设计是：

- **ECN**：让交互阶数随网络深度呈指数增长，而不是 DCN 风格的线性增长。
- **LCA（Low-cost Aggregation）**：把 cross network 中聚合步骤的参数空间大幅压缩，论文报告参数量约减半、推理时延约降 23%。
- **Tri-BCE**：给不同交互子网络提供更合适的监督信号。

实现流程可以概括为：
1. 多字段离散/数值特征先做 embedding；
2. 一条 **LCN** 分支学习低阶、稳定的线性交互；
3. 一条 **ECN** 分支学习指数增长的高阶显式交互；
4. 通过并行或串并混合方式形成 `FCN_p` / `FCN_sp`；
5. 使用 Tri-BCE 训练并输出 CTR 概率。

## 模型如何使用 / 结构说明

把 FCN 看成“双交叉主干”最准确：
- `主干`：两条分支都属于显式交互网络，而不是一条显式一条 DNN。
- `创新模块`：`LCN + ECN` 共同承担低阶与高阶交互学习。
- `关键理解`：作者想证明，强化显式交互本身也能打到很强效果。

## 关键术语

- **LCN**：Linear Cross Network，线性增长的显式交互分支。
- **ECN**：Exponential Cross Network，指数增长的显式交互分支。
- **LCA**：Low-cost Aggregation，用更低成本完成 cross aggregation。
- **Tri-BCE**：为多子网络设计的 BCE 类损失。
- **AUC**：排序能力指标。
- **LogLoss**：概率质量指标。

## 数据集与研究设定

这篇论文评测的是 **6 个公开 CTR benchmark**，不是当前旧稿里写的 `Amazon Electronics / KuaiRand`。原论文 `Table 2` 明确给出的数据集与统计如下：

| 数据集 | #Instances | #Fields | #Features |
|---|---:|---:|---:|
| Avazu | 40,428,967 | 24 | 3,750,999 |
| Criteo | 45,840,617 | 39 | 910,747 |
| ML-1M | 739,012 | 7 | 9,751 |
| KDD12 | 141,371,038 | 13 | 4,668,096 |
| iPinYou | 19,495,974 | 16 | 665,765 |
| KKBox | 7,377,418 | 13 | 91,756 |

预处理方面，论文说明：
- Avazu 的时间戳被拆成 `hour / weekday / weekend` 三个字段；
- Criteo 和 KDD12 的数值特征按 `⌊log2(x)⌋` 离散化；
- 稀有类别被替换为 `OOV`，阈值依数据集而异；
- 指标统一报告 `AUC` 与 `LogLoss`。

因此，FCN 是一个**在 6 个公开 benchmark 上、覆盖大规模稀疏广告数据与中等规模推荐数据的系统性实验**，而不是只在某个单一数据集上验证。

## 评估基线与实验设计

论文总共比较了 **16 个代表性基线**，包括：
- `DNN`
- `PNN`
- `Wide & Deep`
- `DeepFM`
- `DCNv1`
- `xDeepFM`
- `AutoInt*`
- `AFN*`
- `DCNv2`
- `EDCN`
- `MaskNet`
- `EulerNet`
- `FinalMLP`
- `FINAL`
- `RFM`
- `DLF`

这套基线覆盖了从经典 DNN/FM 路线到近年的 cross-network、attention、MLP、multiplicative interaction 模型，因此足以回答论文的核心问题：**只靠显式交互，能否打赢“显式 + 隐式”混合模型？**

## 实验结果

原论文 `Table 3` 的主结果非常具体。最强 FCN 变体在 6 个数据集上的成绩如下：

| 模型 | Avazu | Criteo | ML-1M | KDD12 | iPinYou | KKBox |
|---|---|---|---|---|---|---|
| `FCN_sp` AUC | **79.72** | **81.63** | 90.67 | 80.96 | **78.52** | **85.74** |
| `FCN_sp` LogLoss | **0.3693** | **0.4357** | 0.3017 | **0.1494** | **0.005529** | 0.4754 |
| 最强基线 AUC | 79.43 (MaskNet) | 81.49 (FINAL) | 90.56 (DCNv2) | 80.81 (DLF) | 78.30 (EulerNet) | 85.35 (xDeepFM) |

论文还给出 `Abs.Imp`：
- Avazu：AUC `+0.29`，LogLoss `-0.0018`
- Criteo：AUC `+0.14`，LogLoss `-0.0014`
- ML-1M：AUC `+0.26`，LogLoss `-0.0060`
- KDD12：AUC `+0.18`，LogLoss `-0.0003`
- iPinYou：AUC `+0.22`，LogLoss `-0.000010`
- KKBox：AUC `+0.39`，LogLoss `-0.0019`

作者总结为：FCN 在 6 个 benchmark 上都达到最优，平均 AUC 提升约 `0.25%`，平均 LogLoss 下降约 `0.19%`。对于 CTR 论文来说，这已经是很强的、可核对的主表级证据。

## 指标评估与含义

### AUC 的计算逻辑

`AUC = P(s+ > s-)`

表示随机取一个正样本和一个负样本时，模型把正样本排在更前面的概率。CTR 排序任务里，AUC 越高越好。

### LogLoss 的计算逻辑

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

其中 `y` 是真实点击标签，`p` 是预测点击概率。LogLoss 越低，说明模型的概率估计越可靠。

### 为什么这套指标适合 FCN

因为 FCN 是典型的 CTR 结构改进论文：
- `AUC` 用来判断排序是否更好；
- `LogLoss` 用来判断输出概率是否更准；
- 两者同时提升，才能说明新的显式交互结构既改善排序，也改善概率校准。

## 文献价值与边界

FCN 的价值在于，它说明到 2024/2026 这一代工作里，CTR 文献仍在继续深挖**显式特征交互结构**，而且已经出现“弱化 DNN 依赖、直接把 explicit cross 做到更深”的思路。它很适合作为现代 CTR predictor 结构演化的代表文献。

它的边界也很清楚：FCN 不提供自然语言解释，不输出因子级 explanation，更不涉及 textual feedback 或 prompt optimization。因此，它能支撑的是“预测器如何更强”，而不是“解释如何更忠实、如何基于解释再优化”。
