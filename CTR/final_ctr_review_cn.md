## 传统 CTR 预测文献示例：FINAL

**文献信息**

- **题目**：FINAL: Factorized Interaction Layer for CTR Prediction
- **作者**：Jieming Zhu, Qinglin Jia, Guohao Cai, Quanyu Dai, Jingjie Li, Zhenhua Dong, Ruiming Tang, Rui Zhang
- **年份**：2023
- **会议**：*Proceedings of the 46th International ACM SIGIR Conference on Research and Development in Information Retrieval (SIGIR 2023)*
- **DOI**：10.1145/3539618.3591988
- **研究主题**：CTR 预测；因式分解交互；高阶交互学习；高效显式交互
- **推荐引用格式（APA）**：Zhu, J., Jia, Q., Cai, G., Dai, Q., Li, J., Dong, Z., Tang, R., & Zhang, R. (2023). FINAL: Factorized interaction layer for CTR prediction. In *Proceedings of the 46th International ACM SIGIR Conference on Research and Development in Information Retrieval* (pp. 2006-2010). https://doi.org/10.1145/3539618.3591988
- **备注**：这是一篇短文形式但影响力较强的 2023 年 CTR 结构论文，重点在于提高显式特征交互学习的深度效率。

CTR 预测模型长期依赖两类主要机制来学习特征交互：一种是通过 MLP 隐式学习交互，另一种是通过专门的 cross 或 product 模块显式学习交互。Zhu 等（2023）指出，很多显式交互网络要么单独使用时效果有限，要么需要堆叠较深层数才能覆盖足够高阶的交互。为此，作者提出 **FINAL (Factorized Interaction Layer)**，目标是在较浅网络深度下更高效地扩展交互阶数。其核心思想受快速幂启发：不是线性堆叠许多普通交互层，而是通过 factorized 方式更快地产生高阶交互。

从创新角度看，FINAL 的重点在于改善显式交互学习中的 **depth-efficiency trade-off**。它不是简单增加层数或增加更多手工交互算子，而是设计一个能更高效生成高阶交互的层。其创新可以概括为：**在 CTR 预测中提出一种浅层但高阶的显式交互机制，从而兼顾交互能力与计算效率。**

## 核心创新与研究逻辑

FINAL 所回应的问题是：现有交互网络往往需要更深结构才能学习到足够高阶的交互，这会增加复杂度和部署成本。MLP 虽然能隐式学习交互，但对乘性模式不一定足够高效；而显式交互网络若要达到强效果，往往会变得很深。于是，作者提出 factorized interaction layer，希望让交互阶数增长得比普通堆叠模块更快。

其实现链条可概括为：
1. 对多字段稀疏输入进行 embedding；
2. 把 embedding 输入 factorized interaction blocks；
3. 通过分解与组合让交互阶数高效增长；
4. 聚合交互表示；
5. 通过 prediction head 输出 CTR 概率；
6. 在公开 benchmark 上与强基线模型对比。

## 模型如何使用 / 结构说明

把 FINAL 看成“浅层但高阶的显式交互网络”：
- `主干`：embedding 后不走普通 MLP，而走连续 factorized interaction blocks。
- `创新模块`：factorized block 让交互阶数增长快于线性堆叠。
- `关键理解`：它解决的是显式交互学习里的 depth-efficiency trade-off。

## 关键术语

- **CTR**：点击率，在建模时通常转化为点击概率预测。
- **Explicit feature interaction**：通过明确的结构直接构造跨字段交互，而非完全依赖隐式 MLP。
- **Factorized interaction**：通过分解和组合方式更高效地表示特征交互。
- **Interaction order**：一次交互中共同参与作用的特征数量层级。
- **AUC**：排序型评价指标。
- **LogLoss**：概率质量评价指标。

## 数据集与研究设定

FINAL 明确使用 **4 个公开 benchmark datasets**，并说明“复用 [4] 发布的预处理版本，遵循相同的数据切分与预处理流程”。具体数据集是：

- **Criteo**
- **Avazu**
- **MovieLens**
- **Frappe**

