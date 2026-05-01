## 传统 CTR 预测文献示例：Wide & Deep

**文献信息**

- **题目**：Wide & Deep Learning for Recommender Systems
- **作者**：Heng-Tze Cheng, Levent Koc, Jeremiah Harmsen, Tal Shaked, Tushar Chandra, Hrishi Aradhye, Glen Anderson, Greg Corrado, Wei Chai, Mustafa Ispir, Rohan Anil, Zakaria Haque, Lichan Hong, Vihan Jain, Xiaobing Liu, Hemal Shah
- **年份**：2016
- **会议**：Proceedings of the 1st Workshop on Deep Learning for Recommender Systems (DLRS 2016)
- **研究主题**：推荐与 CTR 排序；记忆与泛化；深度 CTR 奠基结构
- **推荐引用格式（APA）**：Cheng, H.-T., Koc, L., Harmsen, J., Shaked, T., Chandra, T., Aradhye, H., Anderson, G., Corrado, G., Chai, W., Ispir, M., Anil, R., Haque, Z., Hong, L., Jain, V., Liu, X., & Shah, H. (2016). Wide & deep learning for recommender systems. In *Proceedings of the 1st Workshop on Deep Learning for Recommender Systems* (pp. 7-10).
- **备注**：这是现代深度 CTR / 推荐排序里最常被追溯的经典起点之一。

Wide & Deep 的地位在于，它第一次把推荐排序中的两类能力明确拆开并联合起来：**wide 负责记忆（memorization）**，用人工交叉特征学习历史上出现过的有效组合；**deep 负责泛化（generalization）**，用 embedding 和多层感知机学习未显式出现过但语义相近的组合。这个框架直接影响了后来的 DeepFM、DCN 以及大量工业 CTR 系统。

## 核心创新与研究逻辑

Wide & Deep 回答的核心问题是：在广告和推荐排序里，单纯的线性宽模型虽然善于记住强共现，但难以泛化；而单纯的深模型虽然能泛化，却不一定记得那些特别关键、但稀有而高价值的显式组合特征。于是论文提出：

1. 用 **wide component** 学习已知有效的交叉特征；
2. 用 **deep component** 学习 embedding 上的隐式高阶交互；
3. 用 **joint training** 把两者一起训练，而不是训练完再简单集成。

这条逻辑后来几乎成为 CTR 文献里“浅层记忆 + 深层泛化”的标准叙事。

## 模型如何使用 / 结构说明

把 Wide & Deep 看成“两个分支共享一个目标函数”的排序模型最容易理解：
- `wide 分支`：输入原始稀疏特征和少量人工设计的 cross-product features，擅长记住历史上反复出现的模式。
- `deep 分支`：把类别特征映射成 embedding，再送入多层 ReLU 网络，擅长把相似项泛化到未见组合。
- `融合方式`：把 wide 和 deep 的输出 log-odds 做加权求和，再接一个共同的 logistic loss。
- `关键点`：这是 **joint training**，不是两个模型各自训练后再投票。

论文在 Google Play 推荐系统中的具体结构是：
- deep 侧每个类别特征学习 **32 维 embedding**；
- 拼接后的 dense 向量大约 **1200 维**；
- 经过 **3 层 ReLU** 隐层：`1024 -> 512 -> 256`；
- wide 侧使用 `user installed apps × impression apps` 的交叉特征。

## 关键术语

- **Memorization**：模型记住历史上频繁且有效的特征共现。
- **Generalization**：模型把已学到的表示迁移到未直接出现过的组合上。
- **Cross-product transformation**：人工构造的显式交叉特征。
- **Embedding**：把高维稀疏类别特征映射到低维稠密向量。
- **Joint training**：wide 与 deep 分支在同一个目标函数下联合训练。
- **AUC**：排序能力指标。

## 数据集与研究设定

这篇论文也 **不是标准公开 benchmark 论文**，它研究的是 Google Play 的在线推荐排序系统。数据与实验设置必须写具体：

- 研究对象：**Google Play mobile app store**
- 规模背景：**over one billion active users**，**over one million apps**
- 训练样本：论文明确写到 Wide & Deep 模型训练在 **over 500 billion examples** 上进行
- 单条样本定义：每个训练样本对应一次 **app impression**
- 标签定义：如果曝光的 app 被安装，标签为 `1`，否则为 `0`
- 线上实验：在 **3 weeks** 的 A/B testing 框架下进行
- 流量切分：control 组 **1% users**，Wide & Deep 实验组 **1% users**，deep-only 对照组也为 **1% users**

因此，更严谨的描述应当是：**Google Play 线上推荐排序场景，5000 亿级训练样本，3 周在线 A/B 测试，而不是公开数据集上的离线 benchmark。**

## 评估基线与实验设计

论文比较了三种模型：

- **Wide (control)**：此前的高优化 wide-only logistic regression
- **Deep**：只保留 deep component 的神经网络模型
- **Wide & Deep**：联合训练模型

其评估分成两块：
- **Offline**：用 holdout set 报告 AUC
- **Online**：用 A/B test 报告 app acquisition gain

这种设计非常有代表性，因为它明确说明：CTR / 推荐排序里，离线指标和线上业务收益并不总是一一对应。

## 实验结果

论文 Table 1 的核心结果如下：

| Model | Offline AUC | Online Acquisition Gain |
|---|---:|---:|
| Wide (control) | 0.726 | 0% |
| Deep | 0.722 | +2.9% |
| **Wide & Deep** | **0.728** | **+3.9%** |

从这个表可以得到三个很具体的结论：

1. **Wide & Deep** 的离线 AUC `0.728` 高于 wide-only 的 `0.726` 和 deep-only 的 `0.722`
2. 在线上，Wide & Deep 相对 control 组带来 **`+3.9%` app acquisition gain**
3. 论文还明确写到：Wide & Deep 相对 deep-only **再提升约 `+1%`**

这也是这篇论文最经典的经验结论：**wide 与 deep 的联合不仅在离线 AUC 上略优，而且在真实业务线上收益上更显著。**

## 指标评估与含义

### AUC

`AUC = P(s+ > s-)`

其中：
- `s+` 是正样本得分；
- `s-` 是负样本得分。

在这篇论文里，AUC 用于离线 holdout set，回答的是“模型排序能力是否更强”。

### Online Acquisition Gain

论文没有把它写成复杂公式，但其含义就是：

`Gain = (实验组 acquisition - 对照组 acquisition) / 对照组 acquisition`

因此 `+3.9%` 的意思是：实验组相对 control 组，用户安装 app 的获取率提升了 3.9%。

### 为什么这篇论文特别重要

因为它非常清楚地展示了：**离线 AUC 的轻微改善，可能在线上业务指标上对应更显著收益。** 这也是 CTR / 推荐系统文献里一个非常核心的研究认识。

## 文献价值与边界

这篇论文的价值在于：

1. 它提出了后来被不断复用的 **wide + deep** 基本范式；
2. 它把 **memorization vs. generalization** 这条解释框架建立起来了；
3. 它给出的是 **真实工业 A/B test** 证据，而不只是离线 benchmark。

它的边界也很明确：

- 论文依然依赖人工设计的 wide 交叉特征；
- 没有公开 benchmark，复现性弱；
- deep 分支本身还比较基础，后来的 DeepFM、DCN 等工作都在进一步减少人工交叉或增强显式交互建模。
