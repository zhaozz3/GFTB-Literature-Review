## 传统 CTR 预测文献示例：KD-NAFI

**文献信息**

- **题目**：Accurate and interpretable CTR prediction via distilled neural additive feature interaction network
- **作者**：Fei Guan, Jiahuan Zhan, Jing Yang
- **年份**：2025
- **期刊**：*Journal of Big Data*, 12, Article 223
- **DOI**：10.1186/s40537-025-01281-9
- **研究主题**：CTR 预测；可解释性；特征交互；知识蒸馏；神经加性模型
- **推荐引用格式（APA）**：Guan, F., Zhan, J., & Yang, J. (2025). Accurate and interpretable CTR prediction via distilled neural additive feature interaction network. *Journal of Big Data, 12*, 223. https://doi.org/10.1186/s40537-025-01281-9
- **备注**：这篇论文把准确性、可解释性和轻量化部署放在一起优化。

KD-NAFI 关注的核心张力是：可解释模型往往交互能力不够，强交互模型又往往太黑盒、太重。作者先提出 **NAFI**，再用多教师蒸馏压缩成 **KD-NAFI**，以实现“可解释 + 强交互 + 可部署”。

## 核心创新与研究逻辑

KD-NAFI 的最大创新可以概括为：**先用 NAM + FIN 组合学习可解释的一阶贡献与高阶交互，再通过多教师蒸馏把模型压缩成更轻量的学生模型。**

结构上包括：
- `NAM layer`：学习单个特征的可解释贡献；
- `FIN layer`：用多头自注意力学习高阶交互；
- `KD`：从集成教师模型向学生模型蒸馏知识。

## 模型如何使用 / 结构说明

把 KD-NAFI 看成“先学得准且可解释，再蒸馏得轻”：
- `主干`：NAFI 先把加性可解释结构和交互学习合在一起。
- `创新模块`：第二阶段蒸馏把这套能力压缩到更轻的学生模型。
- `关键理解`：论文把准确率、可解释性和部署成本放到同一个设计框架里。

## 关键术语

- **NAM**：Neural Additive Model，可解释的一阶加性结构。
- **FIN**：Feature Interaction Network，高阶交互模块。
- **Knowledge Distillation**：教师模型向学生模型迁移知识。
- **RelaImpr**：相对改进率指标。
- **AUC / LogLoss**：CTR 主评价指标。

## 数据集与研究设定

原论文 `Table 1` 给出的数据集信息是：

| 数据集 | recording | feature-field | sparsity |
|---|---:|---:|---:|
| Criteo | 45M | 39 (26+13) | 96.62% |
| Avazu | 40M | 23 | 95.74% |
| MovieLens-1M | 739,012 | 7 | 95.50% |

论文还明确说明：
- `Criteo` 与 `Avazu` 按 `8:1:1` 划分训练/验证/测试；
- `MovieLens-1M` 主要用于解释性可视化，不参与主性能表；
- 在 CTR 任务中，`AUC` 或 `LogLoss` 达到 `0.001` 级别的改善就有实际意义。

因此，这篇论文的主性能评估应被准确写成：**在 Criteo 与 Avazu 上做离线性能对比，在 Avazu 与 MovieLens-1M 上做解释性分析。**

## 评估基线与实验设计

原论文 `Table 2` 直接比较的基线包括：
- `LR`
- `FM`
- `AFM`
- `DNN`
- `DCN`
- `DeepFM`
- `xDeepFM`
- `AutoInt`
- `NAM`
- `DICN`
- `IARM`
- `Attention Enhanced DeepFM`
- `NAFM`

蒸馏阶段则把 `DeepFM / xDeepFM / AutoInt` 等强模型做集成，构成教师模型 `Ensemble-CTR`，再蒸馏到 `KD-NAFI`。

## 实验结果

### NAFI 主表（Table 2）

| 模型 | Criteo AUC / Logloss / RelaImpr | Avazu AUC / Logloss / RelaImpr |
|---|---|---|
| FM | 0.7931 / 0.4582 / 0.00% | 0.7690 / 0.3754 / 0.00% |
| DCN | 0.8048 / 0.4483 / 3.99% | 0.7777 / 0.3811 / 3.23% |
| xDeepFM | 0.8068 / 0.4480 / 4.67% | 0.7782 / 0.3808 / 3.42% |
| DICN | 0.8073 / 0.4428 / 4.84% | 0.7792 / 0.3801 / 3.79% |
| **NAFI** | **0.8092 / 0.4426 / 5.49%** | **0.7801 / 0.3751 / 4.13%** |

这能支撑三个严谨结论：
- NAFI 在 `Criteo` 和 `Avazu` 上都优于所有单模型基线；
- 相比 `FM`，RelaImpr 分别达到 `5.49%` 和 `4.13%`；
- 相比仅有加性解释的 `NAM`，RelaImpr 提升分别达到 `6.58%` 和 `7.73%`。

### 蒸馏结果（Table 4）

| 模型 | Criteo AUC / Logloss | Avazu AUC / Logloss |
|---|---|---|
| Ensemble-CTR | 0.8122 / 0.4399 | 0.7836 / 0.3426 |
| NAFI | 0.8092 / 0.4426 | 0.7801 / 0.3797 |
| **KD-NAFI** | **0.8105 / 0.4409** | **0.7806 / 0.3752** |

这说明：
- 教师集成 `Ensemble-CTR` 最强；
- `KD-NAFI` 虽未超过教师集成，但确实优于未蒸馏的 `NAFI`；
- 这正符合知识蒸馏的常见目标：让学生模型在更低复杂度下接近教师能力。

### 消融（Table 5）

| 变体 | Criteo AUC | Avazu AUC |
|---|---:|---:|
| NAM（无 FIN） | 0.7901 | 0.7600 |
| FIN（无 NAM） | 0.8071 | 0.7775 |
| NAFI（无 KD） | 0.8092 | 0.7801 |
| **KD-NAFI** | **0.8105** | **0.7806** |

这说明 NAM、FIN、KD 三个组成部分都在贡献性能，而不是只有某一个模块有效。

## 指标评估与含义

### AUC 的计算逻辑

论文给出的公式是：

`AUC = [ Σ(rank_ins) - M(M+1)/2 ] / (M*N)`

其中 `M` 是正样本数，`N` 是负样本数。AUC 越高，排序能力越强。

### LogLoss 的计算逻辑

`L = -(1/N) Σ_i [ y_i log(σ(ŷ_i)) + (1-y_i) log(1-σ(ŷ_i)) ]`

其中 `ŷ_i` 是模型输出，`σ` 是 sigmoid。LogLoss 越低，说明点击概率估计越好。

### RelaImpr 的计算逻辑

论文把 `FM` 作为基准，`RelaImpr` 用于表示相对改进幅度。虽然文中未在该页展开公式，但可理解为相对基准模型的 AUC 增益百分比。

## 文献价值与边界

KD-NAFI 的价值在于，它是少数明确把**可解释性、强交互、轻量化部署**同时放在一个 CTR 框架里讨论的工作，也比很多“解释型 CTR”论文给出了更清晰的定量结果。

它的边界也需要说清：这里的解释性主要是特征贡献与交互权重层面的，不是自然语言 explanation；蒸馏也只发生在 predictor 模型之间，而不是 LLM 模块之间。因此，它适合作为“可解释 CTR predictor”的代表，不属于 prompt 优化或解释生成文献。
