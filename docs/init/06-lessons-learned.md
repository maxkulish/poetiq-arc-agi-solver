# Lessons Learned

This document distills the key insights from Poetiq's ARC-AGI Solver into actionable principles for building your own LLM-powered reasoning systems.

## Principle 1: The Prompt Is an Interface, Not the Intelligence

> "The prompt is an interface, not the intelligence."

### What This Means

**Common misconception**: Better prompts → Better AI
**Reality**: Better systems → Better results (prompts are just the API)

The intelligence in Poetiq doesn't come from clever prompting. It comes from:
- Iteration loops that learn from failures
- Feedback that guides improvement
- Voting that filters noise
- Early stopping that optimizes cost

### How to Apply This

Don't spend weeks optimizing prompts. Instead:
1. Build the feedback loop first
2. Make the system iterate and improve
3. Let the loop do the heavy lifting

```
Simple prompt + good loop > Complex prompt + single shot
```

## Principle 2: Verifiable Outputs Enable Iteration

### The Key Insight

Iteration only works if you can **verify** the output:
- Correct? → Stop
- Incorrect? → Explain why and try again

This is why Poetiq generates **code** instead of **direct answers**:
- Code can be executed and tested
- Tests give concrete pass/fail
- Failures can be analyzed for feedback

### How to Apply This

Design your system to produce verifiable artifacts:

| Domain | Verifiable Output |
|--------|-------------------|
| Code generation | Executable code + tests |
| Math problems | Solutions that can be checked |
| Data extraction | Structured JSON that can be validated |
| Writing | Drafts that can be scored/reviewed |

If you can't verify, you can't iterate.

## Principle 3: Soft Scoring Captures Progress

### The Problem with Binary Scoring

```
Attempt 1: 99% correct → FAIL
Attempt 2: 10% correct → FAIL
```

Both are "wrong" but one is much closer. Binary scoring loses this information.

### The Solution

Track **partial progress** with soft scores:

```
Attempt 1: 99% correct → Score: 0.99
Attempt 2: 10% correct → Score: 0.10
```

Now you can:
- Rank partial solutions
- Know when you're getting warmer
- Return "best effort" if no perfect solution

### How to Apply This

Define a continuous metric for your domain:
- Code: Test pass rate, coverage
- Text: Similarity score, keyword presence
- Classification: Confidence, probability

## Principle 4: Diversity Through Randomization

### The Insight

Same input + same model + same prompt = Same output

This is **bad** for exploration. Different starting points find different solutions.

### How Poetiq Creates Diversity

1. **Random seeds**: Each expert gets different randomness
2. **Example shuffling**: Training examples in different orders
3. **Solution sampling**: Probabilistic selection of feedback
4. **Temperature > 0**: LLM sampling varies

### How to Apply This

Add controlled randomness to your system:
```python
for expert_id in range(num_experts):
    seed = base_seed + expert_id * 100
    result = solve(problem, seed=seed)
```

More diversity → Better exploration → Higher chance of finding solution

## Principle 5: Agreement Indicates Confidence

### The Insight

If 5 independent experts produce the same answer, it's probably right.
If 5 experts produce 5 different answers, confidence is low.

### Voting Mechanism

```
Expert outputs:
[A, A, A, B, C]

Vote counts:
A: 3 votes (highest confidence)
B: 1 vote
C: 1 vote

→ Return A as primary answer
```

### How to Apply This

Run your solution multiple times:
- High agreement → Trust the answer
- Low agreement → Flag for review or return "uncertain"

This is ensemble learning applied to LLM systems.

## Principle 6: Early Exit Saves Cost

### The Math

Traditional: Always run N iterations
```
Cost = N × (cost per iteration)
```

With early exit: Stop when solved
```
Cost = average_iterations × (cost per iteration)
Average often < N because easy problems solve fast
```

### Poetiq's Results

> "Our systems achieve this because they make only a single attempt that uses fewer than two requests on average."

