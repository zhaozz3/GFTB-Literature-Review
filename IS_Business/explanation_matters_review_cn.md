## IS / 商科文献示例：Explanation Matters

**文献信息**
- **题目**：Explanation matters: How explainable artificial intelligence can improve decision-making in human-AI interaction
- **作者**：Hamm 等
- **年份**：2023
- **期刊**：Electronic Markets
- **研究主题**：human-AI interaction；explainability；decision-making
- **备注**：这是一篇非常贴近商科问题的经验论文，核心在于检验解释是否真的改善决策。

这篇文章直接回答了一个对商科期刊很重要的问题：解释是否真的能改善 human-AI interaction 中的决策？它的价值在于，不再停留在“透明度很好”这种抽象说法，而是把 explanation 当成影响 decision quality 的实际变量来研究。

## Research Logic Chain
- **Problem gap**：很多 XAI 讨论默认解释一定有益。
- **Why prior work was insufficient**：解释对决策后果的影响并不总是被直接检验。
- **Core idea**：评估 explainability 是否改变了人的决策质量与人机交互方式。

## Key Terms
- **Decision quality**：在使用 AI 支持时，决策结果是否更好。
- **Human-AI interaction**：人与 AI 输出共同参与的决策过程。
- **Explainability effect**：获得解释后所产生的行为效果。

## Data / Research Setting
这是一篇偏商科的经验研究，核心是决策行为，而不是模型 benchmark。

## Benchmark / Evaluation Logic
其核心评价逻辑是实验性的：比较有解释与无解释条件下的决策结果变化。

## Performance Comparison
论文的主要发现是：explanation matters，因为它会改变用户与 AI 的交互方式，并进一步影响决策效果。

## Architecture / Conceptual Explanation

这篇文章更像一条“行为评价链”：
- `输入`：不同 explanation 设计。
- `核心机制`：解释先影响理解，再影响信任与依赖校准。
- `关键信息`：解释质量最终要看它是否改善人的决策。
## How the Main Metrics / Evaluation Logic Work
这里的关键逻辑是行为结果：解释质量应当通过它是否改善用户决策来判断，而不只是看它听起来是否合理。

## Relevance to the Present Project Design
这篇文章对本项目非常重要，因为它支持这样一个判断：本项目中的 explanation quality 应该用 manager 或 analyst 的 downstream usefulness 来衡量，而不仅仅看技术外观是否“像解释”。
