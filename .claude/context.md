# Project Context

## Overview
- **Project Name**: Poetiq ARC-AGI Solver
- **Technology Stack**: Python 3.11+, LiteLLM, NumPy, SciPy, AsyncIO
- **Primary Goal**: Achieve state-of-the-art performance on ARC-AGI benchmarks through advanced reasoning techniques

## Technical Constraints
- API rate limits for external LLM services (Gemini, OpenAI)
- Asynchronous execution required for efficient multi-model inference
- Must handle diverse abstract reasoning patterns

## Business Requirements
- Reproducible benchmark results
- Configurable model selection and parameters
- Support for both ARC-AGI-1 and ARC-AGI-2 datasets

## Technology Selection Rationale
- **LiteLLM**: Unified interface for multiple LLM providers
- **AsyncIO + asynciolimiter**: Rate-limited concurrent API calls
- **NumPy/SciPy**: Efficient grid manipulation and pattern analysis
