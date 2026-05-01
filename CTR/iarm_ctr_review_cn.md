## 传统 CTR 预测文献示例：IARM

**文献信息**

- **题目**：IARM: Interpretable Attention-based Recommender Model for CTR Prediction
- **作者**：Shuli Zhang, Jiaxing Song, Ying Liu, Yiming Liu
- **年份**：2023
- **发表来源**：*Journal of Big Data*, 10, Article 11
- **研究主题**：CTR prediction；用户兴趣建模；多头自注意力；结构可解释性
- **推荐 APA 引用**：Zhang, S., Song, J., Liu, Y., & Liu, Y. (2023). IARM: Interpretable attention-based recommender model for CTR prediction. *Journal of Big Data, 10*, 11.
- **说明**：这篇工作试图把“用户兴趣”作为中间表示显式建模，再与字段交互学习结合起来。

IARM 关注的不是单纯“再堆一个 attention 层”，而是把 CTR 里的用户偏好单独抽出来，形成一个更清晰的兴趣获取模块，再与交互层结合。论文的逻辑是：如果只做一般性交互，模型未必真正理解“用户喜欢什么”；如果先建模兴趣，再做多头交互，预测可能更稳。

## 核心创新与研究逻辑

IARM 的最大创新可以概括为：**把用户兴趣获取层与多头自注意力交互层组合到同一个 CTR 架构中。**

它的实现链条是：
1. 多字段特征先映射到 embedding 空间；
2. `interest acquisition layer` 学习用户兴趣表示；
3. `multi-head interaction layer` 建模字段间高阶交互；
4. `residual` 保留低阶与原始信息；
5. 拼接后输出 CTR 概率。

论文还专门做了两类消融：
- 去掉兴趣模块，验证显式兴趣建模是否必要；
- 去掉多头注意力或残差，验证交互层设计是否必要。

## 模型如何使用 / 结构说明

把 IARM 看成“先学兴趣，再学交互”：
- `主干`：先从用户相关字段抽取兴趣表示，再做注意力交互建模。
- `创新模块`：interest acquisition layer 被单独放在交互层前面，并保留 residual 原始信号。
- `关键理解`：作者把“用户偏好”和“字段关系”拆成了两个相邻但不同的子问题。

## 关键术语

- **Interest Acquisition**：显式学习用户兴趣表示的模块。
- **Multi-head Self-Attention**：在多个子空间中学习字段关系。
- **Residual Connection**：保留原始特征与低阶信息，避免高阶表示覆盖全部信号。
- **Interpretability**：这里主要指结构层面的可理解性，而不是自然语言解释。

## 数据集与研究设定

原论文 `Table 1` 明确使用了 **4 个公开数据集**，但真正用于主性能表 `Table 2` 的是其中 3 个：

| 数据集 | Samples | Fields | Sparse Features |
|---|---:|---:|---:|
| Criteo | 45,840,617 | 39 | 998,960 |
| Avazu | 40,428,967 | 23 | 1,544,488 |
| MovieLens-1M | 739,012 | 7 | 3,529 |

论文文字还提到一共使用 `four publicly available real-world data sets`，但在当前 PDF 可直接核对到的 `Table 1` 里清楚列出了上面这 3 个核心数据集；而主结果表 `Table 2` 也只报告了这三者。因此综述里最稳妥的写法，就是把主实验明确限定为 **Criteo / Avazu / MovieLens-1M**。

## 评估基线与实验设计

`Table 2` 的直接对比基线包括：
- `FM`
- `Wide & Deep`
- `DeepFM`
- `AFM`
- `DCN`
- `NFM`
- `PNN`
- `AutoInt`
- `DeepCrossing`

这套比较对象能覆盖 FM、attention、cross network 和 DNN 几条主线，因此足以支撑 IARM 的问题设定：**显式兴趣建模 + 注意力交互是否比普通特征交互更有效。**

## 实验结果

原论文 `Table 2` 的主表结果如下：

| 模型 | Criteo AUC / LOSS | MovieLens-1M AUC / LOSS | Avazu AUC / LOSS |
|---|---|---|---|
| FM | 0.6869 / 0.5286 | 0.5347 / 0.4462 | 0.5437 / 0.6221 |
| DeepFM | 0.7283 / 0.4707 | 0.8340 / 0.3346 | 0.7461 / 0.4041 |
| AFM | 0.7220 / 0.4754 | 0.8295 / 0.3358 | 0.7567 / 0.4012 |
| DeepCrossing | 0.7375 / 0.4732 | 0.8373 / 0.3305 | 0.7572 / 0.3989 |
| **IARM** | **0.7545 / 0.4830** | **0.8386 / 0.3296** | **0.7652 / 0.3994** |

需要注意两点：
- 论文文字宣称 IARM 是“最高 AUC、最低 LOSS”；
- 但从表中具体数字看，`Criteo` 上 IARM 的 `LOSS=0.4830` 并不是最小值，`DeepFM` 和 `DeepCrossing` 的 LOSS 更低。

因此，严谨写法应是：
- **IARM 在三套数据上拿到了最高 AUC**；
- 在 `MovieLens-1M` 上也取得了最低 LOSS；
- 但在 `Criteo` 和 `Avazu` 上，LOSS 并非全表最优。

消融也有具体数字：
- 去掉兴趣模块后，`Criteo` AUC 从 `0.7545` 降到 `0.7371`，`MovieLens` 从 `0.8386` 降到 `0.8349`；
- 去掉多头注意力后，`Avazu` AUC 从 `0.7652` 降到 `0.7637`；
- 去掉残差后，`MovieLens` AUC 从 `0.8386` 降到 `0.8236`。

这些数值更能说明：IARM 的收益并不只是“换个名字的 attention 层”，而是兴趣模块、注意力模块和残差模块都在共同贡献。

## 指标评估与含义

### AUC 的计算逻辑

`AUC = P(s+ > s-)`

表示点击样本得分高于未点击样本得分的概率，越高说明排序能力越强。

### LogLoss 的计算逻辑

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

其中 `y` 为真实标签，`p` 为预测点击概率。值越低，说明模型给出的概率越接近真实点击分布。

### 为什么 IARM 要同时看两者

因为 IARM 同时想改善：
- 用户偏好相关样本的排序能力；
- 模型输出点击概率的可靠性。

只看 AUC 会忽略概率质量，只看 LogLoss 又不足以说明排序收益，因此两者都要报告。

## 文献价值与边界

IARM 的价值在于，它比很多纯交互模型更明确地把“用户兴趣”设成了中间层，这对理解 CTR predictor 的内部结构很有帮助。它也能作为“结构可解释性”在 CTR 里的一个例子。

但它的解释性仍停留在**结构和注意力权重层面**，不是自然语言 explanation，也不会把误差转换成语义反馈去优化其他模块。因此，它适合作为传统 CTR 模型中“兴趣建模 + attention interaction”的代表，但不属于解释生成或 prompt 优化文献。
