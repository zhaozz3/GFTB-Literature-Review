## Traditional CTR Prediction Literature Example: FinalMLP

**Bibliographic Information**

- **Title**: FinalMLP: An Enhanced Two-Stream MLP Model for CTR Prediction
- **Authors**: Kelong Mao, Jieming Zhu, Liangcai Su, Guohao Cai, Yuru Li, Zhenhua Dong
- **Year**: 2023
- **Venue**: *Proceedings of the AAAI Conference on Artificial Intelligence (AAAI 2023)*
- **DOI**: 10.1609/aaai.v37i4.25577
- **Research Topic**: CTR prediction; two-stream architecture; MLP; feature gating; bilinear fusion
- **Recommended APA Citation**: Mao, K., Zhu, J., Su, L., Cai, G., Li, Y., & Dong, Z. (2023). FinalMLP: An enhanced two-stream MLP model for CTR prediction. *Proceedings of the AAAI Conference on Artificial Intelligence, 37*(4), 4552-4560. https://doi.org/10.1609/aaai.v37i4.25577
- **Note**: This paper revisits the design logic of two-stream CTR models and argues that a strengthened dual-MLP architecture can become a very strong baseline.

FinalMLP challenges a common assumption in CTR research: that a plain MLP branch must be paired with a specialized explicit interaction branch to be competitive. The authors argue that prior work may have underestimated what a carefully designed dual-MLP structure can do. They therefore propose **FinalMLP**, in which both branches are MLPs, while input differentiation and cross-stream fusion are handled by a **feature gating module** and an **interaction aggregation module**.

## Main Innovation and Research Logic

The main innovation of FinalMLP can be summarized as follows: **it strengthens a dual-MLP CTR architecture through stream-specific feature gating and group-wise bilinear fusion, rather than relying on a heavily customized explicit interaction branch.**

The implementation logic is:
1. Multi-field sparse features are mapped into embeddings;
2. A feature gating module produces stream-specific input views;
3. Two parallel MLP streams learn different representations;
4. A group-wise bilinear fusion module models cross-stream interactions;
5. The fused representation is used to predict CTR.

## Model Usage / Architecture Understanding

Read FinalMLP as controlled input differentiation plus dual-MLP fusion:
- `Backbone`: both representation learners are MLPs.
- `Innovation module`: feature gating makes the two streams see different input views, then bilinear fusion models cross-stream interaction.
- `Why it matters`: the paper shows that a carefully strengthened dual-MLP design can rival more specialized CTR backbones.
## Key Terms

- **CTR**: click-through rate, usually formulated as the probability that an impression receives a click.
- **Two-stream model**: a model with two parallel branches learning from different views.
- **Feature gating**: a gating mechanism that creates differentiated inputs for the two branches.
- **Bilinear fusion**: a fusion operator that models multiplicative interaction between two representations.
- **AUC**: ranking-oriented metric.
- **LogLoss**: probability-quality metric.

## Datasets and Research Setting

FinalMLP evaluates on **4 public benchmark datasets**, and `Table 1` reports their concrete scale:

| Dataset | #Instances | #Fields | #Features |
|---|---:|---:|---:|
| Criteo | 45,840,617 | 39 | 2,086,936 |
| Avazu | 40,428,967 | 22 | 1,544,250 |
| MovieLens | 2,006,859 | 3 | 90,445 |
| Frappe | 288,609 | 10 | 5,382 |

Beyond the offline benchmark, the paper also reports an **online A/B test in an industrial news recommender system**, explicitly stating that FinalMLP outperforms the deployed baseline in both offline and online evaluation.

## Evaluation Benchmarks

The direct comparison baselines listed in `Table 3` include:
- `Wide & Deep`
- `DeepFM`
- `DCN`
- `xDeepFM`
- `AutoInt+`
- `AFN+`
- `DeepIM`
- `MaskNet`
- `DCNv2`
- `EDCN`
- `DualMLP`

This benchmark set directly tests the paper’s main question: **can an enhanced dual-MLP model outperform more specialized explicit-interaction and two-stream CTR architectures?**

## Experimental Results

The main benchmark table is shown below.

![benchmark](figures/finalmlp_benchmark.png)

*Benchmark figure note: cropped from Table 3 in the original FinalMLP paper.*

The core AUC results of FinalMLP are:
- **Criteo**: `81.49`
- **Avazu**: `76.66`
- **MovieLens**: `97.20`
- **Frappe**: `98.61`

These results become more meaningful when directly compared with the strongest competing baselines:

| Model | Criteo | Avazu | MovieLens | Frappe |
|---|---:|---:|---:|---:|
| DCNv2 | 81.47 | 76.52 | 96.71 | 98.50 |
| EDCN | 81.42 | 76.57 | 96.98 | 98.47 |
| DualMLP | 81.42 | 76.57 | 96.98 | 98.47 |
| **FinalMLP** | **81.49** | **76.66** | **97.20** | **98.61** |

The ablation evidence in Figure 2 is also concrete:
- on **Avazu**, `DualMLP = 76.57`, `FinalMLP = 76.66`
- on **MovieLens**, `DualMLP = 96.98`, `FinalMLP = 97.20`
- on **Frappe**, `DualMLP = 98.47`, `FinalMLP = 98.61`

So the right benchmark conclusion is not only that FinalMLP “wins”, but that **it achieves the best AUC on all four public datasets, with stable if not huge gains over the strongest competing baselines.** For CTR architecture work, this kind of stable multi-benchmark advantage is much more convincing than a single large jump on one dataset.

## Metrics and How They Are Computed

### AUC

`AUC = P(s+ > s-)`

where `s+` is the score of a clicked sample and `s-` is the score of a non-clicked sample.

### LogLoss

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

where `y` is the true label and `p` is the predicted click probability.

### Why These Metrics Fit FinalMLP

FinalMLP needs to show both stronger ranking and better probability estimation, so AUC and LogLoss are the natural paired evaluation metrics.

## Literature Value and Boundaries

FinalMLP is useful as a representative of a modern CTR trend: performance gains do not always come from inventing ever more exotic operators; they can also come from reorganizing how known modules are used and fused.

Its boundary is clear: FinalMLP is still a predictor-architecture paper. It does not generate factor-level natural-language explanations, does not convert errors into semantic feedback, and does not use explanations as textual gradients. It is a strong prediction-side reference, not an explanation-generation or prompt-optimization method.

