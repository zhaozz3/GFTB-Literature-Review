## Traditional CTR Prediction Literature Example: FESeq

**Bibliographic Information**

- **Title**: Feature-Interaction-Enhanced Sequential Transformer for Click-Through Rate Prediction
- **Authors**: Quan Yuan, Ming Zhu, Yushi Li, Haozhe Liu, Siao Guo
- **Year**: 2024
- **Venue**: *Applied Sciences*, 14(7), 2760
- **DOI**: 10.3390/app14072760
- **Research Topic**: CTR prediction; sequential recommendation; feature interaction; Transformer; attention pooling
- **Recommended APA Citation**: Yuan, Q., Zhu, M., Li, Y., Liu, H., & Guo, S. (2024). Feature-interaction-enhanced sequential transformer for click-through rate prediction. *Applied Sciences, 14*(7), 2760. https://doi.org/10.3390/app14072760
- **Note**: This paper treats feature interaction as an automatic feature-engineering step before sequential Transformer modeling.

FESeq starts from a clear gap: many CTR models are strong either at multi-field feature interaction or at user behavior sequence modeling, but relatively few combine both in a truly hierarchical pipeline. To address this, the paper proposes **FESeq**, which first performs feature interaction, then sequential refinement, and then target-aware pooling.

## Main Innovation and Research Logic

The main innovation of FESeq can be summarized as follows: **feature interaction is used as an automatic feature-engineering step for a sequential Transformer CTR model.**

More specifically, the design contains three key ideas:
- an `interacting layer` before the sequence model to extract higher-order feature combinations;
- a Transformer-based `sequence-refiner layer` with both `positional embedding` and `linear time interval embedding`;
- `scaled bilinear attention` for sequence pooling so that the model explicitly learns relevance between historical behaviors and the target item.

## Model Usage / Architecture Understanding

Read FESeq as interaction-first sequence modeling:
- `Backbone`: interaction enhancement comes before sequence refinement.
- `Innovation module`: the sequence-refiner layer models order and interval after richer feature interaction is injected.
- `Why it matters`: it does not treat behavior modeling and feature interaction as two disconnected stages.
## Key Terms

- **Interacting Layer**: the higher-order feature interaction layer placed before the sequence model.
- **Sequence-refiner Layer**: the Transformer layer with positional and time-interval information.
- **Scaled Bilinear Attention**: the target-aware attention used in sequence pooling.
- **AUC / LogLoss**: the main evaluation metrics.

## Datasets and Research Setting

The paper’s `Section 4.1.1` and `Table 1` provide very concrete dataset settings:

| Dataset | #Users | #Items | #Samples | #Sequence Fields | #Non-Sequence Fields |
|---|---:|---:|---:|---:|---:|
| Ele.me | 1,013,338 | 449,440 | 1,228,589 | 14 | 9 |
| Bundle | 118,931 | 11 | 4,193,418 | 24 | 9 |

The paper also states that:
- `Ele.me` is a public dataset sampled from 3 days of click logs from a commercial food-delivery website;
- `Bundle` is an industrial dataset collected from an online game server from `2021-11-01` to `2021-12-30`;
- both datasets are sorted by timestamp and split into train/validation/test with an `8:1:1` ratio.

So FESeq’s data setting is not just vaguely “a sequential and non-sequential benchmark”. It is two concrete datasets with clear scale, field counts, and time-based splits.

## Evaluation Benchmarks

The paper compares three categories of baselines:
- **Type I: 11 feature-interaction models**, such as `Wide & Deep`, `DeepFM`, `DCN`, `AutoInt`, `DCNv2`, `FinalMLP`, and `FINAL`
- **Type II: 6 sequential models**, such as `DIN`, `SASRec`, `DIEN`, `BST`, `TiSASRec`, and `DMR`
- **Type III: 1 unified model**, namely `JointCTR`

This benchmark design directly addresses the paper’s question: **does hierarchical fusion of feature interaction and sequence learning outperform models that focus on only one side, and outperform a parallel unified baseline?**

## Experimental Results

The main results in `Table 3` are:

| Model | Ele.me AUC / LogLoss | Bundle AUC / LogLoss |
|---|---|---|
| FINAL | 0.5528 / 0.0933 | 0.8796 / 0.0113 |
| DMR | 0.6039 / 0.0921 | 0.8798 / 0.0112 |
| JointCTR | 0.5562 / 0.0930 | 0.8882 / 0.0109 |
| **FESeq** | **0.6127 / 0.0918** | **0.8912 / 0.0117** |

More precisely:
- on `Ele.me`, FESeq achieves both the highest `AUC=0.6127` and the lowest `LogLoss=0.0918`;
- on `Bundle`, FESeq achieves the highest `AUC=0.8912`, but its `LogLoss=0.0117` is not the lowest, because `JointCTR=0.0109` is smaller.

So the rigorous conclusion is:
- **FESeq achieves the best AUC on both datasets**;
- it also achieves the best LogLoss on `Ele.me`;
- on `Bundle`, it is best in ranking but not best in LogLoss.

The paper also reports FESeq’s gain over `JointCTR`:
- Ele.me: AUC `+1.46%`
- Bundle: AUC `+0.30%`

The ablation results are also concrete:
- removing the interacting layer reduces Ele.me AUC from `0.6127` to `0.5882`, and Bundle AUC from `0.8912` to `0.8649`;
- removing non-sequential features reduces Ele.me AUC from `0.6127` to `0.5980`, and Bundle AUC from `0.8912` to `0.8780`;
- removing positional embedding reduces Ele.me AUC from `0.6127` to `0.5757`, and Bundle AUC from `0.8912` to `0.8866`.

These numbers support a much stronger interpretation than a vague “Transformer helps”: the interaction layer, position/time modeling, and target-aware pooling all matter.

## Metrics and How They Are Computed

### AUC

The paper defines AUC as:

`AUC = (Σ_{i∈I+} Σ_{j∈I-} δ(p_i, p_j)) / (|I+||I-|)`

where:
- `I+` is the positive sample set;
- `I-` is the negative sample set;
- `δ(i,j)=1` if `i>j`, `0.5` if `i=j`, and `0` if `i<j`.

### LogLoss

`LogLoss = - (1/N) Σ_{(x,y)∈D} [ y log p(x) + (1-y) log(1-p(x)) ]`

The training objective in the paper also includes an `L2 regularization` term, but the evaluation interpretation is still the standard cross-entropy quality of predicted click probabilities.

### Why Both Metrics Matter for FESeq

FESeq aims to improve both ranking and probability quality. AUC captures ranking; LogLoss captures probability estimation. Both are needed to support the paper’s claim.

## Value and Boundary of the Paper

FESeq is valuable because it represents a mature 2024 CTR direction: truly hierarchical fusion of structured feature interaction and behavioral sequence learning, rather than a loose or parallel combination. It is a strong representative paper for the trend toward richer heterogeneous information modeling in CTR predictors.

Its boundary is also clear: FESeq’s interpretability mainly comes from attention visualization, not from natural-language explanation. It does not generate explanations, does not provide factor-level rationales in language, and does not involve textual-feedback optimization. So it belongs to the strong-predictor literature, not the explanation-generation or prompt-optimization literature.

