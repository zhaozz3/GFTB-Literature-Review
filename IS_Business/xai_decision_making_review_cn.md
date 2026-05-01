## IS / 商科文献示例：XAI 与商业决策

**文献信息**

- **题目**：Explainable AI for enhanced decision-making
- **作者**：Kristof Coussement, Mohammad Zoynul Abedin, Mathias Kraus, Sebastian Maldonado, Kazim Topuz
- **年份**：2024
- **期刊**：*Decision Support Systems*, 184, 114276
- **DOI**：10.1016/j.dss.2024.114276
- **研究主题**：explainable AI；decision support；business decision-making；IS research；XAI dimensions；future research agenda
- **推荐引用格式（APA）**：Coussement, K., Abedin, M. Z., Kraus, M., Maldonado, S., & Topuz, K. (2024). Explainable AI for enhanced decision-making. *Decision Support Systems, 184*, 114276. https://doi.org/10.1016/j.dss.2024.114276
- **备注**：这篇文章不是方法论文，而是一篇 DSS special issue 语境下的 editorial / positioning paper，重点在于解释为什么 XAI 应被理解为 business decision-support capability。

这篇文章的核心目的，不是提出新的 explainable AI 技术，而是回答一个更偏 IS 与商科的问题：**如果把 XAI 放入 business decision-making 语境中，它究竟意味着什么、应该如何评价、又能打开哪些研究机会？** 作者认为，XAI 的意义不应停留在“模型是否可解释”，而应进一步落到“是否改善决策支持过程、是否提升判断质量、是否带来管理价值”。

从贡献角度看，这篇文章的重点不在算法创新，而在于 **研究定位与评价框架的重构**。它把 explainability 从一个偏技术的属性，重新定位为 business-facing AI artifact 中与 decision support、trust、transparency 和 managerial usefulness 相关的关键能力。

## Research Logic Chain

- **问题缺口**：很多 XAI 讨论仍停留在技术可解释性层面，较少直接连接 business decision-making。
- **既有工作不足之处**：如果只把 explainability 理解为模型解释输出，就很难回答它为什么在商科 / IS 研究中重要。
- **论文核心思路**：把 XAI 放进 business decision support 语境中，并通过 data、method、application 等宽泛维度来组织这一领域。
- **实现链条**：
  1. 将 AI 放入 business decision-making 场景；
  2. 说明 explainability 为什么是 decision support 的关键组成部分；
  3. 用若干宽泛维度来组织 XAI 研究；
  4. 在此基础上提炼未来研究机会。
- **验证逻辑**：这是一篇 editorial / positioning paper，其说服力来自概念整合、领域 framing 与研究议程，而不是实验 benchmark。

## Key Terms

- **Explainable AI**：帮助人理解 AI 输出与决策逻辑的能力。
- **Decision support**：帮助决策者做出更好判断的系统功能。
- **Business decision-making**：组织中的运营或战略决策情境。
- **Managerial usefulness**：解释是否对管理判断真正有帮助。
- **Framing**：用来组织和定位研究问题的概念框架。
- **Research agenda**：从现有讨论中导出的未来研究方向。

## Data / Research Setting

这篇文章不是典型实证研究，因此没有传统意义上的训练数据集或 benchmark dataset。它的“研究设置”更准确地说是：

- 一个面向 Decision Support Systems / Information Systems 语境的 framing / editorial setting
- 对多个 business decision-making 场景中的 XAI 研究进行概念整合

文中提到的应用背景包括医疗预测、剩余寿命预测、学生流失预测、人口贩运识别、反洗钱检测等。这些案例的作用不是构成统一数据集，而是说明 XAI 已经进入多个高影响决策场景。

## Benchmark / Evaluation Logic

由于这篇文章不是算法 benchmark 论文，它并不提供 AUC、accuracy 或 leaderboard。它更重要的是提供一种 **evaluation logic**：

1. XAI 是否帮助用户更好理解 AI 输出？
2. XAI 是否改善了 business decision-making 的质量？
3. XAI 是否增强了 trust、transparency 与 accountability？
4. XAI 的价值是否真正体现在具体决策情境中，而不只是停留在模型解释层面？

它最重要的启发在于：评价 XAI 时，不应只问“解释是否存在”，而应进一步问“解释是否改善了决策支持过程”。

## Performance Comparison

由于这不是实证 benchmark 论文，更适合用结构化方式总结其贡献：

| Dimension | This Paper's Contribution | Why It Matters |
|---|---|---|
| Research positioning | Places XAI inside IS and DSS research | helps legitimate XAI as a business/IS topic |
| Conceptual framing | Organizes XAI research into data, method, and application dimensions | provides a reusable structure for literature review and theory building |
| Decision support logic | Links explainability with better informed decisions | shifts attention from technical interpretability to managerial usefulness |
| Research agenda | Identifies future opportunities for IS scholars | helps define publishable research questions in business journals |

这张表的意义不在算法比较，而在于说明：这篇文章的重要价值是为 XAI in business decision-making 提供了一个更清楚的研究定位。

## Conceptual Summary

这篇论文更像一条“决策支持链”：
- `输入端`：不同 XAI 方法提供不同解释属性。
- `核心机制`：这些属性先改变人的决策过程，而不是直接改变结果。
- `关键信息`：它关心的是 XAI 如何改变决策，而不只是提高透明度。
## Review Logic

由于这篇文章不是算法 benchmark 论文，它并不通过 AUC、accuracy 之类的公式评价模型性能。它的逻辑主要是概念性的、领域定位式的。真正关心的问题包括：

1. 应该如何在 business decision-making 场景中理解 XAI？
2. explainability 会给 decision support 带来什么类型的价值？
3. 哪些宽泛维度有助于组织这一领域的研究？
4. 这种 framing 会为 IS 与商科研究打开哪些未来机会？

这套逻辑的重要性在于：它把关注点从纯粹 model-centric 的 explainability，转向更强调 decision-centric 的 explainability。

## Literature Value and Boundaries

这篇文章的主要价值，在于作为一篇 **研究定位与评价 framing** 的支撑文献，尤其适合用于把技术型 explainability 讨论与 managerial usefulness、decision support 等商科 / IS 语言连接起来。

同时，它的边界也很明确。它并不提供 semantic feedback generation、prompt optimization 或 factor-level faithfulness control 的技术机制。它的贡献主要是概念性的、editorial 式的，而不是方法性的。

