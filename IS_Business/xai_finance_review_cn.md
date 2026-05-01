## IS / 商科文献示例：XAI in Finance Review

**文献信息**
- **题目**：Applications of Explainable Artificial Intelligence in Finance—a systematic review of Finance, Information Systems, and Computer Science literature
- **作者**：Patrick Weber, K. Valerie Carl, Oliver Hinz
- **年份**：2024
- **期刊**：*Management Review Quarterly*, 74, 867-907
- **DOI**：10.1007/s11301-023-00320-0
- **研究主题**：金融中的 XAI；系统综述；管理与监管相关性
- **备注**：这是一篇系统文献综述，不是 benchmark 论文。它的核心价值在于检索规模和综述结构都很扎实。

这篇论文梳理的是 explainable AI 在金融领域的应用。金融是一个对透明性、可追溯性和监管要求都非常高的场景，因此这篇综述的意义不只是“有哪些方法”，更在于说明 Finance、IS 和 CS 三个学科脉络中关于 XAI in finance 的研究如何分布与演化。

## Research Logic Chain
- **Problem gap**：金融领域不能只追求预测能力，还必须保证决策可解释、可追溯。
- **Why prior work was insufficient**：XAI in finance 的研究分散在多个学科中，缺乏统一整理。
- **Core idea**：通过系统文献综述，把 Finance、IS、CS 三个来源的金融 XAI 研究汇总起来。

## Key Terms
- **Systematic literature review (SLR)**：基于明确检索、筛选与编码规则的系统综述。
- **Auditability**：决策是否可被检查、追责和解释。
- **Highly regulated domain**：监管要求很强、透明性要求很高的应用领域。
- **Transparent models / post-hoc explainability**：论文重点讨论的两大解释方法家族。

## Data / Research Setting

这篇论文分析的不是任务样本，而是筛选后的文献集合。论文报告了非常具体的 SLR 流程：
- 初始共筛到 **2,022 篇文章**
- 经过题目/摘要/关键词与全文筛选后，保留 **54 篇**
- backward search 又补入 **6 篇**
- 最终 review corpus 一共 **60 篇文章**

检索范围覆盖：
- 顶级 **Finance journals**
- **8 本 IS basket journals**
- 顶级 **Computer Science journals**
- 以及 **ACM Digital Library、AIS eLibrary、IEEE Xplore** 等数据库

检索时间限定为 **2010 年以后**，以便更聚焦于近年的 XAI 发展。

## Benchmark / Evaluation Logic

这不是经验 benchmark 论文，因此没有 AUC 或模型对比表。它的评价逻辑是综述式的：
1. 金融 XAI 文献到底有多少
2. 发表量如何随时间变化
3. 哪些方法类型最常见
4. 哪些金融应用场景研究较充分
5. 哪些场景仍明显不足

## Performance Comparison

论文给出了多项具体、可量化的综述发现：
- 最终纳入样本为 **60 篇文章**
- 文献高度集中在近几年，其中 **2020 年有 17 篇**，**2021 年有 16 篇**
- 方法类型中，占比最大的是 **theoretical model + real market datasets（32 篇）**
- 其次是 **experiments（18 篇）**
- 其他类别包括 **2 篇 qualitative literature review、3 篇 algorithm demonstration、3 篇 algorithm review、2 篇 case study**

论文还给出了比较明确的主题覆盖结构：
- **risk management、portfolio optimization、stock market applications** 已经研究较多
- **anti-money laundering** 被明确指出研究不足
- 文献中同时包含 **transparent models** 和 **post-hoc explainability**，但近年后者更受偏好

所以，这篇综述的价值不在于“哪个模型更强”，而在于它以较大规模、较严谨的方式把金融 XAI 研究的结构梳理出来了。

## Architecture / Conceptual Explanation

这篇论文更像一个 review matrix：
- `输入端`：金融应用场景与建模设置。
- `核心机制`：不同 XAI 方法家族服务于不同解释目标。
- `关键信息`：论文是在组织“哪些方法在金融里被怎样使用”。
## How the Main Metrics / Evaluation Logic Work

这里的评价逻辑是典型的综述逻辑：
- article counts 用于表示领域规模和增长速度
- methodological coding 用于表示主流研究方法
- area coding 用于表示哪些金融应用已较成熟、哪些仍被忽视

这与论文目标完全一致，因为它的任务是组织领域，而不是比较训练模型。

## Relevance to the Present Project Design

这篇综述很适合用来论证：在 **高风险、强监管的 business domain** 中，可解释性不是附属功能，而是核心要求。同时，它也能提供一个比较扎实的事实基础，说明金融 XAI 已经形成了一定规模的跨学科文献，但像 anti-money laundering 这样的方向仍存在明显研究空白。
