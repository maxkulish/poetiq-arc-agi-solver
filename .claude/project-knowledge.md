# Project Knowledge Base

## Architecture Decisions
- Multi-model ensemble approach for improved accuracy
- Configurable solving strategies via `config.py`
- Separation of data loading, solving logic, and result aggregation

## Implementation Patterns
- Use LiteLLM for provider-agnostic model calls
- Rate limiting via asynciolimiter for API compliance
- Grid-based representation for ARC puzzles (NumPy arrays)

## Anti-Patterns to Avoid
- Synchronous API calls (blocks other requests)
- Hardcoded model names (use config instead)
- Ignoring rate limits (leads to API bans)

## Library Selections & Rationale
| Library | Purpose | Rationale |
|---------|---------|-----------|
| litellm | LLM API access | Unified interface, multiple providers |
| asynciolimiter | Rate limiting | Simple async rate limiting |
| numpy | Grid operations | Fast array manipulation |
| scipy | Pattern analysis | Distance metrics, clustering |
