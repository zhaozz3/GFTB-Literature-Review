## Traditional CTR Prediction Literature Example: SIFAN

**Bibliographic Information**

- **Title**: Session interest model for CTR prediction based on feature co-action network
- **Authors**: Qianqian Wang, Fang'ai Liu, Xiaohui Zhao, Qiaoqiao Tan
- **Year**: 2025
- **Venue**: *Scientific Reports*, 15, 14571
- **DOI**: 10.1038/s41598-025-99671-9
- **Research Topic**: CTR prediction; session interest; behavior sequence; feature co-action network
- **Recommended APA Citation**: Wang, Q., Liu, F., Zhao, X., & Tan, Q. (2025). Session interest model for CTR prediction based on feature co-action network. *Scientific Reports, 15*, 14571. https://doi.org/10.1038/s41598-025-99671-9
- **Note**: This paper combines session-level interest modeling with explicit history-target feature co-action modeling.

SIFAN addresses two concrete weaknesses in many CTR models. First, historical behavior is often treated as one long undifferentiated sequence, without respecting session boundaries. Second, explicit feature-level interaction between historical behaviors and the target item is often weakened or hidden inside a generic deep architecture. To address this, the paper proposes **SIFAN**, which combines a `session interest extractor` with a `feature co-action network (FAN)`.

## Main Innovation and Research Logic

The main innovation of SIFAN can be summarized as follows: **it jointly models session-level interest evolution and explicit history-target feature co-action within one CTR predictor.**

The implementation logic is:
1. Input user profile, scene profile, target item, and historical behavior sequence;
2. Use `FAN` to learn feature co-action between each historical behavior and the target item;
3. Split the behavior history into sessions and use `GRU + AUGRU` to extract session interest;
4. Fuse the two representations and output CTR probability.

## Model Usage / Architecture Understanding

Read SIFAN as two coordinated branches:
- `Backbone`: one branch models session-level interest evolution, the other models history-target feature co-action.
- `Innovation module`: session structure and explicit co-action are both kept visible in the architecture.
- `Why it matters`: it avoids collapsing long behavior history into one undifferentiated sequence representation.
## Key Terms

- **Session Interest**: interest representation defined at the session level instead of over one single long sequence.
- **FAN**: Feature Co-Action Network, the module that explicitly models interaction between historical behavior and the target item.
- **AUGRU**: GRU with attentional update gate, used to align session interest with the target advertisement.
- **AUC / LogLoss / RMSE**: the three metrics reported in the paper.

## Datasets and Research Setting

`Table 1` in the paper gives concrete dataset statistics and states that the data are split with **80% training data**. The most directly citable information is:

| Dataset | customers | items | features | samples |
|---|---:|---:|---:|---:|
| Books | 46,375 | 43,728 | 49,852 | 277,303 |
| Electronics | 29,209 | 34,816 | 35,784 | 194,771 |
| Avazu | 78,437 | 69,175 | 104,242 | 830,163 |
| Criteo | 28,625 | 30,216 | 43,127 | 196,329 |

So SIFAN is not evaluated only on Amazon-style behavior data. It is evaluated on:
- `Amazon Books`
- `Amazon Electronics`
- `Avazu`
- `Criteo`

This matters because the benchmark spans both sequence-rich e-commerce settings and classic advertising CTR settings.

## Evaluation Benchmarks

The main compared baselines are:
- `DIN`
- `DIEN`
- `DSIN`
- `CAN`
- `CTRL`
- `DONN`

These baselines are closely aligned with the paper’s question because they cover sequence interest modeling, session modeling, and feature interaction enhancement.

## Experimental Results

The main reported results come from `Table 2` and `Table 3`.

### LogLoss (`Table 2`)

| Model | Books | Electronics | Avazu | Criteo |
|---|---:|---:|---:|---:|
| DIN | 0.1382 | 0.1406 | 0.2951 | 0.3625 |
| DIEN | 0.1313 | 0.1354 | 0.2836 | 0.3587 |
| DSIN | 0.1142 | 0.1225 | 0.2472 | 0.3473 |
| CAN | 0.1094 | 0.1184 | 0.2289 | 0.3366 |
| CTRL | 0.0997 | 0.1152 | 0.2176 | 0.3262 |
| DONN | 0.0962 | 0.1079 | 0.2101 | 0.3127 |
| **SIFAN** | **0.0873** | **0.1023** | **0.2052** | **0.3004** |

### RMSE (`Table 3`)

| Model | Books | Electronics | Avazu | Criteo |
|---|---:|---:|---:|---:|
| DIN | 0.4527 | 0.5245 | 0.4725 | 0.5825 |
| DIEN | 0.4476 | 0.4913 | 0.4601 | 0.5797 |
| DSIN | 0.4434 | 0.4876 | 0.4579 | 0.5634 |
| CAN | 0.4321 | 0.4725 | 0.4432 | 0.5468 |
| CTRL | 0.4252 | 0.4643 | 0.4402 | 0.5346 |
| DONN | 0.4063 | 0.4431 | 0.4215 | 0.5105 |
| **SIFAN** | **0.3756** | **0.4032** | **0.4001** | **0.4936** |

This is much more rigorous than saying only that SIFAN “outperforms baselines”: it gives table-level best values on 4 datasets across 2 main result tables.

One necessary nuance: the paper also references `Fig. 4` for AUC, but in the currently extractable PDF text, there is no stable, structured AUC table like `Table 2/3`. So the safest review practice is to treat the **LogLoss and RMSE tables as the main hard evidence** rather than inventing precise AUC numbers from a figure.

## Metrics and How They Are Computed

### AUC

`AUC = P(s+ > s-)`

This measures ranking quality. Higher values mean clicked instances are more likely to be ranked above non-clicked instances.

### LogLoss

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

This measures the consistency between predicted click probabilities and true click labels. Lower is better.

### RMSE

`RMSE = sqrt( (1/|T|) * Σ_i (y_i - ŷ_i)^2 )`

The paper uses RMSE as an overall prediction-error metric. Lower values indicate lower overall prediction error.

### Why These Metrics Fit SIFAN

SIFAN tries to improve:
- ranking quality;
- probability estimation quality;
- overall prediction error.

For a model that jointly targets session interest and feature co-action, reporting multiple metrics is reasonable.

## Value and Boundary of the Paper

SIFAN is valuable because it represents a newer CTR trend: predictors are moving beyond static feature interaction toward richer behavior understanding, especially session-aware modeling and history-target interaction.

Its boundary remains clear: SIFAN is still a traditional predictor architecture. It does not produce natural-language explanations, does not output factor-level rationales, and does not use textual feedback for optimization. It belongs in the “stronger CTR predictor” literature, not in explanation generation or prompt optimization literature.

