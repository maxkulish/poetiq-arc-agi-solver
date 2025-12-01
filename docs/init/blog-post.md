```md
![](/images/logo.png)

get in touch

hello@poetiq.ai

join our team

[join@poetiq.ai](/careers/)

[![](/images/logo.png)](/)

Traversing the Frontier of Superintelligence

Poetiq is proud to announce a major milestone in AI reasoning. We have established a new state-of-the-art (SOTA) on the ARC-AGI-1 & 2 benchmarks, significantly advancing both the performance and the efficiency of the current AI systems.  
  
Poetiq’s approach of building intelligence **on top of any model** allowed us to integrate the newly released Gemini 3 and GPT-5.1 models within hours of their release to achieve the SOTA-results presented here.

  
November 20, 2025  
  

![]( assets/arc1captured.png)

  

Figure 1: Poetiq’s systems **redraw the Pareto frontier for cost versus performance**, delivering better results for lower cost at every level on the ARC-AGI-1 Public Eval Set. The comparisons presented here are on the Public Eval Set for all shown entries.

_Sources: (1) Public Eval results are from ARC-AGI official website [(public-set results)](https://arcprize.org/media/data/leaderboard/evaluations.json) (2) Pang’s Public Eval result was unavailable, so we reproduced it with his public code. (3) Berman’s Grok 4 Thinking result was unavailable, so we reproduced it with his public code. (4) Berman’s latest GPT-5 Pro result is on 100 of the 400 problems (see his xAI post)._

Poetiq (Gemini-3-a)Poetiq(Grok-4-Fast)Poetiq (Gemini-3-b)Poetiq (Gemini-3-c)Poetiq (Mix)$0.01$0.10$1$10$100$1K0%10%20%30%40%50%60%70%

ARC-AGI-2, Public EvalCost per task ($)Score (%)Average \[human\] test-takerGPT-5 ProGrok 4 (Fast Reasoning)Claude Sonnet 4.5 (Thinking 32K)Tiny Recursion Model (TRM)o4-mini (High)Claude Opus 4 (Thinking 8K)o3-Pro (Medium)IcecuberDeepseek R1GPT-5 Mini (Low)GPT-4.5Claude 3.7 (1K)Llama 4 ScoutGrok 3Gemini 3 ProGemini 3Deep Think (Preview)

![](assets/arc2captured.png)

  

Figure 2: Poetiq’s systems **redraw the Pareto frontier for cost versus performance**, delivering better results for lower cost at every level on the ARC-AGI-2 Public Eval Set. ARC-AGI-2 is similar to ARC-AGI-1 but with more complex tasks.

_Sources: Public Eval results are from ARC-AGI official website [(public-set results)](https://arcprize.org/media/data/leaderboard/evaluations.json)_

ARC-AGI-1 ARC-AGI-2

The Results

SOTA on the ARC-AGI Benchmark

Poetiq's systems **establish entirely new Pareto frontiers on both ARC-AGI-1 and ARC-AGI-2** (Figures 1 and 2), surpassing previous results and pushing the boundary for what is possible in cost-effective reasoning. We highlight a few interesting points, with emphasis given to our system’s configuration using models released in the last week; GPT-5.1 on November 13, 2025 and Gemini 3 on November 18, 2025.

*   **Poetiq (Mix)** used both the latest Gemini 3 and GPT-5.1 models. Compare with Gemini 3 Deep Think (Preview) which is significantly more expensive and has lower accuracy.
*   **Poetiq (Gemini-3-a,b,c)** are examples of how Poetiq can leverage _multiple LLMs to maximize performance at any target cost_. Poetiq discovered a straight-forward method to achieve pareto-optimal solutions across a wide swath of operating regimes by using multiple Gemini-3 calls to programmatically address these problems (both on ARC-AGI-1 and ARC-AGI-2). We have [open-sourced](https://github.com/poetiq-ai/poetiq-arc-agi-solver) the code for these systems.
*   **Poetiq (Grok-4-Fast)** emphasizes cost and is built on top of the Grok 4 Fast Reasoning model. In fact, it is **both cheaper and more accurate** than the underlying model’s reported numbers (see below for more details). It achieves accuracy rivaling models that are over two orders of magnitude more expensive.
*   **Poetiq (GPT-OSS-b)** is built on top of the open weights GPT-OSS-120B model and shows remarkable accuracy for less than 1 cent per problem (Figure 1).
*   **Poetiq (GPT-OSS-a)** is built on top of the GPT-OSS-120B low thinking model. This point is included to show system performance at extreme cost savings levels (Figure 1).

All these points (and more), while being capable separate systems in their own right, are produced by the underlying, flexible, Poetiq meta-system. One of the meta-system’s core strengths is automatically **selecting combinations of models and approaches**, even deciding when to write any code, and to which models to assign coding tasks. Our recursive, self-improving, system is LLM-agnostic and demonstrates its abilities with the state-of-the-art models.

Four observations:

1.  Note that Poetiq (Gemini-3-b) is saturating the performance on ARC-AGI-1; allowing larger computation expenditure, Poetiq (Gemini-3-c), did not provide benefit. However, on ARC-AGI-2, performance continues improving.
2.  **All** of Poetiq’s meta-system’s adaptation was done prior to the release of the Gemini 3 and GPT-5.1. Additionally, it was never shown problems from ARC-AGI-2. Further, for cost efficiency, the Poetiq system only relied on open-source models for adaptation. The results from that adaptation (the basis for all of the systems shown) were then used on both ARC-AGI-1 & 2, and also with over a dozen different underlying LLM models (shown below in Figure 3). This indicates substantial transference and generalization in the results of Poetiq’s system across model versions, families, and sizes. We have observed this type of generalization on other problems as well.
3.  Our ARC-AGI-2 results have exceeded the performance of the average human test-taker ([60%](https://arcprize.org/arc-agi/2/#:~:text=The%20average%20test%2Dtaker%20score%20was%2060%25)).
4.  As is described below (see final section), most of the underlying LLM models suffer varying degrees of performance degradation when moving from Public to Semi-Private evaluation on ARC-AGI-1. We expect the same. Most models have seen a smaller difference in performance on ARC-AGI-2 as the sets are more closely calibrated. All results reported here, for our work and everyone else’s, are on the public evaluation sets. [See our analysis below](#appendix).

Per Model Improvements

Using Poetiq’s Meta-System to Improve Performance of Popular Models

To further illustrate the benefits of Poetiq’s meta-system we apply our technique to popular recent models from Google DeepMind, OpenAI, Anthropic, and xAI. In each case, our system improves the accuracy while reducing the cost. _How is this even possible?_ Our systems achieve this because they make only a single attempt that uses fewer than two requests on average, rather than the **two** attempts that ARC-AGI permits. Figure 3 shows this on ARC-AGI-1 for 12 models from a variety of model families: GPT, Claude Haiku, Gemini, Grok 4, and GPT-OSS.

Gemini 2.5 Pro (Thinking 16K)PoetiqGemini 2.5 Pro (Thinking 16K)PoetiqGemini 3.0 ProGemini 3.0 ProPoetiqGemini 2.5 Pro (Thinking 16K)PoetiqGemini 3.0 ProGemini 3.0 ProPoetiqGemini 2.5 Pro (Thinking 16K)PoetiqGemini 3.0 ProGemini 3.0 Pro$0.23$0.37$0.5050%60%70%80%90%

Gemini 2.5 / 3Cost per task ($)Score (%)

![](assets/geminicaptured.png)

GPT-5.1 (Thinking High)GPT-5 (High)GPT-5 (Medium)GPT-5 (Low)PoetiqGPT-5 (High)PoetiqGPT-5 (Medium)PoetiqGPT-5 (Low)PoetiqGPT 5.1 (High)$0.05$0.37$0.6840%50%60%70%80%

GPT-5 / 5.1Cost per task ($)Score (%)

![](assets/gptcaptured.png)

PoetiqGPT-OSS 120B (Medium)PoetiqGPT-OSS 120B (Low)GPT-OSS 120B (Medium)GPT-OSS 120B (Low)$0$0.005$0.01$0.015$0.02$0.02510%20%30%40%50%

GPT-OSS-120BCost per task ($)Score (%)

![](assets/osscaptured.png)

Grok 4 (Fast Reasoning)Poetiq Grok4-fast$0.02$0.0440%50%60%70%80%

Grok 4 FastCost per task ($)Score (%)

![](assets/grokcaptured.png)

Claude Haiku 4.5 (Thinking 32K)Claude Haiku 4.5 (Thinking 16K)Claude Haiku 4.5 (Thinking 8K)PoetiqHaiku 4.5 (32k)PoetiqHaiku 4.5 (16k)PoetiqHaiku 4.5 (8k)$0.05$0.20$0.3540%50%60%70%

Claude Haiku 4.5Cost per task ($)Score (%)

![](assets/haikucaptured.png)

Figure 3: Poetiq’s systems improve existing models across model sizes and families (with open or closed weights), in terms of both accuracy and cost.

How We Did It

It’s LLMs all the way down. We used LLMs to _build, improve, and power_ the system. This flexible, powerful, and recursive architecture is what allowed our small team to rapidly achieve this suite of state-of-the-art results.

The specific configurations that we are **open-sourcing** were chosen to illustrate two key principles:

*   **The prompt is an interface, not the intelligence:** Our system engages in an iterative problem-solving loop. It doesn't just ask a single question; it uses the LLM to generate a potential solution (sometimes code as in this example), receives feedback, analyzes the feedback, and then uses the LLM again to refine it. This multi-step, self-improving process allows us to incrementally build and perfect the answer.
*   **Self-Auditing:** The system autonomously audits its own progress. It decides for itself when it has enough information and the solution is satisfactory, allowing it to terminate the process. This self-monitoring is critical for avoiding wasteful computation and minimizing costs.

We hope that our open-source code will help inspire new ideas and accelerate the path to superintelligence. The official code is available on [Github](https://github.com/poetiq-ai/poetiq-arc-agi-solver).

Why ARC-AGI?

ARC-AGI provides an ideal test bed to firmly establish one of our core tenets – LLMs contain much of humanity’s knowledge, but often struggle with tasks that rely on more complex reasoning. While the performance of an LLM heavily relies on the query, their inherent stochasticity makes knowledge extraction unreliable and makes the reasoning steps unpredictable. The challenge lies in discovering a reasoning strategy that can both find the necessary pieces of information and assemble them when they are discovered to intelligently determine what information is needed next.

At Poetiq, automating and optimizing this process is one of our key goals. We are building technology to optimize the extraction of this fragmented knowledge for complex reasoning tasks by not a priori dictating, but rather discovering, appropriate reasoning strategies that are both adaptive to the underlying LLM and work within specified real-world constraints (budgets, tokens, or compute). This will unlock the rapid progress in AI that the technology promises. Our system is designed to very quickly adapt to the specifics of the task and the model. ARC-AGI provides a concrete demonstration of this. For ARC-AGI, our system discovered an elegant method to improve performance across the entire frontier!

What's Next

At Poetiq, our core meta-system produces optimized agents to automate the extraction of knowledge for hard tasks that require complex reasoning. We optimize every part of the process: developing better strategies for determining what to ask, refining sequential chain-of-questions, and devising fundamental new methods for assembling the answers. ARC-AGI is just the beginning – we’ve tackled several other benchmarks as well, with similarly compelling results. Watch this space for more information on those, as well as other fun demonstrations of our capabilities.

Poetiq is a lean, deeply technical team of 6 researchers and engineers with a combined 53 years of experience from Google DeepMind. We're focused on solving the fundamental problems of AI reasoning and knowledge extraction in the presence of noise and uncertainty. Want to join us? [Check out our open positions](/careers/).

We're excited to share this result with the community and look forward to the discussion. Let us know your thoughts at

poetiq@poetiq.ai

.

Appendix

Expected Differences Between Public and Semi-Private Sets

The ARC-AGI-1 benchmark holds a set of problems as Semi-Private (100 problems). These problems are not available to the public and only ARC-AGI staff can run models on these. The Semi-Private set is significantly different from the public set and methods generally exhibit a difference in performance between the two. This can be seen from the official data on the public and private sets for some of the popular models, visualized in the figure below. For example, Gemini 2.5 Pro, Claude Haiku 4.5, and Grok 4 Fast Reasoning show significant drops, while GPT-5 (High) stands out for having a small drop.

Since Poetiq’s systems build on top of existing LLMs, we expect their accuracy to be affected by the drop in accuracy of the underlying model in the same way. When official evaluations are posted, we will provide updates!

95.2%\*75.0%86.2%\*72.8%77.1%\*70.2%77.0%\*63.7%73.8%\*66.7%73.7%\*65.7%65.9%\*47.7%62.9%\*48.5%58.1%\*37.0%55.9%\*Poetiq (Gemini-3-c)Gemini 3 ProGPT-5.1 (Thinking High)GPT-5 ProClaude Sonnet 4.5(Thinking 32K)Grok 4 (Thinking)GPT-5 (High)Claude Haiku 4.5(Thinking 32K)Grok 4 (Fast Reasoning)Gemini 2.5 Pro (Thinking32K)020406080100

ARC-AGI-1 Public vs Private Eval AccuraciesAccuracy (%)\* Public Evaluation Set

![](assets/publicprivatecaptured.png)

  

Figure 4: ARC-AGI Public-vs-Semi-Private Accuracies. The Poetiq models have the highest reported accuracy on the Public Eval set. All models show differences in accuracy between the Public and Semi-Private set.

_Source: Thanks to the ARC-AGI team for making both sets of results available on their website._

[![](/images/logo.png)](/)

get in touch

hello@poetiq.ai
```

