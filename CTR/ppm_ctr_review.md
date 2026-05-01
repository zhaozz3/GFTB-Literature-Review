## Traditional CTR Prediction Literature Example: PPM

**Bibliographic Information**

- **Title**: PPM: A Pre-trained Plug-in Model for Click-through Rate Prediction
- **Authors**: Yuanbo Gao, Peng Lin, Dongyue Wang, Feng Mei, Xiwei Zhao, Sulong Xu, Jinghe Hu
- **Year**: 2024
- **Venue**: *Companion Proceedings of the ACM Web Conference 2024 (WWW 2024 Companion)*
- **Research Topic**: CTR prediction; pre-training; cold-start; multimodal recommendation; plug-in architecture
- **Recommended APA Citation**: Gao, Y., Lin, P., Wang, D., Mei, F., Zhao, X., Xu, S., & Hu, J. (2024). PPM: A pre-trained plug-in model for click-through rate prediction. In *Companion Proceedings of the ACM on Web Conference 2024*.
- **Note**: This paper is highly industry-oriented and is especially relevant for cold-start and multimodal integration settings.

PPM addresses a different question from many classical CTR papers. Instead of asking only how to better model structured ID features, it asks: **how can a CTR system leverage large-scale pre-trained multimodal knowledge without incurring unacceptable online latency?** Traditional ID-based recommenders struggle in cold-start scenarios, but directly placing a large pre-trained model into the online serving path can dramatically increase latency. To address this, the paper proposes **PPM (Pre-trained Plug-in Model)**.

## Main Innovation and Research Logic

The main innovation of PPM is not merely “using pre-training for CTR”, but rather **bridging pre-trained multimodal representation learning and industrial CTR serving through a plug-in design**.

The implementation logic is:
1. Use multimodal content such as item text and images in pre-training;
2. Produce intermediate representations that can be cached;
3. Plug these representations into an ID-based CTR/ranking backbone;
4. Limit how many parameters must remain active online;
5. Improve prediction quality while controlling latency.

## Model Usage / Architecture Understanding

Read PPM as an offline-to-online plug-in architecture:
- `Backbone`: the serving system remains an ID-based CTR model.
- `Innovation module`: multimodal knowledge is learned offline, cached, and injected as a plug-in rather than put directly on the online path.
- `Why it matters`: the method targets the performance-latency trade-off in industrial recommendation.
## Key Terms

- **CTR**: click-through rate, usually modeled as click probability.
- **Cold-start**: the difficulty of predicting behavior for new items or users with sparse history.
- **Pre-trained model**: a model trained on large-scale upstream data before downstream adaptation.
- **Plug-in model**: a module that enhances an existing backbone without fully replacing it.
- **Multimodal features**: signals from different modalities, such as text and image.
- **Latency**: online serving delay, a major practical constraint in industrial recommender systems.

## Datasets and Research Setting

PPM is evaluated on **real JD e-commerce production CTR data**. Section 4.1 explicitly states that each sample contains:
- user historical behavior sequence
- target item
- click label

This means the paper is not structured as a public-benchmark reproducibility paper. It is a typical **industrial production dataset** paper, with strong emphasis on:
- cold-start behavior
- large pre-trained module cost
- iteration efficiency
- serving latency

The paper also explicitly distinguishes **cold-start** and **warm** settings in its analysis.

## Evaluation Benchmarks

The comparison is organized around industrial baselines and progressively enhanced variants rather than around generic public-benchmark families. The paper directly compares:
- `Base`
- `QM&EP`
- `UMM`
- `Base+QM&EP`
- `URM`

For the online A/B test, the setting is also concrete:
- **JD homepage recommendation service**
- **10 consecutive days**
- baseline is the previously deployed online CTR model

The online business metrics are explicitly defined as:
- `UCTR = #clicks / #users`
- `UCVR = #orders / #users`

## Experimental Results

The main benchmark page is shown below.

![benchmark](figures/ppm_page6_full.png)

*Benchmark figure note: cropped from Tables 2 and 3 in the original PPM paper.*

From `Table 2`, the strongest configuration **URM** reports the following offline results:
- **Click AUC**: `0.7343`
- **Click P@2**: `0.2363`
- **Order AUC**: `0.7722`
- **Order P@2**: `0.2313`
- **Average AUC**: `0.7532`
- **Average P@2**: `0.2338`

Compared directly with the `Base` configuration:

| Setting | Click AUC | Click P@2 | Order AUC | Order P@2 | Average AUC |
|---|---:|---:|---:|---:|---:|
| Base | 0.7252 | 0.2310 | 0.7612 | 0.2305 | 0.7432 |
| **URM** | **0.7343** | **0.2363** | **0.7722** | **0.2313** | **0.7532** |

So the average AUC gain is **+0.0100**.

The paper also explicitly states that:
- PPM / URM is especially helpful under **cold-start** conditions;
- both offline experiments and online A/B tests support the method;
- the gains come **without an increase in online latency**.

So the right summary is not just “PPM improves performance”, but rather that **on real JD production CTR data, URM/PPM increases average AUC from 0.7432 to 0.7532, improves both click and order tasks, and is reported to do so without extra online latency.**

## Metrics and How They Are Computed

### AUC

`AUC = P(s+ > s-)`

This measures ranking quality.

### LogLoss

`LogLoss = - [ y log(p) + (1-y) log(1-p) ]`

This measures click-probability quality.

### Why PPM Needs More Than Offline Metrics

PPM is especially notable because its evaluation goes beyond offline ranking metrics. It also cares about:
- serving latency
- iteration efficiency
- online A/B test performance
- cold-start slice performance

That makes it much more deployment-oriented than a standard public-benchmark CTR paper.

## Literature Value and Boundaries

PPM is valuable because it shows that newer CTR work increasingly incorporates richer content signals and pre-trained knowledge while still respecting system-deployment constraints. This is useful background for any project that treats CTR systems as more than an offline predictor.

Its boundary is still clear: PPM is a traditional CTR system paper. It improves prediction and deployment design, but it does not generate factor-level natural-language explanations and does not use explanations as textual gradients. It is therefore a strong prediction-side and systems-side reference, not an explanation-generation or prompt-optimization paper.

