## IS / 商科文献示例：Human-Centered XAI Evaluation

**文献信息**
- **题目**：Human-centered evaluation of explainable AI applications: a systematic review
- **作者**：Nothdurft 等
- **年份**：2024
- **期刊**：Frontiers in Artificial Intelligence
- **研究主题**：人本 XAI 评估；解释 usefulness；评价维度
- **备注**：这是一篇较新的系统综述，核心是从人本视角讨论 explainable AI 应该如何被评估。

该综述认为，explainable AI 不能只用技术准确率来评价。解释质量必须结合用户目标、决策情境、感知 usefulness、trust 和 actionability 来判断。对商科和 IS 研究而言，这篇文章尤其重要，因为它提供了一个把 AI artifact 当作 decision-support tool 来评价的语言框架，而不是只把它看成预测器。

## Research Logic Chain
- **Problem gap**：XAI 评估往往仍停留在模型中心视角。
- **Why prior work was insufficient**：单纯技术指标无法反映 human usefulness。
- **Core idea**：围绕用户、任务情境和使用结果来组织 XAI 评价。
- **Implementation chain**：
  1. 回顾经验性 XAI 研究；
  2. 分类评价维度；
  3. 比较人本测量方式；
  4. 提炼对 artifact 评估的启示。

## Key Terms
- **Human-centered evaluation**：围绕用户需求与结果来评价 AI 系统。
- **Actionability**：解释是否支持用户采取行动。
- **Explanation usefulness**：解释是否真的能被用户用起来。

## Data / Research Setting
这是一篇系统综述，不是算法论文。它的研究设置是跨领域的 XAI 应用文献语料。

## Benchmark / Evaluation Logic
其核心评价逻辑是概念性的：解释不应只看 plausibility 或技术 fidelity，还应看用户理解、trust、满意度和决策质量。

## Performance Comparison
该文不提供传统 benchmark 表，而是给出评价框架综合。其主要贡献是识别出一组应当补充技术指标的人本维度。

## Architecture / Conceptual Explanation

这篇文章更像一个“人本评价框架”：
- `输入端`：解释设计、用户特征和任务情境共同决定结果。
- `核心机制`：解释质量不是绝对值，而是情境相关的。
- `关键信息`：不能只用 prediction quality 来评价 XAI。
## How the Main Metrics / Evaluation Logic Work
这里的关键不是单一指标，而是多维评价逻辑：解释质量只有在用户理解、任务支持、trust 和行为结果中才真正有意义。

## Relevance to the Present Project Design
这篇文章与本项目高度相关，因为本项目不能只依赖 prediction quality 评估。它支持把 human usefulness、actionability 和 explanation quality 纳入评价设计。
