## 传统 CTR 预测文献示例：FinalMLP

**文献信息**

- **题目**：FinalMLP: An Enhanced Two-Stream MLP Model for CTR Prediction
- **作者**：Kelong Mao, Jieming Zhu, Liangcai Su, Guohao Cai, Yuru Li, Zhenhua Dong
- **年份**：2023
- **会议**：*Proceedings of the AAAI Conference on Artificial Intelligence (AAAI 2023)*
- **DOI**：10.1609/aaai.v37i4.25577
- **研究主题**：CTR 预测；双流架构；MLP；特征门控；双线性融合
- **推荐引用格式（APA）**：Mao, K., Zhu, J., Su, L., Cai, G., Li, Y., & Dong, Z. (2023). FinalMLP: An enhanced two-stream MLP model for CTR prediction. *Proceedings of the AAAI Conference on Artificial Intelligence, 37*(4), 4552-4560. https://doi.org/10.1609/aaai.v37i4.25577
- **备注**：这是一篇有代表性的 2023 年 CTR 方法论文，核心在于重新审视双流 CTR 建模中 MLP 的潜力。

传统 CTR 预测研究通常认为，单纯的 MLP 不足以有效学习复杂的乘性交互，因此许多双流模型都会把一个 MLP 分支与一个显式交互分支搭配使用。Mao 等（2023）对这一假设提出了挑战。作者认为，过去的工作可能低估了“设计得当的双 MLP 架构”本身的能力，因此提出 **FinalMLP**：两个分支都使用 MLP，而通过 **feature gating module** 和 **interaction aggregation module** 来完成输入差异化与跨流融合。换言之，这篇论文并不是简单换了一个 backbone，而是在重新讨论双流 CTR 模型到底应该如何设计。

从创新角度看，FinalMLP 的关键贡献是指出：CTR 性能提升不一定依赖更复杂的显式交互算子，也可以通过更好地设计双流输入差异和跨流融合来实现。其创新可以概括为：**通过特征门控和组级双线性融合增强双 MLP 结构，使一个相对简单的双流架构也能成为强 CTR 基线。**

## 核心创新与研究逻辑

FinalMLP 所回应的问题是：以往很多双流 CTR 模型把创新重点放在“显式交互分支”上，却默认 MLP 分支只是一个配角。作者认为，这一假设并没有被重新充分验证。于是，论文的核心思路变成：如果让两个 MLP 分支接收差异化输入，并在输出端做有效融合，是否也可以达到甚至超过更复杂的双流交互模型。

其实现链条可概括为：
1. 将多字段稀疏输入映射成 embedding；
2. 利用 feature gating 模块生成两个分支各自关注的输入表示；
3. 将差异化输入送入两个并行的 MLP 分支；
4. 通过 group-wise bilinear fusion 聚合跨流交互；
5. 将融合表示送入输出层预测 CTR；
6. 在多个公开 benchmark 和在线 A/B 测试中验证设计有效性。

## 模型如何使用 / 结构说明

把 FinalMLP 看成“受控输入差异化 + 双 MLP 融合”：
- `主干`：两条表示学习分支都用 MLP。
- `创新模块`：feature gating 先让两条流看到不同输入，再用双线性融合建模跨流交互。
- `关键理解`：它的亮点不是复杂 cross 网络，而是把双 MLP 结构做强。

## 关键术语

- **CTR**：点击率，建模时通常表示为一次展示被点击的概率。
- **Two-stream model**：由两个并行分支组成的模型，两条分支从不同视角学习特征关系。
- **Feature gating**：通过门控机制为不同分支生成差异化输入。
- **Bilinear fusion**：通过双线性方式显式建模两个表示之间的乘性交互。
- **Embedding**：把稀疏离散特征映射到低维稠密向量空间的表示方式。
- **AUC**：衡量排序能力的指标。
- **LogLoss**：衡量概率预测质量的指标。

## 数据集与研究设定

FinalMLP 在 **4 个公开 benchmark** 上做离线实验，论文 Table 1 还给出了明确的数据规模统计：

| Dataset | #Instances | #Fields | #Features |
|---|---:|---:|---:|
| Criteo | 45,840,617 | 39 | 2,086,936 |
| Avazu | 40,428,967 | 22 | 1,544,250 |
| MovieLens | 2,006,859 | 3 | 90,445 |
| Frappe | 288,609 | 10 | 5,382 |

