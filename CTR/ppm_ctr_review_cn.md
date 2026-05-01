## 传统 CTR 预测文献示例：PPM

**文献信息**

- **题目**：PPM: A Pre-trained Plug-in Model for Click-through Rate Prediction
- **作者**：Yuanbo Gao, Peng Lin, Dongyue Wang, Feng Mei, Xiwei Zhao, Sulong Xu, Jinghe Hu
- **年份**：2024
- **会议**：*Companion Proceedings of the ACM Web Conference 2024 (WWW 2024 Companion)*
- **研究主题**：CTR 预测；预训练；冷启动；多模态推荐；插拔式架构
- **推荐引用格式（APA）**：Gao, Y., Lin, P., Wang, D., Mei, F., Zhao, X., Xu, S., & Hu, J. (2024). PPM: A pre-trained plug-in model for click-through rate prediction. In *Companion Proceedings of the ACM on Web Conference 2024*.
- **备注**：这是一篇 2024 年、产业相关性较强的 CTR 方法论文，尤其适合放在冷启动和多模态特征整合背景下阅读。

PPM 的出发点与很多经典 CTR 论文不同。它并不只问“如何在结构化 ID 特征上学得更好”，而是进一步追问：**一个 CTR 系统如何在不引入过高在线延迟的前提下，利用大规模预训练多模态知识？** 其现实动机很明确：传统 ID-based 推荐模型容易受冷启动影响，而直接把大型预训练模型放到在线服务路径里，又会明显增加 latency。为此，Gao 等（2024）提出 **PPM (Pre-trained Plug-in Model)**，通过“预训练 + 插件化接入”的方式，把多模态知识接入标准 CTR 栈，同时控制在线训练和推理开销。

从创新角度看，PPM 的重点并不是泛泛地“把预训练用在 CTR 上”，而是提出一种面向部署的设计：把预训练模块看作 plug-in，而不是一个必须端到端在线参与的重模型。因此，它的创新可以概括为：**通过插拔式架构把预训练多模态表示学习与工业 CTR 预测桥接起来，从而兼顾性能提升与服务延迟控制。**

## 核心创新与研究逻辑

PPM 回应的是一个典型的工业问题：传统 ID-based CTR 模型在冷启动场景下表现受限，也难以高效利用更长的历史和更丰富的内容信号；但如果直接把大型预训练模型塞进在线路径，又会带来难以接受的计算成本。于是，论文的核心思路是：先在大规模多模态数据上做预训练，再把中间结果以插件方式接入 CTR backbone，并通过缓存降低在线成本。

其实现链条可概括为：
1. 使用商品文本、图像等多模态信息进行预训练；
2. 让预训练模块输出可缓存的中间表示；
3. 把这些中间表示接入传统 ID-based CTR / ranking backbone；
4. 限制在线训练和推理阶段需要激活的参数；
5. 在保证部署可行性的前提下输出 CTR 预测；
6. 通过离线实验与在线 A/B test 检验价值。

## 模型如何使用 / 结构说明

把 PPM 看成“离线预训练 + 在线插件接入”最合适：
- `主干`：线上服务主干仍然是 ID-based CTR 模型。
- `创新模块`：多模态知识先离线学好，再缓存成插件表示接入线上系统。
- `关键理解`：它要解决的是工业场景里的效果-时延权衡，而不是完全重写推荐骨干。

## 关键术语

- **CTR**：点击率，通常建模为点击概率。
- **Cold-start**：新商品或新用户历史行为较少时，模型难以准确预测。
- **Pre-trained model**：先在大规模上游数据上训练，再迁移到目标任务的模型。
- **Plug-in model**：增强原有 backbone、但不完全替代其在线路径的模块。
- **Multimodal features**：来自多种模态的特征，例如文本和图像。
- **Latency**：在线服务延迟，是工业推荐系统的重要约束。

## 数据集与研究设定

