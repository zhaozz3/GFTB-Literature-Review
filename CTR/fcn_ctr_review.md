## Traditional CTR Prediction Literature Example: FCN

**Bibliographic Information**

- **Title**: FCN: Fusing Exponential and Linear Cross Network for Click-Through Rate Prediction
- **Authors**: Honghao Li, Yiwen Zhang, Yi Zhang, Hanwei Li, Lei Sang, Jieming Zhu
- **Year**: 2024 (arXiv version); the PDF header shows the KDD 2026 version
- **Venue**: arXiv preprint / KDD 2026 version PDF
- **Research Topic**: CTR prediction; explicit feature interaction; cross network; high-order interaction noise control
- **Recommended APA Citation**: Li, H., Zhang, Y., Zhang, Y., Li, H., Sang, L., & Zhu, J. (2024). FCN: Fusing exponential and linear cross network for click-through rate prediction. arXiv. https://arxiv.org/abs/2407.13349
- **Note**: This paper extends the DCN line by asking whether explicit feature interaction alone can be made strong enough without relying on a DNN branch.

FCN starts from a very concrete limitation in DCN-style models: linear cross structures are efficient, but they may be too weak to capture rich useful interactions; meanwhile, stronger high-order interaction modeling can introduce redundancy and noise. To address this, the paper proposes **FCN**, which fuses a **Linear Cross Network (LCN)** with an **Exponential Cross Network (ECN)** so that low-order and high-order explicit interactions can be modeled within one unified architecture.

## Main Innovation and Research Logic

The biggest innovation of FCN is not “adding another deep branch”, but almost the opposite: it tries to show that **explicit interaction modeling alone can reach SOTA performance**.

The core design has three parts:
- **ECN**: interaction order grows exponentially with depth rather than linearly.
- **LCA (Low-cost Aggregation)**: the aggregation step in the cross network is compressed to reduce parameter redundancy; the paper reports about 50% fewer parameters and about 23% lower inference latency.
- **Tri-BCE**: a tailored loss to provide more appropriate supervision for different interaction sub-networks.

The implementation pipeline is:
1. Multi-field sparse/numeric features are embedded;
2. One **LCN** branch learns stable low-order linear interactions;
3. One **ECN** branch learns exponentially growing high-order explicit interactions;
4. The branches are fused into `FCN_p` or `FCN_sp`;
5. The model is trained with Tri-BCE and outputs CTR probability.

## Model Usage / Architecture Understanding

Read FCN as a dual-cross architecture:
- `Backbone`: both branches stay in the explicit interaction family.
- `Innovation module`: `LCN + ECN` replaces the usual explicit-branch-plus-DNN recipe.
- `Why it matters`: the paper argues that stronger explicit interaction modeling alone can be competitive.
## Key Terms

- **LCN**: Linear Cross Network, the linearly growing explicit interaction branch.
- **ECN**: Exponential Cross Network, the exponentially growing explicit interaction branch.
- **LCA**: Low-cost Aggregation, a cheaper aggregation mechanism for cross interaction.
- **Tri-BCE**: a BCE-style loss tailored to the multi-branch structure.
- **AUC**: ranking-oriented metric.
- **LogLoss**: probability-quality metric.

## Datasets and Research Setting

The paper evaluates FCN on **six public CTR benchmarks**. This is important because the older review draft incorrectly mentioned `Amazon Electronics` and `KuaiRand`; those are not the datasets reported in the paper’s main benchmark table. The actual datasets from `Table 2` are:

| Dataset | #Instances | #Fields | #Features |
|---|---:|---:|---:|
| Avazu | 40,428,967 | 24 | 3,750,999 |
| Criteo | 45,840,617 | 39 | 910,747 |
| ML-1M | 739,012 | 7 | 9,751 |
| KDD12 | 141,371,038 | 13 | 4,668,096 |
| iPinYou | 19,495,974 | 16 | 665,765 |
| KKBox | 7,377,418 | 13 | 91,756 |

The paper further specifies that:
- the Avazu timestamp is transformed into `hour / weekday / weekend` fields;
- numeric fields in Criteo and KDD12 are discretized by `⌊log2(x)⌋`;
- rare categories are replaced with `OOV` using dataset-specific thresholds;
- the evaluation metrics are `AUC` and `LogLoss`.

So FCN is a broad benchmark paper over six public datasets, spanning very large sparse advertising data and medium-scale recommendation-style data, rather than a narrow single-dataset study.

## Evaluation Benchmarks

The paper compares FCN against **16 representative baselines**:
- `DNN`
- `PNN`
- `Wide & Deep`
- `DeepFM`
- `DCNv1`
- `xDeepFM`
- `AutoInt*`
- `AFN*`
- `DCNv2`
- `EDCN`
- `MaskNet`
- `EulerNet`
- `FinalMLP`
- `FINAL`
- `RFM`
- `DLF`

This baseline set is broad enough to directly answer the paper’s question: **can explicit interaction modeling alone outperform modern mixed explicit/implicit CTR models?**

## Experimental Results

The main results in `Table 3` are concrete. The strongest FCN variant reports the following performance:

| Model | Avazu | Criteo | ML-1M | KDD12 | iPinYou | KKBox |
|---|---|---|---|---|---|---|
| `FCN_sp` AUC | **79.72** | **81.63** | 90.67 | 80.96 | **78.52** | **85.74** |
| `FCN_sp` LogLoss | **0.3693** | **0.4357** | 0.3017 | **0.1494** | **0.005529** | 0.4754 |
| Strongest baseline AUC | 79.43 (MaskNet) | 81.49 (FINAL) | 90.56 (DCNv2) | 80.81 (DLF) | 78.30 (EulerNet) | 85.35 (xDeepFM) |

The paper also reports `Abs.Imp` for FCN over the strongest baseline:
- Avazu: AUC `+0.29`, LogLoss `-0.0018`
- Criteo: AUC `+0.14`, LogLoss `-0.0014`
- ML-1M: AUC `+0.26`, LogLoss `-0.0060`
- KDD12: AUC `+0.18`, LogLoss `-0.0003`
- iPinYou: AUC `+0.22`, LogLoss `-0.000010`
- KKBox: AUC `+0.39`, LogLoss `-0.0019`

The authors summarize this as FCN achieving the best performance on all six benchmarks, with about `0.25%` average AUC gain and about `0.19%` average LogLoss decrease. For CTR literature, those are strong, table-level claims rather than vague statements.

## Metrics and How They Are Computed

### AUC

`AUC = P(s+ > s-)`

This is the probability that a randomly chosen positive instance receives a higher score than a randomly chosen negative instance. In CTR tasks, higher AUC means better ranking ability.

### LogLoss

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

Here `y` is the true click label and `p` is the predicted click probability. Lower LogLoss means better probabilistic estimation.

### Why These Metrics Fit FCN

FCN is a typical CTR architecture paper:
- `AUC` tests whether ranking improves;
- `LogLoss` tests whether the predicted probabilities improve;
- both need to move in the right direction to support the claim that the new cross structure is genuinely better.

## Value and Boundary of the Paper

FCN is valuable because it shows that even in 2024/2026-style CTR research, explicit feature interaction architecture is still an active and meaningful direction. It is especially useful as a representative paper for the evolution of stronger cross-network predictors.

Its boundary is also clear: FCN does not produce natural-language explanations, does not generate factor-level rationales, and does not involve textual feedback or prompt optimization. It supports the “how to build a stronger predictor” side of the story, not the “how to explain and optimize via language” side.