They run up to 10 iterations but average < 2 because easy problems exit early.

### How to Apply This

**Always check for success after each step:**
```python
for iteration in range(max_iterations):
    result = attempt(problem)
    if is_solved(result):
        return result  # Exit early!
    # Continue iterating...
```

Never iterate for the sake of iterating.

## Principle 7: Model-Agnostic Design Enables Adaptation

### The Insight

Poetiq achieved SOTA with:
- Gemini 3 (released Nov 18, 2025)
- GPT-5.1 (released Nov 13, 2025)

They integrated these models **within hours of release**. How?

### The Architecture

The system doesn't depend on any specific model:
- LiteLLM abstracts the API
- Config selects the model
- Same algorithm works across models

### How to Apply This

Build your system as:
```
[Your Logic] → [Abstraction Layer] → [Any LLM]
```

Not as:
```
[Your Logic hardcoded for GPT-4] → [Only GPT-4]
```

When better models release, you just change a config string.

## Principle 8: Feedback Is More Important Than Prompts

### The Traditional Focus
```
Spend 80% of time crafting the perfect prompt
Spend 20% on everything else
```

### The Poetiq Focus
```
Spend 20% on prompts (good enough works)
Spend 80% on feedback loops (this is where value is)
```

### Why Feedback Wins

A mediocre prompt with great feedback:
- Iteration 1: Wrong answer
- Iteration 2: Feedback explains error → Better answer
- Iteration 3: More feedback → Correct answer

A perfect prompt with no feedback:
- Iteration 1: Wrong answer
- Done. (No way to improve)

### How to Apply This

Invest in feedback generation:
- What went wrong?
- How wrong was it?
- What should change?

This is more valuable than prompt engineering.

## Principle 9: Generalization Comes from Simplicity

### The Surprising Finding

> "All of Poetiq's meta-system's adaptation was done prior to the release of the Gemini 3 and GPT-5.1... never shown problems from ARC-AGI-2... only relied on open-source models for adaptation."

Yet it worked on:
- New models (Gemini 3, GPT-5.1)
- New problems (ARC-AGI-2)
- Different model families (12+ models)

### Why This Works

The system doesn't learn model-specific tricks. It learns **general reasoning patterns**:
- Iterate until correct
- Learn from feedback
- Vote for confidence

These principles transfer across models and tasks.

### How to Apply This

Build general systems, not model-specific hacks:
- Avoid "prompt injection for GPT-4 specifically"
- Avoid "workaround for Claude's weakness"
- Build clean abstractions that work everywhere

## Summary: Actionable Principles

| Principle | Action |
|-----------|--------|
| Prompt ≠ intelligence | Build loops, not prompts |
| Verifiable outputs | Generate testable artifacts |
| Soft scoring | Track partial progress |
| Diversity | Randomize inputs/seeds |
| Voting | Confidence from agreement |
| Early exit | Stop when solved |
| Model-agnostic | Use abstraction layers |
| Feedback focus | Invest in error explanation |
| Generalization | Build simple, general systems |

## The Meta-Lesson

Poetiq's success comes not from any single technique, but from **combining simple ideas well**:

```
Simple iteration + Simple feedback + Simple voting = SOTA
```

You don't need breakthrough algorithms. You need:
1. A feedback loop
2. A way to measure progress
3. A way to aggregate results
4. Patience to iterate

These are engineering decisions, not research breakthroughs. And that's the point—**systematic engineering beats clever tricks**.

---

## Start Building

Now that you understand the architecture, start with the simplest version:

1. **Minimal loop**: Single expert, 3 iterations
2. **Basic feedback**: "Correct" or "Wrong + expected output"
3. **Simple scoring**: Pass/fail on test cases

Then iterate on your system the same way it iterates on problems:
- What went wrong?
- How can you improve?
- Try again.

The meta-system is recursive. Apply its own principles to improve itself.

Good luck!
