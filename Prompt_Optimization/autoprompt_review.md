## Prompt Optimization Literature Review: AutoPrompt

### Bibliographic Information

- **Title**: AutoPrompt: Eliciting Knowledge from Language Models with Automatically Generated Prompts
- **Authors**: Taylor Shin, Yasaman Razeghi, Robert L. Logan IV, Eric Wallace, Sameer Singh
- **Year**: 2020
- **Venue**: EMNLP 2020
- **Core Topic**: automatic prompt generation; gradient-guided search; masked language model probing

### 1. Prompt Optimization Strategy

AutoPrompt is a classic early paper in automatic prompt optimization. Its core idea is not to hand-write prompts, but to use gradient signals to search for trigger tokens that improve a prompt automatically.

The optimization chain is:

1. rewrite classification, fact retrieval, or relation extraction as a cloze / fill-in-the-blank task
2. fix a prompt template with searchable trigger-token slots `[T]`
3. compute gradients with respect to the target token
4. use those gradients to guide discrete token replacement

So the method is best described as **gradient-guided discrete prompt search**.

### 2. Biggest Innovation

AutoPrompt's biggest innovation is that it shows **discrete prompt tokens can themselves be optimized**, rather than only manually written.

GPT-3 showed that prompts matter. AutoPrompt went one step further: **prompts can be searched automatically.**

### 3. Metrics and How They Are Computed

The main metrics vary by task:

- **Accuracy**

`Accuracy = number of correct predictions / total number of examples`

- **MRR (Mean Reciprocal Rank)**

`MRR = (1/N) * sum_i (1 / rank_i)`

- **P@10 / P@1**

`P@k = proportion of cases where the correct answer appears in the top-k predictions`

These map to:
- sentiment analysis and NLI: `Accuracy`
- factual retrieval: `MRR / P@10 / P@1`

### 4. Datasets / Task Setting

AutoPrompt is evaluated on three concrete task families:

- **SST-2** for sentiment classification
- **SICK-E** for natural language inference
  - standard
  - unbiased 3-way
  - 2-way
- **LAMA / T-REx** for factual retrieval
  - the paper collects training data for **41 relations**
  - with up to **1000 facts per relation**
  - split into **80/20 train/dev**
- **T-REx relation extraction**

That is much more precise than saying only that the paper evaluates on classification and probing tasks.

### 5. Benchmark Performance Summary

Three result groups are especially representative.

1. **SST-2 (test)**

| Model | Accuracy |
|---|---:|
| BERT (manual prompt) | 63.2 |
| **BERT (AutoPrompt)** | **82.3** |
| RoBERTa (manual prompt) | 85.2 |
| **RoBERTa (AutoPrompt)** | **91.4** |

So automatically discovered prompts substantially outperform manual prompts.

2. **SICK-E**

| Model | Standard | 3-way | 2-way |
|---|---:|---:|---:|
| Majority | 56.7 | 33.3 | 50.0 |
| BERT (finetuned) | 86.7 | 84.0 | 95.6 |
| BERT (AutoPrompt) | 62.3 | 55.4 | 85.7 |
| RoBERTa (AutoPrompt) | 65.0 | 69.3 | 87.3 |

The careful interpretation is that AutoPrompt clearly beats majority-style and simple probing baselines, but it does not fully match supervised finetuning on NLI.

3. **LAMA factual retrieval**

| Prompt Type | Original MRR | Original P@10 | Original P@1 | T-REx MRR | T-REx P@10 | T-REx P@1 |
|---|---:|---:|---:|---:|---:|---:|
| LAMA | 40.27 | 59.49 | 31.10 | 35.79 | 54.29 | 26.38 |
| LPAQA (Top1) | 43.57 | 62.03 | 34.10 | 39.86 | 57.27 | 31.16 |
| **AutoPrompt 5 tokens** | **53.06** | **72.17** | **42.94** | **54.42** | **70.80** | **45.40** |
| **AutoPrompt 7 tokens** | **53.89** | **73.93** | **43.34** | **54.89** | **72.02** | **45.57** |

The paper explicitly notes that AutoPrompt improves `P@1` by **up to about 12 points** in factual retrieval.

So the most rigorous benchmark takeaway is: **AutoPrompt substantially improves over manual prompt construction for discrete prompt search, with especially strong gains on factual retrieval.**

### 6. Architecture / Conceptual Understanding

The method becomes clear if you track three elements:

- `Optimization object`: discrete natural-language trigger tokens rather than continuous soft prompts.
- `Feedback signal`: gradients from the masked LM toward the target label token.
- `Update mechanism`: gradients are not used to update tokens directly, but to rank replacement candidates in discrete search.

So the key idea is not “train a new model,” but: **use an existing language model's gradients to search backward for a better prompt.**

### 7. Literature Value and Limitations

AutoPrompt is valuable because:

- it is an early landmark in automatic prompt search;
- it shows that discrete prompt tokens can be optimized systematically;
- it connects prompt optimization to gradient-guided search.

Its limitations are:

- it is mainly designed for masked language models rather than later decoder-only LLMs;
- the resulting prompts are often unnatural and not especially interpretable;
- it is closer to probing and elicitation than to general-purpose instruction prompting.