PPM 使用的是 **JD 电商真实生产 CTR 数据集**。论文在 4.1 节明确写到：每条样本都包含

- 用户历史行为序列
- target item
- 点击标签

因此，这篇论文并不是“公开 benchmark 对比”风格，而是典型的 **industrial production dataset** 风格。它强调的不是可公开复现的数据集名称，而是场景约束：

- 冷启动问题显著
- 预训练模型参数大，容易带来延迟
- 训练迭代效率也是工业约束

论文还明确区分了 **cold-start** 和 **warm** 设置，并在图中单独比较两者的 AUC 改进。

## 评估基线与实验设计

PPM 的实验设计不是模糊的“标准基线 vs 增强版本”，而是围绕工业模型逐步比较。论文里直接比较：

- **Base**
- **QM&EP**
- **UMM**
- **Base+QM&EP**
- **URM**

同时，在线 A/B test 使用的是：

- **JD 首页推荐服务**
- 连续 **10 天**
- baseline 是当时线上已部署 CTR 模型

在线指标不是普通 AUC，而是：

- **UCTR = #clicks / #users**
- **UCVR = #orders / #users**

这比单纯说“做了在线 A/B test”严谨得多，因为它明确了场景、时长和指标定义。

## 实验结果

原论文中的主要 benchmark 页如下：

![benchmark](figures/ppm_page6_full.png)

*Benchmark 图示说明：该图截取自 PPM 原论文的 Tables 2 和 3。*

从 Table 2 可以直接读到，最强配置 **URM** 的关键离线结果为：

- **Click AUC**：`0.7343`
- **Click P@2**：`0.2363`
- **Order AUC**：`0.7722`
- **Order P@2**：`0.2313`
- **Average AUC**：`0.7532`
- **Average P@2**：`0.2338`

如果与表中的 **Base** 直接比较：

- Base average AUC = `0.7432`
- URM average AUC = `0.7532`

也就是 **+0.0100 的 average AUC 提升**。论文文本还明确强调：

- PPM / URM 在 **cold-start** 场景下优势更明显
- 离线和在线 A/B testing 都证明了方法有效
- 同时 **without an increase in online latency**

| 设置 | Click AUC | Click P@2 | Order AUC | Order P@2 | Average AUC |
|---|---:|---:|---:|---:|---:|
| Base | 0.7252 | 0.2310 | 0.7612 | 0.2305 | 0.7432 |
| **URM** | **0.7343** | **0.2363** | **0.7722** | **0.2313** | **0.7532** |

因此，PPM 的结果不能只写成“有明显增益”，而应更精确地写成：**在 JD 真实生产 CTR 数据上，URM/PPM 将 average AUC 从 0.7432 提升到 0.7532，同时改善 click 与 order 两个任务，并且论文明确声称没有带来额外在线延迟。**

## 指标评估与含义

### AUC 的计算逻辑

AUC 衡量排序能力：

`AUC = P(s+ > s-)`

### LogLoss 的计算逻辑

对于二分类点击预测：

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

### 为什么 PPM 不只看离线指标

PPM 特别强调，离线指标并不足以完整反映方法价值。除了标准的排序或概率指标，它还关注：
- 服务延迟；
- 训练迭代效率；
- 在线 A/B test 表现；
- 冷启动样本切片表现。

因此，这是一篇明显更偏部署导向的 CTR 论文。

## 文献价值与边界

PPM 对本项目有启发的地方在于，它说明较新的 CTR 文献已经开始更积极地引入 richer content signals 和 pre-trained knowledge，同时仍然非常重视系统部署约束。这对本项目理解“现实 CTR 系统不只是一个离线预测器”很有帮助。

不过，PPM 仍然属于传统 CTR 系统论文。它优化的是预测结构和工业部署，而不是因子级自然语言解释，更不把解释作为 textual gradients 用于优化。因此，它适合作为较新的预测侧参考文献，但并不直接触及本项目的解释与优化核心。
