## IS / Business Literature Example: Why Am I Seeing This Ad? The Effect of Ad Transparency on Ad Effectiveness

**Bibliographic Information**

- **Title**: Why Am I Seeing This Ad? The Effect of Ad Transparency on Ad Effectiveness
- **Authors**: Tami Kim, Kate Barasz, Leslie K. John
- **Year**: 2019
- **Venue**: *Journal of Consumer Research*, 45(5), 906–932
- **DOI**: 10.1093/jcr/ucy039
- **Research Topic**: ad transparency; ad effectiveness; information flows; privacy; personalization; platform trust
- **Recommended APA Citation**: Kim, T., Barasz, K., & John, L. K. (2019). Why am I seeing this ad? The effect of ad transparency on ad effectiveness. *Journal of Consumer Research, 45*(5), 906–932. https://doi.org/10.1093/jcr/ucy039
- **Note**: This is not an IS methods paper, but it provides especially strong theoretical and business support for why advertising explanations and transparency matter.

The core question of this paper is not “how to optimize an ad CTR model,” but rather: **when a platform tells consumers why they are seeing an ad, how does that transparency affect ad effectiveness?** Kim et al. (2019) note that as digital advertising increasingly relies on user data and personalized targeting, consumers and regulators increasingly demand ad transparency—that is, disclosure of why a particular ad was shown. However, whether ad transparency necessarily improves advertising effectiveness is not obvious. Explanations may make ads seem more relevant, but they may also make consumers feel that the information use behind targeting is overly “creepy.” To explain this tension, the authors develop and test a key theory: ad transparency helps or hurts depending on whether the information flows revealed by the explanation are consistent with consumers’ social norms.

From an innovation perspective, the paper’s major contribution lies in moving advertising-explanation research beyond the simple question of whether “more explanation is better” and toward the more nuanced question of **which explanations are perceived as acceptable and which trigger backlash**. The paper introduces two key distinctions:

- whether the information is obtained **within-site** or **cross-site**
- whether the information is **stated** by the consumer or **inferred** by the firm

The latter in each pair is more likely to be perceived as unacceptable. The most important contribution of the paper is therefore this: **the value of advertising explanations is not monotonic; it depends on whether the targeting logic exposed by the explanation violates consumers’ normative expectations about information flows.**

## Research Logic Chain

- **Problem gap**: Markets and regulators demand ad transparency, but it remains unclear whether such explanations increase or decrease ad effectiveness.
- **Why prior work was insufficient**: If transparency is treated only as “more information disclosure,” it cannot explain why some explanations increase trust while others increase privacy concerns and backlash.
- **Core idea of the paper**: Consumers’ reactions to ad explanations depend on whether the information flows revealed by those explanations are perceived as acceptable.
- **Implementation chain**:
  1. Develop the theory from offline information-sharing norms;
  2. Use Study 1 to identify how consumers distinguish acceptable from unacceptable information flows;
  3. Use Studies 2 and 3 to test how different information-flow disclosures affect ad effectiveness;
  4. Use Study 4 to test the moderating role of platform trust;
  5. Use field studies (Studies 5a/5b) to validate the effects in a real-world setting.
- **Validation logic**: The paper uses multiple experiments and field studies to test the relationship between transparency disclosure, privacy concern, trust, and ad effectiveness.

## Key Terms

- **Ad transparency**: the practice of disclosing to users why an ad is being shown to them.
- **Ad effectiveness**: the extent to which an advertisement generates interest, clicks, purchases, or other favorable responses.
- **Information flows**: consumers’ normative beliefs about how their information should move between different parties.
- **Within-site information**: information obtained within the same website or platform where the ad appears.
- **Cross-site information**: information obtained across websites or platforms.
- **Stated information**: information actively provided by the consumer.
- **Inferred information**: information inferred by the platform or firm from behavioral patterns or data.
- **Platform trust**: the level of trust consumers place in the platform on which the ad appears.

## Data / Research Setting

This is not a machine-learning dataset paper. Instead, it consists of **multiple experiments and field studies**. The research design includes:

