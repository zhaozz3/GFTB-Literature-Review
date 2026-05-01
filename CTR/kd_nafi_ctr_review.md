## Traditional CTR Prediction Literature Example: KD-NAFI

**Bibliographic Information**

- **Title**: Accurate and interpretable CTR prediction via distilled neural additive feature interaction network
- **Authors**: Fei Guan, Jiahuan Zhan, Jing Yang
- **Year**: 2025
- **Venue**: *Journal of Big Data*, 12, Article 223
- **DOI**: 10.1186/s40537-025-01281-9
- **Research Topic**: CTR prediction; interpretability; feature interaction; knowledge distillation; neural additive model
- **Recommended APA Citation**: Guan, F., Zhan, J., & Yang, J. (2025). Accurate and interpretable CTR prediction via distilled neural additive feature interaction network. *Journal of Big Data, 12*, 223. https://doi.org/10.1186/s40537-025-01281-9
- **Note**: This paper jointly optimizes prediction accuracy, interpretability, and lightweight deployment.

KD-NAFI tackles a familiar tension in CTR research: interpretable models are often weak at interaction learning, while strong interaction models are often heavier and more opaque. The paper first proposes **NAFI**, then distills it into **KD-NAFI** through a multi-teacher framework so that the final system is both more interpretable and more deployable.

## Main Innovation and Research Logic

The main innovation of KD-NAFI can be summarized as follows: **it first combines NAM and FIN to learn interpretable first-order contributions plus high-order feature interactions, and then uses multi-teacher distillation to compress the model into a lighter student.**

The core structure includes:
- a `NAM layer` for interpretable single-feature contributions;
- a `FIN layer` for high-order feature interaction through multi-head self-attention;
- a `KD` stage that transfers knowledge from an ensemble teacher into the student model.

## Model Usage / Architecture Understanding

Read KD-NAFI as a two-stage accurate-and-interpretable pipeline:
- `Backbone`: NAFI first combines additive interpretability with interaction learning.
- `Innovation module`: the second-stage distillation compresses that capability into a lighter deployable student.
- `Why it matters`: the paper treats accuracy, interpretability, and deployment cost as connected design goals.
## Key Terms

- **NAM**: Neural Additive Model, an interpretable additive structure for first-order effects.
- **FIN**: Feature Interaction Network, the high-order interaction component.
- **Knowledge Distillation**: transferring knowledge from teacher models into a student model.
- **RelaImpr**: the relative improvement metric used in the paper.
- **AUC / LogLoss**: the main CTR evaluation metrics.

## Datasets and Research Setting

The paper’s `Table 1` reports the following dataset information:

| Dataset | recording | feature-field | sparsity |
|---|---:|---:|---:|
| Criteo | 45M | 39 (26+13) | 96.62% |
| Avazu | 40M | 23 | 95.74% |
| MovieLens-1M | 739,012 | 7 | 95.50% |

The paper also states that:
- `Criteo` and `Avazu` are split into train/validation/test with an `8:1:1` ratio;
- `MovieLens-1M` is mainly used for interpretability visualization rather than the main predictive benchmark;
- in CTR tasks, an `AUC` or `LogLoss` change at the `0.001` level can already be meaningful.

So the most accurate description of the setup is: **predictive evaluation on Criteo and Avazu, plus interpretability analysis on Avazu and MovieLens-1M.**

## Evaluation Benchmarks

The direct baselines in `Table 2` include:
- `LR`
- `FM`
- `AFM`
- `DNN`
- `DCN`
- `DeepFM`
- `xDeepFM`
- `AutoInt`
- `NAM`
- `DICN`
- `IARM`
- `Attention Enhanced DeepFM`
- `NAFM`

In the distillation stage, strong models such as `DeepFM / xDeepFM / AutoInt` are ensembled into the teacher model `Ensemble-CTR`, and the student is then trained as `KD-NAFI`.

## Experimental Results

### Main NAFI Benchmark (`Table 2`)

| Model | Criteo AUC / Logloss / RelaImpr | Avazu AUC / Logloss / RelaImpr |
|---|---|---|
| FM | 0.7931 / 0.4582 / 0.00% | 0.7690 / 0.3754 / 0.00% |
| DCN | 0.8048 / 0.4483 / 3.99% | 0.7777 / 0.3811 / 3.23% |
| xDeepFM | 0.8068 / 0.4480 / 4.67% | 0.7782 / 0.3808 / 3.42% |
| DICN | 0.8073 / 0.4428 / 4.84% | 0.7792 / 0.3801 / 3.79% |
| **NAFI** | **0.8092 / 0.4426 / 5.49%** | **0.7801 / 0.3751 / 4.13%** |

This supports three concrete conclusions:
- NAFI outperforms all single-model baselines on both `Criteo` and `Avazu`;
- compared with `FM`, RelaImpr reaches `5.49%` and `4.13%`;
- compared with the additive-only `NAM`, RelaImpr improves by `6.58%` and `7.73%`.

### Distillation Results (`Table 4`)

| Model | Criteo AUC / Logloss | Avazu AUC / Logloss |
|---|---|---|
| Ensemble-CTR | 0.8122 / 0.4399 | 0.7836 / 0.3426 |
| NAFI | 0.8092 / 0.4426 | 0.7801 / 0.3797 |
| **KD-NAFI** | **0.8105 / 0.4409** | **0.7806 / 0.3752** |

This means:
- the teacher ensemble `Ensemble-CTR` is the strongest overall;
- `KD-NAFI` does not surpass the teacher ensemble, but it does outperform the undistilled `NAFI`;
- this is exactly the typical goal of knowledge distillation: a lighter student that approaches teacher capability.

### Ablation (`Table 5`)

| Variant | Criteo AUC | Avazu AUC |
|---|---:|---:|
| NAM (without FIN) | 0.7901 | 0.7600 |
| FIN (without NAM) | 0.8071 | 0.7775 |
| NAFI (without KD) | 0.8092 | 0.7801 |
| **KD-NAFI** | **0.8105** | **0.7806** |

This shows that all three parts, NAM, FIN, and KD, contribute to performance.

## Metrics and How They Are Computed

### AUC

The paper gives the formula:

`AUC = [ Σ(rank_ins) - M(M+1)/2 ] / (M*N)`

where `M` is the number of positive samples and `N` is the number of negative samples. Higher AUC means better ranking ability.

### LogLoss

`L = -(1/N) Σ_i [ y_i log(σ(ŷ_i)) + (1-y_i) log(1-σ(ŷ_i)) ]`

where `ŷ_i` is the model output and `σ` is the sigmoid function. Lower LogLoss means better click-probability estimation.

### RelaImpr

The paper uses `RelaImpr` to quantify relative improvement over the `FM` baseline. Although the full formula is not expanded on the result page, the interpretation is straightforward: it measures the percentage improvement relative to the baseline model.

## Value and Boundary of the Paper

KD-NAFI is valuable because it is one of the few CTR papers that puts **interpretability, strong interaction learning, and lightweight deployment** into the same framework while still reporting clear quantitative evidence.

Its boundary should also be stated clearly: interpretability here mainly means feature contribution and interaction-weight analysis, not natural-language explanation. Distillation also happens only between CTR predictors, not between LLM modules. So it is best treated as a representative interpretable CTR predictor paper, not as a prompt-optimization or explanation-generation paper.

