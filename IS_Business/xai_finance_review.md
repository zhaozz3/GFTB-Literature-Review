## IS / Business Literature Example: XAI in Finance Review

**Bibliographic Information**
- **Title**: Applications of Explainable Artificial Intelligence in Finance—a systematic review of Finance, Information Systems, and Computer Science literature
- **Authors**: Patrick Weber, K. Valerie Carl, Oliver Hinz
- **Year**: 2024
- **Venue**: *Management Review Quarterly*, 74, 867-907
- **DOI**: 10.1007/s11301-023-00320-0
- **Research Topic**: XAI in finance; systematic review; managerial and regulatory relevance
- **Note**: This is a systematic literature review, not a benchmark paper. Its strength lies in the scale and structure of the review process.

This paper reviews how explainable AI is being used in finance, a domain where transparency, traceability, and regulation matter more than in many ordinary prediction settings. Its main contribution is to organize the literature across Finance, Information Systems, and Computer Science.

## Research Logic Chain
- **Problem gap**: Finance needs more than predictive power; it needs traceable and explainable AI decisions.
- **Why prior work was insufficient**: Research on XAI in finance was scattered across multiple disciplines.
- **Core idea**: Conduct a systematic literature review that aggregates Finance, IS, and CS work on XAI in finance.

## Key Terms
- **Systematic literature review (SLR)**: a structured review procedure based on explicit search, screening, and coding rules.
- **Auditability**: whether decisions can be inspected and justified.
- **Highly regulated domain**: a setting where transparency and compliance requirements are unusually strong.
- **Transparent models / post-hoc explainability**: two major families of explainability approaches discussed in the review.

## Data / Research Setting

The “data” of this paper are screened publications rather than empirical task instances. The paper reports a very concrete SLR process:
- it initially screened **2,022 articles**
- after title/abstract/keyword and full-text screening, **54 articles** remained
- a backward search added **6 more**
- the final review corpus therefore contains **60 articles**

The search focuses on:
- top-ranked **Finance journals**
- the **8 IS basket journals**
- top **Computer Science journals**
- databases such as **ACM Digital Library**, **AIS eLibrary**, and **IEEE Xplore**

The search period is restricted to **2010 onward** to capture recent XAI development in a rapidly changing research landscape.

## Benchmark / Evaluation Logic

This is not an empirical benchmark paper, so it does not report AUC or model-comparison tables. Its evaluation logic is synthesis-based:
1. how many finance-XAI papers exist
2. how publication volume evolves over time
3. which methods dominate
4. which finance areas are well studied
5. which areas remain underexplored

## Performance Comparison

The paper provides several concrete review-scale findings:
- the final corpus contains **60 articles**
- publication volume is concentrated in recent years, with **17 articles in 2020** and **16 in 2021**
- methodologically, the largest group is **theoretical model + real market datasets (32 articles)**
- followed by **experiments (18 articles)**
- smaller categories include **2 qualitative literature reviews**, **3 algorithm demonstrations**, **3 algorithm reviews**, and **2 case studies**

The review also identifies substantive coverage patterns:
- areas such as **risk management**, **portfolio optimization**, and **stock-market applications** are relatively well studied
- **anti-money laundering** is explicitly identified as under-studied
- the literature includes both **transparent models** and **post-hoc explanation methods**, but the latter have become increasingly favored

So the paper’s contribution is not “which model performs best”, but rather that it maps the structure of XAI-in-finance research at a much larger and more disciplined scale than a narrative review.

## Architecture / Conceptual Explanation

Read the paper as a review matrix:
- `Input`: finance application plus modeling setup.
- `Core mechanism`: different XAI families are matched to different explanation goals.
- `Key message`: the paper organizes where and why specific XAI approaches are used in finance.
## How the Main Metrics / Evaluation Logic Work

The evaluation logic is review-oriented:
- article counts indicate field size and growth
- methodological coding indicates what kinds of studies dominate
- area coding indicates which finance applications are mature or neglected

This makes sense because the paper’s purpose is to organize the field rather than compare trained models.

## Relevance to the Present Project Design

This review is valuable because it helps frame explainability as especially important in **high-stakes, regulated business domains**. It also provides concrete evidence that XAI in finance is already a sizable interdisciplinary literature, while still containing clear gaps such as anti-money laundering.