因此，这里不应写成“更广泛的 FuxiCTR / RecZoo 比较语境下通常围绕哪些数据”，而应直接写成：**论文实际评估就在这 4 个公开数据集上完成**。论文评价指标也写得很直接，使用的是：

- **AUC**
- **LogLoss**

这说明 FINAL 是非常标准的“结构化离线 CTR benchmark 对比”论文，而不是序列 CTR 或多模态 CTR 论文。

## 评估基线与实验设计

FINAL 的对比基线覆盖了一阶、二阶、三阶以及高阶交互模型。Table 1 中可直接看到的代表性基线包括：

- **LR**：`78.86 / 75.16 / 93.42 / 93.56`
- **FM**：`80.22 / 76.13 / 94.34 / 96.71`
- **AFM**：`80.44 / 75.74 / 94.72 / 96.97`
- **CrossNet**：`79.47 / 75.45 / 93.85 / 94.19`
- **CrossNetV2**：`81.10 / 76.05 / 95.83 / 97.16`
- **CIN**：`80.96 / 76.26 / 96.02 / 97.76`
- **HiNet**：`81.39 / 76.49 / 96.97 / 98.45`
- **SAM**：`81.31 / 76.32 / 96.31 / 98.01`

这些数字按顺序分别对应 **Criteo / Avazu / MovieLens / Frappe**。这种对比设计是合理的，因为 FINAL 的问题意识非常清楚：它要证明一种更高效的显式交互层，能否超过现有显式交互和混合交互结构。

## 实验结果

原论文中的主 benchmark 表如下：

![benchmark](figures/final_benchmark_clean.png)

*Benchmark 图示说明：该图截取自 FINAL 原论文的 Table 1。*

从表中可以直接读到，最强的 **FINAL(2B)+KD** 在 **Criteo / Avazu / MovieLens / Frappe** 上分别达到：

- **81.56**
- **76.67**
- **97.20**
- **98.95**

如果和表中较强基线直接比，可以更明确看到提升幅度：

- 相比 **CrossNetV2** 的 `81.10 / 76.05 / 95.83 / 97.16`，FINAL(2B)+KD 全部更高
- 相比 **HiNet** 的 `81.39 / 76.49 / 96.97 / 98.45`，FINAL(2B)+KD 仍然全部更高
- 相比 **SAM** 的 `81.31 / 76.32 / 96.31 / 98.01`，FINAL(2B)+KD 同样全部更高

| 模型 | Criteo | Avazu | MovieLens | Frappe |
|---|---:|---:|---:|---:|
| CrossNetV2 | 81.10 | 76.05 | 95.83 | 97.16 |
| HiNet | 81.39 | 76.49 | 96.97 | 98.45 |
| SAM | 81.31 | 76.32 | 96.31 | 98.01 |
| **FINAL(2B)+KD** | **81.56** | **76.67** | **97.20** | **98.95** |

因此，FINAL 的结论不应只写成“总体更强”，而应更精确地写成：**FINAL(2B)+KD 在 4 个公开 CTR benchmark 上都取得了表中最优结果，并且对 CrossNetV2、HiNet、SAM 等强基线形成了稳定优势。**

## 指标评估与含义

### AUC 的计算逻辑

AUC 可以理解为：

`AUC = P(s+ > s-)`

其中 `s+` 为点击样本得分，`s-` 为未点击样本得分。

### LogLoss 的计算逻辑

对于二分类点击预测：

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

LogLoss 越低，说明预测概率越接近真实结果。

### 为什么这套评价逻辑适合 FINAL

由于 FINAL 是一篇交互学习论文，关键问题是改进交互层之后是否能同时提升：
- 排序能力（AUC）
- 概率预测质量（LogLoss）

## 文献价值与边界

FINAL 对本项目的价值在于，它是近期传统 CTR 文献中一个很纯粹的“预测结构创新”代表：重点依然是如何更好地学习高阶特征交互。这有助于界定本项目的传统预测侧背景。

但它的局限也非常清楚。FINAL 不会生成因子级自然语言解释，也不涉及 textual feedback 或 prompt optimization。因此，它能帮助界定传统 CTR baseline 空间，却不能直接支持本项目在解释与优化循环上的创新。