这比只写“4 个公开 benchmark”更严谨，因为它直接说明了每个数据集的规模、字段数与特征规模。除此之外，论文还报告了一个工业新闻推荐系统中的 **online A/B test**，并明确说 FinalMLP 在工业设置中的离线和在线测试都优于 deployed baseline。

## 评估基线与实验设计

FinalMLP 的对比对象不是模糊的“其他近期模型”，而是论文 Table 3 中明确列出的强基线：

- **Wide & Deep**
- **DeepFM**
- **DCN**
- **xDeepFM**
- **AutoInt+**
- **AFN+**
- **DeepIM**
- **MaskNet**
- **DCNv2**
- **EDCN**
- **DualMLP**

这种 benchmark 设计是有说服力的，因为它正面回答了论文自己的问题：**一个增强的双 MLP 架构，能否击败多种更复杂的显式交互 / 双流 CTR 模型。**

## 实验结果

论文原文中的 benchmark 主表如下：

![benchmark](figures/finalmlp_benchmark.png)

*Benchmark 图示说明：该图截取自 FinalMLP 原论文的 Table 3。*

从 Table 3 可以直接读到 FinalMLP 的四个核心 AUC：

- **Criteo**：`81.49`
- **Avazu**：`76.66`
- **MovieLens**：`97.20`
- **Frappe**：`98.61`

如果和表中最强竞争基线对照，结论会比“取得最优”更具体：

- 在 **Criteo** 上，FinalMLP `81.49`，高于 **DCNv2** 的 `81.47`
- 在 **Avazu** 上，FinalMLP `76.66`，高于 **EDCN** 的 `76.57`
- 在 **MovieLens** 上，FinalMLP `97.20`，高于 **EDCN** 的 `96.98`
- 在 **Frappe** 上，FinalMLP `98.61`，高于 **DCNv2** 的 `98.50`

| 模型 | Criteo | Avazu | MovieLens | Frappe |
|---|---:|---:|---:|---:|
| DCNv2 | 81.47 | 76.52 | 96.71 | 98.50 |
| EDCN | 81.42 | 76.57 | 96.98 | 98.47 |
| DualMLP | 81.42 | 76.57 | 96.98 | 98.47 |
| **FinalMLP** | **81.49** | **76.66** | **97.20** | **98.61** |

另外，论文 Figure 2 的消融实验也给出了较具体的结构性证据：

- 在 **Avazu** 上，`DualMLP = 76.57`，`FinalMLP = 76.66`
- 在 **MovieLens** 上，`DualMLP = 96.98`，`FinalMLP = 97.20`
- 在 **Frappe** 上，`DualMLP = 98.47`，`FinalMLP = 98.61`

因此，FinalMLP 的 benchmark 结论更适合写成：**FinalMLP 不仅在 4 个公开数据集上取得表中最优 AUC，而且相对最强竞争基线的优势虽不算极大，但在四个 benchmark 上表现稳定一致，这对 CTR 结构论文是更有说服力的证据。**

## 指标评估与含义

### AUC 的计算逻辑

AUC 可以理解为：

`AUC = P(s+ > s-)`

其中 `s+` 表示点击样本得分，`s-` 表示未点击样本得分。AUC 越高，说明模型越能把正样本排在前面。

### LogLoss 的计算逻辑

对于二分类 CTR 预测，LogLoss 通常写作：

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

其中 `y` 为真实标签，`p` 为预测点击概率。LogLoss 越低，表示概率预测越准确。

### 为什么这套指标适合 FinalMLP

FinalMLP 既需要证明自己在排序上更强，也需要证明预测概率更可靠，因此 AUC 与 LogLoss 是最自然的评价组合。

## 文献价值与边界

FinalMLP 对本项目的意义在于，它代表了一类较新的传统 CTR 方法：仍然聚焦预测结构本身，但开始重新思考双流建模中的设计逻辑。它说明 CTR 文献中的创新并不总是来自“更复杂的模块”，有时也来自“重新组织已有模块的方式”。

但从本项目角度看，FinalMLP 仍属于典型的 predictor architecture 论文。它不生成因子级自然语言解释，也不涉及 numerical-to-semantic feedback，更不会把解释当作 textual gradients 去更新多模块 LLM prompt。因此，它是很好的预测侧背景文献，但不是本项目解释与优化机制的直接来源。
