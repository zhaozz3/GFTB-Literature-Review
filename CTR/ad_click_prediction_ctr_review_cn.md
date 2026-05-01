## 传统 CTR 预测文献示例：Ad Click Prediction

**文献信息**

- **题目**：Ad Click Prediction: a View from the Trenches
- **作者**：H. Brendan McMahan, Gary Holt, D. Sculley, Michael Young, Dietmar Ebner, Julian Grady, Lan Nie, Todd Phillips, Eugene Davydov, Daniel Golovin, Sharat Chikkerur, Dan Liu, Martin Wattenberg, Arnar Mar Hrafnkelsson, Tom Boulos, Jeremy Kubica
- **年份**：2013
- **会议**：Proceedings of the 19th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD 2013)
- **研究主题**：广告点击率预测；大规模在线学习；工业级 CTR 系统
- **推荐引用格式（APA）**：McMahan, H. B., Holt, G., Sculley, D., Young, M., Ebner, D., Grady, J., Nie, L., Phillips, T., Davydov, E., Golovin, D., Chikkerur, S., Liu, D., Wattenberg, M., Hrafnkelsson, A. M., Boulos, T., & Kubica, J. (2013). Ad click prediction: A view from the trenches. In *Proceedings of the 19th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining* (pp. 1222-1230).
- **备注**：这篇论文不是某个新网络结构论文，而是工业 CTR 系统的经典“底层问题定义”文献。

这篇论文的重要性在于，它把 CTR 预测从“实验室里的分类任务”拉回到了真实广告系统语境中。论文讨论的是 Google 已部署 sponsored search CTR 系统中的一系列核心问题：如何在每天数十亿级事件上做概率预测，如何在极端高维稀疏特征空间里训练模型，以及如何把精度、内存、校准和上线可维护性一起纳入建模目标。与后来的深度 CTR 文献相比，它的主角不是复杂神经网络，而是围绕 **逻辑回归 + 在线学习 + 工程优化** 展开的完整工业方案。

## 核心创新与研究逻辑

这篇论文的核心创新不在“提出一个更深的模型”，而在于系统性回答了工业 CTR 的四个现实问题：

1. 如何在 **极大规模流式数据** 上持续训练 CTR 模型；
2. 如何在保证精度的同时，把模型做得足够 **稀疏、可存储、可部署**；
3. 如何用更合理的 **在线评估与校准** 方法判断 CTR 模型是否真的变好；
4. 如何把看似边缘的工程问题，如特征管理、内存节省和可视化分析，纳入核心建模范式。

论文给出的技术主线是：
1. 用 **logistic regression** 预测 `P(click | q, a)`；
2. 用 **FTRL-Proximal** 进行在线优化，同时引入 `L1/L2` 正则以获得稀疏模型；
3. 用 **per-coordinate learning rate** 改善不同特征维度更新频率差异很大的问题；
4. 用 **progressive validation、AucLoss、校准分析** 来做更接近线上系统的评估。

## 模型如何使用 / 结构说明

把这篇论文里的 CTR 系统理解成“检索之后的点击概率估计器”最直观：
- `输入`：查询词、广告文本、广告元数据等高维稀疏特征。
- `核心模型`：单层逻辑回归，不追求深结构，而追求在线更新、可扩展、可稀疏化。
- `优化机制`：FTRL-Proximal 对每个特征坐标独立更新，天然适合超高维稀疏场景。
- `输出`：`P(click | q, a)`，作为广告排序与竞价机制的重要输入。

如果用一句话概括它的结构逻辑，就是：**先把 CTR 问题定义成海量稀疏特征下的概率预测，再用适合工业部署的在线稀疏学习算法把它做稳。**

## 关键术语

- **CTR**：点击率，建模时通常表示某次展示被点击的概率。
- **FTRL-Proximal**：一种适合在线大规模稀疏学习的优化方法，兼顾精度与稀疏性。
- **Per-coordinate learning rate**：每个特征维度使用不同学习率，而不是整模型共享一个全局学习率。
- **AucLoss**：论文里使用的排序损失表达，定义为 `1 - AUC`，因此越小越好。
- **Progressive Validation**：边预测边累计评估的在线验证方式，更接近真实流式系统。
- **Calibration**：预测概率是否与真实点击频率一致。

## 数据集与研究设定

