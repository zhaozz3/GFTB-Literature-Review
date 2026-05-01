## Prompt 优化文献综述：AutoPrompt

### 文献信息

- **题目**：AutoPrompt: Eliciting Knowledge from Language Models with Automatically Generated Prompts
- **作者**：Taylor Shin, Yasaman Razeghi, Robert L. Logan IV, Eric Wallace, Sameer Singh
- **年份**：2020
- **会议**：EMNLP 2020
- **核心主题**：automatic prompt generation；gradient-guided search；masked language model probing

### 1. Prompt 优化策略

AutoPrompt 是自动 prompt 优化的早期经典。它的核心思路不是让人手写模板，而是利用梯度信号去搜索触发词（trigger tokens），从而自动构造 prompt。

优化链条可以概括成：

1. 先把分类、事实检索、关系抽取任务改写成 cloze / fill-in-the-blank 形式
2. 固定一个 prompt 模板，其中 `[T]` 是可搜索的 trigger token 位置
3. 计算目标 token 对 trigger 的梯度
4. 用梯度引导候选替换，逐步更新 prompt

因此，AutoPrompt 的本质是 **gradient-guided discrete prompt search**。

### 2. 最大创新点

AutoPrompt 最大的创新在于：它证明了 **离散 prompt token 也可以被“优化”**，而不是只能靠人工写。这一点对后续整个 prompt optimization 文献都非常关键。

相比 GPT-3 只是展示 prompt 重要，AutoPrompt 更进一步：**prompt 可以自动搜索出来。**

### 3. 指标评估及如何计算

AutoPrompt 的主要指标按任务不同而不同：

- **Accuracy**

`Accuracy = 正确预测数 / 总样本数`

- **MRR（Mean Reciprocal Rank）**

`MRR = (1/N) * sum_i (1 / rank_i)`

- **P@10 / P@1**

`P@k = 前 k 个候选中命中正确答案的比例`

这些指标分别对应：
- 情感分类、自然语言推理：`Accuracy`
- 事实检索：`MRR / P@10 / P@1`

### 4. 数据集 / 任务设置

AutoPrompt 不是只在一个任务上测，它覆盖了三类很具体的任务：

- **SST-2**：情感分类
- **SICK-E**：自然语言推理
  - 标准版
  - 3-way 无偏版
  - 2-way 版
- **LAMA / T-REx**：事实检索
  - 论文在 LAMA 的 **41 个关系** 上收集训练数据
  - 每个关系最多收集 **1000 facts**
  - 并按 **80/20** 切分 train / dev
- **T-REx relation extraction**：关系抽取

这比“在分类和知识探测任务上评估”要具体得多。

### 5. Benchmark 效果总结

论文中最有代表性的几组结果如下。

1. **SST-2（test）**

| Model | Accuracy |
|---|---:|
| BERT (manual prompt) | 63.2 |
| **BERT (AutoPrompt)** | **82.3** |
| RoBERTa (manual prompt) | 85.2 |
| **RoBERTa (AutoPrompt)** | **91.4** |

这表明自动搜索得到的 prompt 明显优于人工 prompt。

2. **SICK-E**

| Model | Standard | 3-way | 2-way |
|---|---:|---:|---:|
| Majority | 56.7 | 33.3 | 50.0 |
| BERT (finetuned) | 86.7 | 84.0 | 95.6 |
| BERT (AutoPrompt) | 62.3 | 55.4 | 85.7 |
| RoBERTa (AutoPrompt) | 65.0 | 69.3 | 87.3 |

这里要严谨写：AutoPrompt 在 NLI 上 **显著优于多数基线和线性 probing**，但整体仍低于 fully finetuned BERT。

3. **LAMA factual retrieval**

| Prompt Type | Original MRR | Original P@10 | Original P@1 | T-REx MRR | T-REx P@10 | T-REx P@1 |
|---|---:|---:|---:|---:|---:|---:|
| LAMA | 40.27 | 59.49 | 31.10 | 35.79 | 54.29 | 26.38 |
| LPAQA (Top1) | 43.57 | 62.03 | 34.10 | 39.86 | 57.27 | 31.16 |
| **AutoPrompt 5 tokens** | **53.06** | **72.17** | **42.94** | **54.42** | **70.80** | **45.40** |
| **AutoPrompt 7 tokens** | **53.89** | **73.93** | **43.34** | **54.89** | **72.02** | **45.57** |

论文明确总结：AutoPrompt 在 factual retrieval 上把 `P@1` 提升了 **最多约 12 个点**。

因此，这篇论文最严谨的 benchmark 结论是：**AutoPrompt 在离散 prompt 搜索上显著优于人工 prompt，尤其在 factual retrieval 上效果非常突出。**

### 6. Architecture / 帮助理解的结构

理解 AutoPrompt 可以抓住三点：

- `优化对象`：不是连续 soft prompt，而是自然语言里的离散 trigger tokens。
- `反馈信号`：来自 masked LM 对目标 label token 的梯度。
- `更新方式`：梯度不直接更新 token，而是用来指导离散候选替换。

所以它的关键逻辑不是“训练一个新模型”，而是：**利用已有语言模型的梯度，反向搜索更有效的 prompt 词。**

### 7. 文献价值与局限

AutoPrompt 的价值在于：

- 它是自动 prompt 搜索的早期代表作；
- 它证明了离散 prompt token 也能被系统优化；
- 它把 prompt optimization 和梯度搜索联系起来了。

它的局限是：

- 主要面向 masked language models，而不是后来更主流的 decoder-only LLM；
- prompt 模板往往不自然，可解释性有限；
- 它更像 probing / elicitation 方法，不直接等同于通用任务型 prompt engineering。
