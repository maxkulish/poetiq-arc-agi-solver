# Project Overview

Poetiq ARC-AGI Solver - A record-breaking submission to the ARC-AGI-1 and ARC-AGI-2 benchmarks for abstract reasoning tasks.

## Technology Stack
- Python 3.11+
- LiteLLM for multi-model API access (Gemini, OpenAI)
- NumPy/SciPy for numerical computations
- AsyncIO for concurrent API calls

## Knowledge Management System
This project uses structured knowledge files in `.claude/` directory:
- `context.md`: Project background, objectives, constraints
- `project-knowledge.md`: Technical decisions and patterns
- `project-improvements.md`: Iteration history and lessons learned
- `common-patterns.md`: Reusable implementations
- `debug-log.md`: Critical debugging records

**Important**: Update relevant files when making significant decisions or discoveries.

## Quick Reference

### Running the Solver
```bash
source .venv/bin/activate
python main.py
```

### Environment Setup
Requires `.env` file with API keys:
```
GEMINI_API_KEY=...
OPENAI_API_KEY=...
```

### Configuration
- Modify constants in `main.py` for problem set and count
- Edit `config.py` to change model configurations