这篇论文 **没有提供一个公开可复现的标准 benchmark 数据集**，这点必须写清楚。它研究的是 Google 已部署广告系统中的真实流式日志，因此实验设置主要分成两类：

- **小规模 prototyping datasets**：论文明确说明是“millions of examples”的原型实验数据，用来比较 FTRL、RDA、FOBOS 等优化方法。
- **full-scale deployed data**：用于真实上线系统与大规模比较，论文说明系统每天要处理 **billions of events per day**，模型有 **billions of coefficients**。

因此，这篇论文的数据设定更严谨的写法应当是：**工业流式广告日志 + 小规模原型数据集用于算法比较，而不是一个像 Criteo 那样公开命名、固定切分的 benchmark。**

## 评估基线与实验设计

论文的实验不是“和很多 CTR 神经网络刷分”，而是围绕工业在线学习系统的关键部件做比较，主要包括：

- **FTRL-Proximal**：论文主方法；
- **RDA**：Regularized Dual Averaging；
- **FOBOS**：Forward-Backward Splitting；
- **OGD-Count**：一个更简单的启发式在线梯度方法。

其核心比较目标是：
- 在相近甚至相同精度下，哪个方法能得到 **更稀疏** 的模型；
- 在大规模线上环境中，哪个方法的 **AucLoss / LogLoss / 校准表现** 更稳；
- 每坐标学习率、概率特征包含、校准等工程技巧能否持续带来收益。

## 实验结果

论文里最经典的一组结果是优化方法的稀疏性对比：

| 方法 | 非零参数数量相对 FTRL | AucLoss 相对 FTRL |
|---|---:|---:|
| **FTRL-Proximal** | baseline | baseline |
| RDA | `+3%` | `0.6%` detriment |
| FOBOS | `+38%` | `0.0%` |
| OGD-Count | `+216%` | `0.0%` |

这张表支持的核心结论是：
- 与 **RDA** 相比，FTRL 只用更少的非零参数，同时避免了 `0.6%` 的 AucLoss 损失；
- 与 **FOBOS** 相比，FTRL 在保持精度的同时显著更稀疏；
- 与 **OGD-Count** 相比，FTRL 在大规模数据上大幅减少模型大小，`非零参数少得多`。

论文还特别强调：在它们的应用场景中，**AucLoss 降低 1% 就已经是很大的改进**。这意味着这里表面上看很小的差别，在工业广告系统里其实是非常重要的收益。

因此，这篇论文最准确的 benchmark 结论不是“模型效果更好”这种泛泛说法，而是：**FTRL-Proximal 在工业 CTR 在线学习场景下给出了更好的精度-稀疏性折中，是后续大规模 CTR 学习的经典优化基线。**

## 指标评估与含义

### AUC 与 AucLoss

论文虽然大量讨论 `AucLoss`，它本质上仍然和 AUC 相关：

`AucLoss = 1 - AUC`

其中：
- `AUC` 越高越好，表示点击样本排在未点击样本之前的概率更高；
- `AucLoss` 越低越好。

### LogLoss

对单条样本，LogLoss 写作：

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

其中：
- `y` 是是否点击；
- `p` 是模型预测的点击概率。

### 为什么这篇工业论文重视校准

因为工业 CTR 模型不仅用于排序，还会进入竞价、预估收益等后续环节，所以预测概率本身要“像概率”。如果模型输出 `0.2`，真实点击率也应接近 `20%`，这就是 calibration 的意义。

## 文献价值与边界

这篇论文的价值主要有三点：

1. 它给出了 **工业 CTR 问题的真实定义**，说明 CTR 研究不只是离线分类，而是一个涉及在线学习、部署、校准和特征工程的大系统问题。
2. 它把 **FTRL-Proximal + 稀疏在线学习** 这条经典路线打牢了，对后续大规模广告与推荐建模影响很大。
3. 它提醒后来研究者：在 CTR 里，**工程约束本身就是研究问题的一部分**。

它的边界也很明确：

- 论文不提供公开 benchmark，复现性弱于后来的 Criteo 类研究；
- 论文主模型仍是线性的，无法直接学习复杂高阶非线性交互；
- 它更像“工业系统论文”而不是“深度结构创新论文”，所以如果研究重点是多模态、序列兴趣或可解释交互结构，需要和后续文献配合阅读。
