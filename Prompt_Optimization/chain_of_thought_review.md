## Prompt Optimization Literature Review: Chain-of-Thought

### Bibliographic Information

- **Title**: Chain-of-Thought Prompting Elicits Reasoning in Large Language Models
- **Authors**: Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, Fei Xia, Ed Chi, Quoc V. Le, Denny Zhou
- **Year**: 2022
- **Venue**: NeurIPS 2022
- **Core Topic**: chain-of-thought prompting; reasoning elicitation; few-shot prompt design

### 1. Prompt Optimization Strategy

Chain-of-Thought (CoT) does not search for better prompt tokens. Instead, it optimizes the **structure of few-shot exemplars**. Rather than showing only the final answer, it includes intermediate reasoning steps in the demonstrations, so the model is induced to produce similar reasoning traces at test time.

Its strategy is:

1. choose a small number of few-shot exemplars
2. include natural-language reasoning steps inside each exemplar
3. let the model imitate a “reason first, answer second” format
4. reuse that prompt structure on new problems

So CoT is best understood as **prompt format optimization** rather than token search.

### 2. Biggest Innovation

Its main innovation is the demonstration that **reasoning ability can be elicited by explicitly showing intermediate steps in the prompt**. In other words, a prompt can influence not only *what* the model should do, but also *how* it should proceed.

This became highly influential because many later prompt methods effectively optimize:
- whether to include reasoning traces
- what those traces should look like
- whether to sample multiple reasoning chains

### 3. Metrics and How They Are Computed

The paper mainly reports:

- **Accuracy / Solve Rate**

`Accuracy = number of correctly solved examples / total number of examples`

For arithmetic and commonsense tasks, solve rate is effectively the same idea: the proportion of questions answered correctly.

### 4. Datasets / Task Setting

The evaluation setup is concrete and spans three task families:

1. **Arithmetic reasoning**
- GSM8K
- SVAMP
- AQuA
- MAWPS
- ASDiv

2. **Commonsense reasoning**
- CSQA
- StrategyQA
- BIG-bench Date Understanding
- BIG-bench Sports Understanding
- SayCan

3. **Symbolic reasoning**
- Last Letter Concatenation
- Coin Flip

The paper compares results across **LaMDA, GPT-3, and PaLM** model families, with particular emphasis on how CoT becomes stronger at larger scales.

### 5. Benchmark Performance Summary

Three result groups are especially canonical.

1. **GSM8K**

The abstract explicitly states that with just **8 chain-of-thought exemplars**, `PaLM 540B` reaches **`58.1%`** on GSM8K and achieves state-of-the-art performance at the time.

The rigorous way to summarize the arithmetic result is: **CoT produces major gains on math reasoning benchmarks such as GSM8K, SVAMP, and MAWPS, with the 58.1% GSM8K result being the paper's signature number.**

2. **StrategyQA**

The main text explicitly reports:

| Setting | Accuracy |
|---|---:|
| Prior SOTA | 69.4% |
| **PaLM 540B + CoT** | **75.6%** |

3. **Sports Understanding**

The paper also explicitly reports:

| Setting | Accuracy |
|---|---:|
| Human sports enthusiast | 84% |
| **PaLM 540B + CoT** | **95.4%** |

In addition, the paper notes that on symbolic reasoning tasks, CoT allows `PaLM 540B` to reach near-**100%** solve rates on some in-domain settings and improves out-of-domain length generalization relative to standard prompting.

So the most accurate benchmark takeaway is: **by changing the structure of few-shot prompts, CoT substantially improves large-model performance on arithmetic, commonsense, and symbolic reasoning, with especially strong gains at larger model scales.**

### 6. Architecture / Conceptual Understanding

The method becomes clear if you track three elements:

- `Optimization object`: not single tokens, but the output format of exemplars.
- `Feedback mechanism`: not explicit gradients, but imitation of the contextual pattern “reasoning steps -> final answer.”
- `Key effect`: latent reasoning ability is surfaced through prompt structure.

So CoT is fundamentally a **capability-elicitation method achieved by rewriting the structure of prompt demonstrations**.

### 7. Literature Value and Limitations

CoT is valuable because:

- it is a foundational paper in reasoning-oriented prompting;
- it makes intermediate reasoning traces a central variable in prompt design;
- it directly inspired later work such as self-consistency, least-to-most, and tree-of-thought.

Its limitations are also clear:

- gains depend strongly on model scale;
- the paper mainly uses manually designed reasoning exemplars rather than automatic optimization;
- longer reasoning traces are not always more faithful or more verifiable.
