## 传统 CTR 预测文献示例：FESeq

**文献信息**

- **题目**：Feature-Interaction-Enhanced Sequential Transformer for Click-Through Rate Prediction
- **作者**：Quan Yuan, Ming Zhu, Yushi Li, Haozhe Liu, Siao Guo
- **年份**：2024
- **发表来源**：*Applied Sciences*, 14(7), 2760
- **DOI**：10.3390/app14072760
- **研究主题**：CTR prediction；序列推荐；特征交互；Transformer；attention pooling
- **推荐 APA 引用**：Yuan, Q., Zhu, M., Li, Y., Liu, H., & Guo, S. (2024). Feature-interaction-enhanced sequential transformer for click-through rate prediction. *Applied Sciences, 14*(7), 2760. https://doi.org/10.3390/app14072760
- **说明**：这篇工作把 feature interaction 作为 sequential Transformer 的前置自动特征工程步骤。

FESeq 的核心问题是：很多 CTR 模型要么擅长字段交互，要么擅长行为序列建模，却很少把两者真正做成一个层级化、串联式结构。作者因此提出 **FESeq**，先做 feature interaction，再做 sequence refinement，再做 target-aware pooling。

## 核心创新与研究逻辑

FESeq 的最大创新可以概括为：**把 feature interaction 作为 sequential Transformer 的自动特征工程步骤。**

具体包括三部分：
- 在序列模型之前引入 `interacting layer`，先提取高阶特征组合；
- 在 Transformer 中同时加入 `positional embedding` 与 `linear time interval embedding`；
- 用 `scaled bilinear attention` 做 sequence pooling，显式学习历史行为与目标 item 的相关性。

## 模型如何使用 / 结构说明

把 FESeq 看成“先增强交互，再精炼序列”：
- `主干`：不是先上序列模型，而是先做高阶交互增强。
- `创新模块`：sequence-refiner 在更强表示上建模顺序与时间间隔。
- `关键理解`：历史行为建模和特征交互被串成了一条连续信息流。

## 关键术语

- **Interacting Layer**：先于序列模型的高阶特征交互层。
- **Sequence-refiner Layer**：带位置和时间间隔信息的 Transformer 层。
- **Scaled Bilinear Attention**：用于 sequence pooling 的目标感知注意力。
- **AUC / LogLoss**：主评价指标。

## 数据集与研究设定

原论文 `Section 4.1.1` 与 `Table 1` 给出非常明确的数据集设定：

| 数据集 | #Users | #Items | #Samples | #Sequence Fields | #Non-Sequence Fields |
|---|---:|---:|---:|---:|---:|
| Ele.me | 1,013,338 | 449,440 | 1,228,589 | 14 | 9 |
| Bundle | 118,931 | 11 | 4,193,418 | 24 | 9 |

另外，论文明确说明：
- `Ele.me` 为公开数据集，来自商业外卖网站 3 天点击日志；
- `Bundle` 为工业数据集，采集自在线游戏服务器 `2021-11-01` 到 `2021-12-30` 的 bundle 推荐交互；
- 两个数据集都按时间排序，再按 `8:1:1` 划分训练/验证/测试集。

因此，FESeq 的“数据集”不是泛泛的“有序列和非序列字段”，而是两套有明确规模、明确时间切分方式的数据。

## 评估基线与实验设计

论文比较了三类基线：
- **Type I：11 个特征交互模型**，如 `Wide & Deep`、`DeepFM`、`DCN`、`AutoInt`、`DCNv2`、`FinalMLP`、`FINAL`
- **Type II：6 个序列模型**，如 `DIN`、`SASRec`、`DIEN`、`BST`、`TiSASRec`、`DMR`
- **Type III：1 个统一模型**，即 `JointCTR`

这样的 benchmark 设计能直接回答论文问题：**把 feature interaction 和 sequence learning 串联融合，是否优于只做其中一类的方法，以及优于并联式统一模型。**

## 实验结果

原论文 `Table 3` 的主结果是：

| 模型 | Ele.me AUC / LogLoss | Bundle AUC / LogLoss |
|---|---|---|
| FINAL | 0.5528 / 0.0933 | 0.8796 / 0.0113 |
| DMR | 0.6039 / 0.0921 | 0.8798 / 0.0112 |
| JointCTR | 0.5562 / 0.0930 | 0.8882 / 0.0109 |
| **FESeq** | **0.6127 / 0.0918** | **0.8912 / 0.0117** |

更具体地说：
- 在 `Ele.me` 上，FESeq 同时拿到最高 `AUC=0.6127` 和最低 `LogLoss=0.0918`；
- 在 `Bundle` 上，FESeq 拿到最高 `AUC=0.8912`，但 `LogLoss=0.0117` 并不是最小值，`JointCTR=0.0109` 更低。

所以最严谨的结论应写成：
- **FESeq 在两套数据上都拿到最高 AUC**；
- 在 `Ele.me` 上也拿到最佳 LogLoss；
- 在 `Bundle` 上则体现为“排序最好，但概率质量并非全表最佳”。

论文还给出与 `JointCTR` 的提升幅度：
- Ele.me：AUC `+1.46%`；
- Bundle：AUC `+0.30%`。

消融结果同样很具体：
- 去掉 interacting layer 后，Ele.me AUC `0.6127 -> 0.5882`；Bundle `0.8912 -> 0.8649`；
- 去掉非序列特征后，Ele.me `0.6127 -> 0.5980`；Bundle `0.8912 -> 0.8780`；
- 去掉 positional embedding 后，Ele.me `0.6127 -> 0.5757`；Bundle `0.8912 -> 0.8866`。

这些具体数值能更有力地说明论文主张：不是“只加个 Transformer 就更好”，而是交互层、位置/时间建模和目标感知 pooling 都在起作用。

## 指标评估与含义

### AUC 的计算逻辑

论文给出的定义是：

`AUC = (Σ_{i∈I+} Σ_{j∈I-} δ(p_i, p_j)) / (|I+||I-|)`

其中：
- `I+` 是正样本集合；
- `I-` 是负样本集合；
- `δ(i,j)=1` 当 `i>j`，`0.5` 当 `i=j`，`0` 当 `i<j`。

### LogLoss 的计算逻辑

`LogLoss = - (1/N) Σ_{(x,y)∈D} [ y log p(x) + (1-y) log(1-p(x)) ]`

论文在训练损失里还加入 `L2 regularization` 项，但在评价阶段，核心理解仍是点击概率与真实标签的交叉熵。

### 为什么 FESeq 要同时看两者

因为它既想提升排序能力，也想让目标感知 pooling 后的概率输出更稳定。AUC 反映排序，LogLoss 反映概率质量，二者缺一不可。

## 文献价值与边界

FESeq 的价值在于，它代表了 2024 年较成熟的一类 CTR 思路：把结构化字段交互和行为序列学习做真正的串联式融合，而不是简单并排拼接。它非常适合放在“现代 CTR predictor 越来越重视异质信息联合建模”的脉络里。

它的边界同样明确：FESeq 的“解释性”主要来自 attention 权重可视化，不生成自然语言解释，也不涉及基于文本反馈的优化。因此，它是强预测模型文献，而不是解释生成或 prompt 优化方法文献。
