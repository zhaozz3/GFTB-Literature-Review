## Traditional CTR Prediction Literature Example: FINAL

**Bibliographic Information**

- **Title**: FINAL: Factorized Interaction Layer for CTR Prediction
- **Authors**: Jieming Zhu, Qinglin Jia, Guohao Cai, Quanyu Dai, Jingjie Li, Zhenhua Dong, Ruiming Tang, Rui Zhang
- **Year**: 2023
- **Venue**: *Proceedings of the 46th International ACM SIGIR Conference on Research and Development in Information Retrieval (SIGIR 2023)*
- **DOI**: 10.1145/3539618.3591988
- **Research Topic**: CTR prediction; factorized interaction; higher-order interaction learning; efficient explicit interaction
- **Recommended APA Citation**: Zhu, J., Jia, Q., Cai, G., Dai, Q., Li, J., Dong, Z., Tang, R., & Zhang, R. (2023). FINAL: Factorized interaction layer for CTR prediction. In *Proceedings of the 46th International ACM SIGIR Conference on Research and Development in Information Retrieval* (pp. 2006-2010). https://doi.org/10.1145/3539618.3591988
- **Note**: This is a short but influential 2023 CTR architecture paper focused on improving the depth-efficiency trade-off of explicit feature interaction learning.

FINAL starts from a specific limitation in explicit interaction learning: many explicit interaction networks need substantial depth before they can capture sufficiently high-order interactions, which increases complexity and cost. The paper proposes **FINAL (Factorized Interaction Layer)** to make interaction order grow more efficiently in a relatively shallow architecture, inspired by fast exponentiation.

## Main Innovation and Research Logic

The main innovation of FINAL can be summarized as follows: **it introduces a shallow-but-high-order explicit interaction mechanism for CTR prediction, aiming to improve interaction strength without simply making the network much deeper.**

The implementation logic is:
1. Multi-field sparse features are embedded;
2. Embeddings are fed into factorized interaction blocks;
3. Interaction order grows efficiently through factorized composition;
4. The resulting interaction representations are aggregated;
5. A prediction head outputs CTR probability.

## Model Usage / Architecture Understanding

Read FINAL as a shallow-but-high-order explicit interaction network:
- `Backbone`: embeddings are pushed through stacked factorized interaction blocks.
- `Innovation module`: each block grows interaction order more efficiently than ordinary linear-depth stacking.
- `Why it matters`: the paper targets the depth-efficiency trade-off of explicit interaction learning.
## Key Terms

- **CTR**: click-through rate, typically modeled as click-probability prediction.
- **Explicit feature interaction**: directly constructed cross-field interactions rather than purely implicit MLP learning.
- **Factorized interaction**: interaction representation built by decomposition and recomposition.
- **Interaction order**: the number of jointly involved features in a combinational effect.
- **AUC**: ranking-oriented metric.
- **LogLoss**: probability-quality metric.

## Datasets and Research Setting

FINAL explicitly evaluates on **4 public benchmark datasets**, reusing the preprocessed versions released in prior work and following the same data splits and preprocessing pipeline. The actual datasets used in the paper are:

- **Criteo**
- **Avazu**
- **MovieLens**
- **Frappe**

The evaluation metrics are the standard CTR pair:
- **AUC**
- **LogLoss**

So this is a very standard offline structural CTR benchmark paper, not a sequence CTR or multimodal CTR paper.

## Evaluation Benchmarks

The main comparison baselines visible in `Table 1` include:
- `LR`: `78.86 / 75.16 / 93.42 / 93.56`
- `FM`: `80.22 / 76.13 / 94.34 / 96.71`
- `AFM`: `80.44 / 75.74 / 94.72 / 96.97`
- `CrossNet`: `79.47 / 75.45 / 93.85 / 94.19`
- `CrossNetV2`: `81.10 / 76.05 / 95.83 / 97.16`
- `CIN`: `80.96 / 76.26 / 96.02 / 97.76`
- `HiNet`: `81.39 / 76.49 / 96.97 / 98.45`
- `SAM`: `81.31 / 76.32 / 96.31 / 98.01`

These numbers are ordered as **Criteo / Avazu / MovieLens / Frappe**, and this benchmark design directly matches the paper’s question: **can a more depth-efficient explicit interaction layer outperform strong explicit and hybrid interaction baselines?**

## Experimental Results

The main benchmark table is shown below.

![benchmark](figures/final_benchmark_clean.png)

*Benchmark figure note: cropped from Table 1 in the original FINAL paper.*

From `Table 1`, the strongest variant **FINAL(2B)+KD** achieves:
- **81.56** on Criteo
- **76.67** on Avazu
- **97.20** on MovieLens
- **98.95** on Frappe

This is clearer when compared directly with the strongest baselines in the same table:

| Model | Criteo | Avazu | MovieLens | Frappe |
|---|---:|---:|---:|---:|
| CrossNetV2 | 81.10 | 76.05 | 95.83 | 97.16 |
| HiNet | 81.39 | 76.49 | 96.97 | 98.45 |
| SAM | 81.31 | 76.32 | 96.31 | 98.01 |
| **FINAL(2B)+KD** | **81.56** | **76.67** | **97.20** | **98.95** |

So the rigorous empirical conclusion is not just that FINAL is “generally stronger”, but that **FINAL(2B)+KD achieves the best reported result on all 4 public CTR benchmarks and maintains a stable advantage over strong baselines such as CrossNetV2, HiNet, and SAM.**

## Metrics and How They Are Computed

### AUC

`AUC = P(s+ > s-)`

where `s+` is the score of a clicked instance and `s-` is the score of a non-clicked instance.

### LogLoss

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

Lower LogLoss means the predicted click probabilities are closer to the true outcomes.

### Why These Metrics Fit FINAL

Because FINAL is an interaction-learning paper, the key empirical question is whether the new interaction layer improves both:
- ranking quality (`AUC`)
- probability estimation quality (`LogLoss`)

## Literature Value and Boundaries

FINAL is valuable as a recent representative of pure predictor-side CTR innovation, especially in higher-order explicit feature interaction learning. It helps define the modern traditional CTR baseline space.

Its boundary is clear: FINAL does not generate factor-level natural-language explanations, does not provide textual feedback, and does not support prompt optimization. It is a strong predictor paper, not an explanation-generation or optimization-loop paper.

