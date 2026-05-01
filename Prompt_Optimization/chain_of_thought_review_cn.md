## Prompt 优化文献综述：Chain-of-Thought

### 文献信息

- **题目**：Chain-of-Thought Prompting Elicits Reasoning in Large Language Models
- **作者**：Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, Fei Xia, Ed Chi, Quoc V. Le, Denny Zhou
- **年份**：2022
- **会议**：NeurIPS 2022
- **核心主题**：chain-of-thought prompting；reasoning elicitation；few-shot prompt design

### 1. Prompt 优化策略

CoT 不是搜索 prompt token，而是优化 **prompt 的示例结构**。它在 few-shot 示例中不只给最终答案，而是把中间推理步骤一起放进 prompt，从而诱导模型在测试时也生成中间推理链。

它的策略链条是：

1. 选择少量 few-shot exemplars
2. 在每个 exemplar 中加入自然语言推理步骤
3. 让模型模仿“先推理、后作答”的输出格式
4. 用同样的 prompt 结构推广到新问题

因此，CoT 的核心不是自动搜索，而是 **prompt format optimization**。

### 2. 最大创新点

CoT 最大的创新在于：它证明了 **推理能力可以通过 prompt 中的中间步骤被显式诱导出来**。也就是说，prompt 不仅告诉模型“做什么”，还可以告诉模型“怎么想”。

这对后续 prompt optimization 影响极大，因为很多后续方法其实都在优化：
- 是否给 reasoning trace
- reasoning trace 长什么样
- 是否要采样多条 reasoning chains

### 3. 指标评估及如何计算

CoT 论文主要使用：

- **Accuracy / Solve Rate**

`Accuracy = 正确解答题目数 / 总题目数`

在数学和常识推理任务中，论文经常直接报告 solve rate，本质上也是正确率。

### 4. 数据集 / 任务设置

CoT 的任务设置非常明确，分成三类：

1. **Arithmetic reasoning**
- GSM8K
- SVAMP
- AQuA
- MAWPS
- ASDiv

2. **Commonsense reasoning**
- CSQA
- StrategyQA
- BIG-bench 的 Date Understanding
- BIG-bench 的 Sports Understanding
- SayCan

3. **Symbolic reasoning**
- Last Letter Concatenation
- Coin Flip

论文在主结果里比较了 **LaMDA、GPT-3、PaLM** 三组模型，并特别强调模型规模扩大后 CoT 效果更明显。

### 5. Benchmark 效果总结

CoT 最经典的结果有三组。

1. **GSM8K**

论文摘要明确指出：只用 **8 个 chain-of-thought exemplars**，`PaLM 540B` 在 GSM8K 上达到 **`58.1%`**，并取得当时 SOTA。

更严谨地说，这篇论文在数学推理上的结论是：**CoT 使大模型在 GSM8K、SVAMP、MAWPS 等任务上出现显著跃升，其中 GSM8K 的 58.1% 是最标志性的数字。**

2. **StrategyQA**

论文正文明确写到：

| Setting | Accuracy |
|---|---:|
| Prior SOTA | 69.4% |
| **PaLM 540B + CoT** | **75.6%** |

3. **Sports Understanding**

论文正文明确写到：

| Setting | Accuracy |
|---|---:|
| Human sports enthusiast | 84% |
| **PaLM 540B + CoT** | **95.4%** |

此外，论文还指出在符号推理任务上，使用 CoT 后 `PaLM 540B` 的 solve rate 可接近 **100%**，并且在 OOD 长度泛化任务上也比 standard prompting 更强。

因此，这篇论文最准确的 benchmark 结论是：**CoT 通过改变 few-shot prompt 的结构，在数学、常识和符号推理上显著提升了大模型性能，而且这种提升在大模型上尤为明显。**

### 6. Architecture / 帮助理解的结构

理解 CoT，抓住下面三点就够了：

- `优化对象`：不是单个词，而是 exemplar 的输出格式。
- `反馈机制`：不是显式梯度，而是让模型在上下文中模仿“推理步骤 -> 最终答案”的范式。
- `关键作用`：把原本隐含在参数里的推理能力，通过 prompt 结构显化出来。

所以 CoT 本质上是一种 **通过示例结构重写 prompt 的能力诱导方法**。

### 7. 文献价值与局限

CoT 的价值在于：

- 它是 reasoning-oriented prompting 的奠基文献；
- 它让“中间推理步骤”成为 prompt 设计里的关键变量；
- 它直接催生了 self-consistency、least-to-most、tree-of-thought 等后续研究。

它的局限也很明确：

- 效果强依赖模型规模；
- 论文主要是人工设计 reasoning exemplars，不是自动优化；
- reasoning chain 生成得更长，并不总意味着更真实或更可验证。
