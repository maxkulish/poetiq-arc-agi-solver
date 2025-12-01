# LLM Integration Patterns

This document extracts reusable patterns from Poetiq's LLM integration that you can apply to your own projects.

## Pattern 1: Model Abstraction Layer

### The Problem
Different LLM providers have different APIs:
- OpenAI: `openai.ChatCompletion.create()`
- Anthropic: `anthropic.messages.create()`
- Google: `genai.generate_content()`

### The Solution
Use **LiteLLM** as a unified interface:

```python
from litellm import acompletion

# Same API for any model
response = await acompletion(
    model="gemini/gemini-3-pro-preview",  # or "openai/gpt-5"
    messages=[{"role": "user", "content": prompt}],
    temperature=1.0
)
```

### Why This Matters
- Switch models with one config change
- Test across model families without code changes
- Future-proof: new models just need a string change

## Pattern 2: Model-Specific Properties

### The Problem
Different models have different capabilities:
- Some support "extended thinking"
- Some have reasoning effort levels
- Some need different rate limits

### The Solution
Model property dictionary:

```python
props: dict[str, dict] = {
    "openai/gpt-5": {
        "reasoning_effort": "high"
    },
    "anthropic/claude-sonnet-4-5": {
        "thinking": {"type": "enabled", "budget_tokens": 32_000}
    },
    "gemini/gemini-2.5-pro": {
        "thinking": {"type": "enabled", "budget_tokens": 16_000}
    },
}

# Usage
extra_kwargs = props.get(model, {})
response = await acompletion(model=model, **extra_kwargs, ...)
```

### Why This Matters
- Model-specific optimizations without hardcoding
- Easy to add new models
- Thinking budgets can be tuned per model

## Pattern 3: Per-Model Rate Limiting

### The Problem
Different APIs have different rate limits:
- Gemini: 2 requests/second
- Most others: 1 request/second
- Exceeding limits → 429 errors → wasted retries

### The Solution
AsyncIO limiter per model:

```python
from asynciolimiter import Limiter

rate_limits: dict[str, float] = {
    "gemini/gemini-2.5-pro": 2.0,  # 2 req/sec
    "openai/gpt-5": 1.0,           # 1 req/sec
    # default: 1.0
}

limiters: dict[str, Limiter] = {}

def get_limiter(model: str) -> Limiter:
    if model not in limiters:
        rate = rate_limits.get(model, 1.0)
        limiters[model] = Limiter(rate)
    return limiters[model]

# Usage
async with get_limiter(model):
    response = await acompletion(...)
```

### Why This Matters
- Prevents hitting rate limits
- Different models can run at different speeds
- No wasted retries from 429 errors

## Pattern 4: Graceful Error Handling

### The Problem
API calls fail in many ways:
- Network timeouts
- Rate limit errors (429)
- Server errors (500, 503)
- Invalid responses

### The Solution
Retry with backoff, but smart categorization:

```python
from litellm.exceptions import (
    RateLimitError,
    InternalServerError,
    ServiceUnavailableError,
    APIConnectionError,
)

async def llm_with_retry(model, prompt, retries=3):
    for attempt in range(retries):
        try:
            return await acompletion(model=model, ...)
        except (RateLimitError, ServiceUnavailableError):
            # Transient errors: wait and retry (don't count against limit)
            await asyncio.sleep(5)
            continue  # Same attempt number
        except Exception as e:
            # Other errors: count against retry limit
            if attempt == retries - 1:
                return "Error: " + str(e)
            await asyncio.sleep(5)
```

### Key Insight
**Rate limits are not failures**—they're signals to slow down. Don't consume retries on rate limits.

## Pattern 5: Timeout Budget Management

### The Problem
Some problems are harder than others:
- Easy problem: solved in 1 request
- Hard problem: needs 10 requests
- Budget: don't spend forever on one problem

### The Solution
Track cumulative time and timeout counts:

```python
async def solve_with_budget(problem, max_time=600, max_timeouts=15):
    remaining_time = max_time
    remaining_timeouts = max_timeouts

    for iteration in range(max_iterations):
        start = time.time()

        try:
            response = await asyncio.wait_for(
                llm_call(problem),
                timeout=min(60, remaining_time)
            )
        except asyncio.TimeoutError:
            remaining_timeouts -= 1
            if remaining_timeouts <= 0:
                break  # Too many timeouts, give up

        elapsed = time.time() - start
        remaining_time -= elapsed

        if remaining_time <= 0:
            break  # Time budget exhausted

        if is_solution(response):
            return response

    return best_partial_solution
```

