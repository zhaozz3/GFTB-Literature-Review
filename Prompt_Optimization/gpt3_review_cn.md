## Prompt 优化文献综述：GPT-3 / In-Context Learning

### 文献信息

- **题目**：Language Models are Few-Shot Learners
- **作者**：Tom B. Brown 等
- **年份**：2020
- **会议**：NeurIPS 2020
- **核心主题**：few-shot learning；in-context learning；prompting 作为任务接口

### 1. Prompt 优化策略

严格说，GPT-3 这篇论文 **不是后来的自动 prompt optimization 论文**，但它是 prompt 研究的奠基文献，因为它第一次系统展示了：**不改参数，只改输入提示形式，也能让同一个大模型在不同任务上工作。**

它的 prompt 策略主要是 **zero-shot / one-shot / few-shot prompting**：

1. 把任务写成自然语言说明
2. 在 prompt 中加入 `0 / 1 / k` 个示例
3. 让模型直接在上下文里完成任务
4. 不做任何梯度更新或微调

所以从后来的视角看，GPT-3 的最大贡献是把 “prompt” 正式变成了一个可研究、可设计的任务接口。

### 2. 最大创新点

GPT-3 最大的创新不只是模型大，而是它把 **in-context learning** 证明成了一个系统性现象：同一个预训练模型，在不改参数的情况下，仅靠 prompt 中示例组织方式的变化，就能切换任务行为。

也正因为这篇论文，后续的：
- prompt engineering
- automatic prompt search
- chain-of-thought
- prompt optimization

才真正成为一条独立研究线。

### 3. 指标评估及如何计算

GPT-3 横跨很多任务，因此指标并不单一，论文里主要使用：

- **Accuracy**

`Accuracy = 正确预测样本数 / 总样本数`

- **F1**

`F1 = 2PR / (P + R)`

- **BLEU**

用于翻译任务的 n-gram 匹配指标。

这篇论文的重点不在提出新指标，而在于比较 **zero-shot / one-shot / few-shot** 三种 prompt 设置下，同一模型性能如何变化。

### 4. 数据集 / 任务设置

GPT-3 的任务设置非常广，不应写成“很多 NLP tasks”这种泛化表述。论文明确强调它评估了 **42 个 accuracy-denominated benchmarks**，覆盖：

- **cloze / completion**：LAMBADA、StoryCloze、HellaSwag
- **question answering / commonsense**：OpenBookQA、ARC、PIQA、TriviaQA、WebQuestions
- **reading comprehension**：CoQA、DROP、SQuAD
- **benchmark suites**：SuperGLUE
- **translation**
- **arithmetic**
- **word unscrambling / analogy**

其中在 SuperGLUE 上，few-shot 设置使用 **每个任务 32 个示例** 作为上下文。

### 5. Benchmark 效果总结

论文里最常被引用的几个具体结果包括：

1. **LAMBADA**

| Setting | Accuracy |
|---|---:|
| SOTA | 68.0 |
| GPT-3 Zero-shot | 76.2 |
| GPT-3 One-shot | 72.5 |
| GPT-3 Few-shot | **86.4** |

这意味着 GPT-3 在 few-shot LAMBADA 上比此前 SOTA 高 **18.4 个点**。

2. **SuperGLUE（test set）**

| Model / Setting | Score |
|---|---:|
| Fine-tuned SOTA | 89.0 |
| Fine-tuned BERT-Large | 69.0 |
| GPT-3 Few-shot | **71.8** |

这里需要严谨地写：GPT-3 few-shot **超过了 fine-tuned BERT-Large**，但仍低于当时 SuperGLUE 的整体 SOTA。

3. **部分 SuperGLUE 子任务**

| Task | GPT-3 Few-shot |
|---|---:|
| BoolQ | 76.4 |
| COPA | 92.0 |
| RTE | 69.0 |
| WSC | 80.1 |
| ReCoRD Accuracy | 90.2 |
| ReCoRD F1 | 91.1 |

因此，这篇论文最准确的 benchmark 结论不是“GPT-3 全面 SOTA”，而是：**仅靠 prompt 中示例，GPT-3 在很多任务上逼近或超过强微调基线，但表现并不均匀。**

### 6. Architecture / 帮助理解的结构

如果只从 prompt 研究角度理解这篇论文，可以把它看成三层逻辑：

- `任务表达层`：任务不再通过专门分类头定义，而是通过自然语言说明和示例来表达。
- `上下文学习层`：模型在当前 prompt 中临时适应任务，不改参数。
- `研究意义层`：prompt 从“输入文本”升级成“控制模型行为的接口”。

所以这篇论文虽然不是自动 prompt 优化方法，但它定义了后续 prompt optimization 的出发点：**先承认 prompt 本身可以决定任务表现。**

### 7. 文献价值与局限

GPT-3 的价值在于：

- 它奠定了 **in-context learning / prompt-based learning** 的主线；
- 它让后续 prompt 研究有了清晰的基准问题：怎样设计 prompt 能更好；
- 它证明了“只改输入不改参数”也可能带来强任务迁移能力。

它的局限也很明显：

- 并不是所有任务都稳定强；
- 论文本身没有提出自动 prompt 搜索机制；
- 表现高度依赖模型规模，这也是后续 prompt 优化文献持续试图解决的问题。
