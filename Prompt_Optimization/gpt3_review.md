## Prompt Optimization Literature Review: GPT-3 / In-Context Learning

### Bibliographic Information

- **Title**: Language Models are Few-Shot Learners
- **Authors**: Tom B. Brown et al.
- **Year**: 2020
- **Venue**: NeurIPS 2020
- **Core Topic**: few-shot learning; in-context learning; prompting as a task interface

### 1. Prompt Optimization Strategy

Strictly speaking, GPT-3 is **not** a later automatic prompt optimization paper. It is foundational because it shows, at scale, that **changing the prompt alone can change task behavior without changing model parameters**.

Its prompt strategy is the now-classic **zero-shot / one-shot / few-shot prompting** setup:

1. express the task in natural language
2. optionally include `0 / 1 / k` demonstrations in the prompt
3. let the model complete the task in context
4. perform no gradient updates or task-specific fine-tuning

From the later prompt-optimization viewpoint, GPT-3 matters because it turns the prompt into a legitimate object of study.

### 2. Biggest Innovation

The biggest innovation is not only model scale. It is the demonstration that **in-context learning** is a systematic phenomenon: the same pretrained model can switch tasks through the arrangement of prompt instructions and demonstrations alone.

That is why later lines such as:
- prompt engineering
- automatic prompt search
- chain-of-thought prompting
- prompt optimization

could become a distinct research area at all.

### 3. Metrics and How They Are Computed

Because GPT-3 spans many tasks, it does not rely on one single metric. The paper mainly reports:

- **Accuracy**

`Accuracy = number of correct predictions / total number of examples`

- **F1**

`F1 = 2PR / (P + R)`

- **BLEU**

used for translation tasks through n-gram overlap.

The paper's main comparative axis is not a new metric, but how the same model changes under **zero-shot, one-shot, and few-shot** prompting.

### 4. Datasets / Task Setting

GPT-3 should not be described vaguely as “many NLP tasks.” The paper explicitly evaluates **42 accuracy-denominated benchmarks**, covering:

- **cloze / completion**: LAMBADA, StoryCloze, HellaSwag
- **question answering / commonsense**: OpenBookQA, ARC, PIQA, TriviaQA, WebQuestions
- **reading comprehension**: CoQA, DROP, SQuAD
- **benchmark suites**: SuperGLUE
- **translation**
- **arithmetic**
- **word unscrambling / analogy**

On SuperGLUE, the few-shot setup uses **32 examples per task** in context.

### 5. Benchmark Performance Summary

Several concrete results from the paper are especially important:

1. **LAMBADA**

| Setting | Accuracy |
|---|---:|
| SOTA | 68.0 |
| GPT-3 Zero-shot | 76.2 |
| GPT-3 One-shot | 72.5 |
| GPT-3 Few-shot | **86.4** |

So on LAMBADA, GPT-3 few-shot improves over prior SOTA by **18.4 points**.

2. **SuperGLUE (test set)**

| Model / Setting | Score |
|---|---:|
| Fine-tuned SOTA | 89.0 |
| Fine-tuned BERT-Large | 69.0 |
| GPT-3 Few-shot | **71.8** |

The careful interpretation is: GPT-3 few-shot **beats fine-tuned BERT-Large** on the overall SuperGLUE score, but still trails the then state of the art.

3. **Selected SuperGLUE tasks**

| Task | GPT-3 Few-shot |
|---|---:|
| BoolQ | 76.4 |
| COPA | 92.0 |
| RTE | 69.0 |
| WSC | 80.1 |
| ReCoRD Accuracy | 90.2 |
| ReCoRD F1 | 91.1 |

So the rigorous benchmark takeaway is not “GPT-3 is SOTA everywhere,” but: **with prompt demonstrations alone, GPT-3 approaches or exceeds strong fine-tuned baselines on many tasks, though performance remains uneven across benchmarks.**

### 6. Architecture / Conceptual Understanding

For prompt research purposes, the paper can be read as a three-level conceptual shift:

- `Task expression layer`: a task is specified through natural-language instructions and examples rather than a task-specific output head.
- `Contextual adaptation layer`: the model adapts temporarily inside the prompt context without parameter updates.
- `Research significance layer`: the prompt becomes a controllable interface for behavior steering.

So although the paper is not an automatic prompt optimizer, it defines the premise of later prompt optimization work: **the prompt itself can materially change model performance.**

### 7. Literature Value and Limitations

GPT-3 is valuable because:

- it established the main line of **in-context and prompt-based learning**;
- it gave later prompt research a clear central question: how should prompts be designed;
- it showed that changing inputs alone can produce strong task transfer.

Its limitations are also clear:

- performance is not uniformly strong across all tasks;
- the paper itself does not propose an automatic prompt search method;
- the effect depends heavily on model scale, which later prompt optimization work tries to address more directly.