### Why This Matters
- Fair distribution of compute across problems
- Prevents one hard problem from blocking others
- Graceful degradation under resource pressure

## Pattern 6: Prompt Template System

### The Problem
Different scenarios need different prompts:
- Initial attempt vs. refinement with feedback
- Different emphasis for different models
- Consistent structure with variable content

### The Solution
Template-based prompts with placeholders:

```python
SOLVER_PROMPT = """
<Problem>
$$problem$$
</Problem>

<Instructions>
Analyze the examples and write a transform function.
</Instructions>
"""

FEEDBACK_PROMPT = """
<PreviousAttempts>
$$feedback$$
</PreviousAttempts>

Learn from these attempts and try again.
"""

def build_prompt(template: str, **fields: str) -> str:
    result = template
    for key, value in fields.items():
        result = result.replace(f"$${key}$$", value)
    return result

# Usage
prompt = build_prompt(SOLVER_PROMPT, problem=problem_description)
if previous_solutions:
    prompt += build_prompt(FEEDBACK_PROMPT, feedback=feedback_text)
```

### Why This Matters
- Consistent prompt structure
- Easy to A/B test different prompts
- Clear separation of template and content

## Pattern 7: Multiple Prompt Strategies

### The Problem
One prompt doesn't work best for all models or problems.

### The Solution
Multiple prompt variants with selection:

```python
SOLVER_PROMPT_1 = "..."  # Step-by-step analysis
SOLVER_PROMPT_2 = "..."  # Hypothesis testing emphasis
SOLVER_PROMPT_3 = "..."  # Concise code emphasis

# In config
config = {
    "solver_prompt": SOLVER_PROMPT_1,  # Selected variant
}

# Or per-model selection
model_prompts = {
    "gemini/*": SOLVER_PROMPT_2,
    "openai/*": SOLVER_PROMPT_1,
}
```

### Why This Matters
- Different models respond to different styles
- A/B testing reveals best combinations
- No single "best" prompt exists

## Pattern 8: Code Extraction from Markdown

### The Problem
LLMs often wrap code in markdown:
```
Here's the solution:
\`\`\`python
def transform(grid):
    return grid
\`\`\`
```

### The Solution
Regex extraction:

```python
import re

def extract_code(response: str) -> str | None:
    match = re.search(r"```python\s*(.*?)```", response, re.DOTALL)
    if match:
        return match.group(1).strip()
    return None
```

### Edge Cases to Handle
- Multiple code blocks (take last one)
- No markdown (treat whole response as code)
- Invalid Python (return None, let caller handle)

## Pattern 9: Structured Feedback Generation

### The Problem
Generic error messages don't help LLMs improve:
- "Wrong answer" → No guidance
- "Expected [[1,2]] got [[3,4]]" → Slightly better
- Visual diff with accuracy score → Much better

### The Solution
Rich, structured feedback:

```python
def build_feedback(predicted, expected) -> str:
    if predicted.shape != expected.shape:
        return f"Shape mismatch: expected {expected.shape}, got {predicted.shape}"

    diff_grid = []
    for i in range(len(predicted)):
        row = []
        for j in range(len(predicted[i])):
            if predicted[i][j] == expected[i][j]:
                row.append(str(predicted[i][j]))
            else:
                row.append(f"{predicted[i][j]}/{expected[i][j]}")
        diff_grid.append(" ".join(row))

    accuracy = (predicted == expected).mean()

    return f"""
Visualization (prediction/expected):
{chr(10).join(diff_grid)}

Accuracy: {accuracy:.2f} (0 = worst, 1 = best)
"""
```

### Why This Matters
- LLM can see exactly which cells are wrong
- Accuracy score provides gradient signal
- Structured format is easy to parse

## Summary: Integration Checklist

When building an LLM-powered system:

- [ ] Use abstraction layer (LiteLLM or similar)
- [ ] Configure model-specific properties
- [ ] Implement per-model rate limiting
- [ ] Handle errors gracefully (rate limits ≠ failures)
- [ ] Track time and timeout budgets
- [ ] Use template-based prompts
- [ ] Test multiple prompt variants
- [ ] Extract code cleanly from responses
- [ ] Generate structured, informative feedback

These patterns make your system **robust, flexible, and cost-effective**.

## Next Steps

- [06-lessons-learned.md](./06-lessons-learned.md) - Key insights for building similar systems