- **Study 1**
  - an inductive study identifying how consumers judge whether information flows are acceptable
- **Studies 2 and 3**
  - experiments manipulating ad transparency disclosures and testing the effect of different information-flow disclosures on ad effectiveness
- **Study 4**
  - a study testing the moderating role of platform trust
- **Studies 5a and 5b**
  - field studies in a loyalty-program website setting

The importance of this design is that the paper does not rely only on self-reported attitudes. Instead, it builds the mechanism through:

- concept identification
- controlled experiments
- real-world field evidence

Together, these establish the mechanism chain from **ad transparency -> privacy concern / trust -> ad effectiveness**.

## Benchmark / Evaluation Logic

This paper has no algorithm benchmark, but it has a very clear **business evaluation logic**. Its core evaluative questions include:

1. Does transparency disclosure increase ad attractiveness and purchase interest?
2. Do different forms of information-flow disclosure reduce ad effectiveness?
3. Does this effect occur through the balance between privacy concern and personalization desire?
4. Does platform trust change the effect of transparency?

This means the paper is ultimately evaluating not model accuracy, but:

- whether explanations change consumer perceptions
- whether explanations affect ad effectiveness
- whether explanation value varies depending on trust and context

## Performance Comparison

Because this is not an algorithm paper, its contributions are better summarized structurally:

| Dimension | This Paper’s Contribution | Why It Matters |
|---|---|---|
| Ad explanation logic | Shows that “why this ad” explanations can help or hurt | explains why explanation is not always beneficial |
| Information-flow theory | Distinguishes acceptable vs. unacceptable information flows | gives a theory for explanation acceptance |
| Trust mechanism | Identifies platform trust as a moderator | shows business value depends on context |
| Field evidence | Demonstrates real-world effects in a loyalty-program setting | strengthens managerial relevance |

The key value of this summary is that it shows the managerial value of advertising explanations is not unconditional; it depends jointly on explanation content and business context.

## Architecture / Conceptual Explanation

Read the paper as a mechanism chain rather than an algorithm:
- `Trigger`: the platform explains why an ad is shown.
- `Core mechanism`: consumers judge whether the revealed information flow is acceptable.
- `Key moderation`: platform trust changes how that interpretation translates into ad outcomes.
## How the Main Metrics / Evaluation Logic Work

Because this is not an algorithm paper, it does not rely on AUC or LogLoss. Yet its evaluation logic is highly relevant to your project.

### Explanation Acceptance Logic

The core question is not:

`Does an explanation exist?`

but rather:

`Is the information flow revealed by the explanation perceived as acceptable?`

This means the value of explanation depends on its content, not merely on the existence of transparency.

### Business Outcome Logic

The paper ultimately asks:

`Does the explanation improve or reduce ad effectiveness?`

That is, the business value of explanation should be judged through outcomes such as:

- interest
- click intention
- purchase intention

### Moderation Logic

The paper also shows that:

`The effect of explanation is not fixed; it is moderated by platform trust.`

This is very important for business journals because it means artifact value depends on context rather than being universally positive.

## Relevance to the Present Project Design

This paper is highly relevant to your project, even though it is not a methods paper. Your project aims to build an advertising artifact that generates CTR predictions and explanations, and this paper directly shows that **advertising explanations are themselves a meaningful managerial research object**.

It helps your project in at least four ways:

1. **It proves that advertising explanations have business value**
   Your explanation component is not a decorative add-on, but potentially a core managerial feature.
2. **It warns that explanations are not always better when they are more explicit**
   If explanations reveal unacceptable logic, they may create backlash.
3. **It helps justify explanation usefulness**
   This is crucial for business/IS journals, because explanations must matter not only technically but managerially.
4. **It connects your project to the advertising transparency / trust literature**
   This significantly strengthens the business relevance of your research framing.

At the same time, this paper does not solve your technical mechanism design problems. It does not tell you:

- how to build an interpretable CTR predictor
- how to turn numerical errors into textual gradients
- how to optimize prompts

Thus, its role for your project is not technical-methodological, but rather as a **business justification source for advertising explanations and their managerial consequences**.