=== Summary
The main idea of the blog post is that Poetiq has built an LLM-agnostic “meta-system” that significantly improves reasoning performance and cost-efficiency on the ARC-AGI benchmarks, effectively redrawing the Pareto frontier for many top models while remaining cheap and fast to adapt.[1]

## Core claim

Poetiq reports new state-of-the-art results on both ARC-AGI-1 and ARC-AGI-2, beating previous systems at almost every accuracy–cost tradeoff point and even exceeding average human performance on ARC-AGI-2’s public eval set. Their configurations using newly released models like GPT‑5.1 and Gemini 3 show higher accuracy at lower cost than the raw models themselves or competing systems such as Gemini 3 Deep Think.[1]

## What Poetiq actually is

Poetiq is presented as a “meta-system” that sits on top of arbitrary LLMs and automatically chooses which models to call, how many times, when to write and run code, and when to stop reasoning. It is recursive and self-improving, using LLMs not only to answer tasks but also to design and refine its own reasoning strategies, while remaining largely LLM-agnostic across families like GPT, Gemini, Claude, Grok, and open-weight models.[1]

## Key technical ideas

Two emphasized principles are: (1) “the prompt is an interface, not the intelligence” – the system runs multi-step loops of propose–test–refine instead of single prompts; and (2) self‑auditing – the system decides on its own when a solution is good enough to stop, which helps avoid wasted computation and lowers cost. A notable detail is that they typically use fewer than two requests per problem (vs. the two attempts allowed by ARC‑AGI), yet still improve both accuracy and cost for many base models.[1]

## Evidence and benchmarks

On ARC‑AGI‑1 and 2, Poetiq variants (e.g., Gemini‑3‑a/b/c, Grok‑4‑Fast, GPT‑OSS‑based systems, and “Mix” using GPT‑5.1 + Gemini 3) form new Pareto frontiers, meaning for a wide range of budgets they dominate alternatives in both score and price. They also show consistent per‑model gains: for Gemini, GPT‑5/5.1, Claude Haiku 4.5, Grok 4, and GPT‑OSS‑120B, running these models through Poetiq yields higher benchmark scores at lower average dollar cost per task than using the models directly.[1]

## Why it matters and what’s next

The authors argue ARC‑AGI is a good testbed for complex reasoning because LLMs already “contain” the knowledge but struggle to coordinate multi-step reasoning under constraints like tokens and budget. Poetiq’s goal is to automate discovery of such reasoning strategies across tasks and models, and they position these ARC‑AGI results as an early demonstration, with open-sourced code intended to spur further work and additional forthcoming benchmarks and demos.[1]

[1](https://poetiq.ai/posts/arcagi_announcement/)
